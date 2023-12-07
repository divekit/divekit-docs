---
title: "cli"
linkTitle: "cli"
weight: 2
description: >
  CLI to simplify and orchestrate the usage of multiple divekit tools.
---

{{% pageinfo %}}
The following documentation is more a concept than a description of the finished application.
{{% /pageinfo %}}

## Getting started

### Prerequisites

- `npm`
- `python`
- `pip`

### usage

```bash
npm i @divekit/cli -g # install

mkdir st-2042 # create empty project directory
cd st-2042

divekit init -g 152 -c 14 # initializes in GitLab Dir with groupId 152 and with config base from project in GitLab dir 14
```

### file structure

```yml
st-2042
├── .divekit # all configs, tools etc. needed for this project and the divekit-cli
|   ├── config
|   |    ├── automated-repo-setup
|   |    ├── ...
|   |    └── basic.yml # name, milestone data, ...
|   └── tools
|        ├── access-manager
|        └── ...
├── milestone1
|   ├── config.yml # milestone specific domain-config
|   └── origin-repo
|       └── ...
├── milestone2
└── ...
```

#### basic.yml example

```yml
name: ST1-2022
rootStaffGroupId: 42
rootStudentGroupId: 43
overviewGroupId: 44
milestones:
  m1:
    groupId: 54
    start: 20.08.2023
    end: 04.09.2023
    codeRepoGroupId: 142
    testRepoGroupId: 143
  m2:
    groupId: 67
    start: 07.09.2023
    end: 27.09.2023
    codeRepoGroupId: 152
    testRepoGroupId: 153
  # ...

```

## Functionality

### `divekit init`

Initialize a new divekit project, done once per semester and module. Must be used in an empty directory otherwise it
only checks and installs existing dependencies needed for *tools*. An example for a generated file structure can be seen
above.

#### Options

* `-w / --wizard` install via wizard which asks and explains all parameters and gives options if available
* `-g / --groupId` groupId for the corresponding GitLab directory (should be empty as well)
* `-n / --name` set a name for the project (Defaults to name of parent directory)
* `-c / --config <groupId>` copy a previous used config from given project (*Nice to have*: also works on old projects)

#### errors

* exits if node, python or pip are not available

#### Example usage

Creating a new project with the help of the wizard.

```bash
mkdir st1-2023
divekit init --wizard
$ "Please enter the GitLab GroupId where all Staff content will be managed (should be empty):"
$ 150 # <user_input>
$ "Please enter the GitLab GroupId where all student repositories will be published:"
$ 151 # <user_input>
$ "Please enter a name for the project (default: st1-2023)"
$ # <user_input>
$ "Do you want to use a already existing configuration, then please enter a groupId for the corresponding project otherwise a basic config will be initialized"
$ 42 # <user_input>
$ "Divekit initialized successfully"
# Result: Directory .divekit created locally and pushed to GitLab
```

### `divekit milestone`

manage the creation, testing, publication and more for a milestone.

*if not otherwise configured naming, config etc. will be copied from the last milestone*

#### Options

* `-w / --wizard` create the next milestone via wizard which asks and explains all parameters and gives options if
  available (for more information see the example below)
* `-n / --name <string>` set a custom name for the milestone (default to milestone<Id>)
* `-t / --template <groupId>` create the next milestone based of an already existing origin repo
* `-f / --fresh` while creating a new milestone ignore already existing and their configuration
* `-i / --id <number>` specify milestoneId (default start with 1 or last existing milestone +1)
* `-l / --local <number>` create a given number student-repos locally in demo-folder
* `-r / --random <none || all | roundrobin>` configure randomization. None at all, Everything (like normal student repo
  generation) or roundrobin which tries to create uniform distribution of all variations
* `-p / --publish` creates and publishes all repos for students based on origin repo
* `-d / --demo <repoCount>` create a given count of test repositories

#### Example usage

Create the first milestone with the help of the wizard

```bash
divekit milestone --wizard
$ "creating the first milestone of this project"
$ "please enter a number for the first milestone (default: 1)"
$ # <user_input>
$ "Do you want to use a already existing milestone as template?"
$ "Please enter the groupId otherwise skip with Enter"
$ 47 # <user_input>
$ "Milestone created - pls configure the origin repository"
# Result:
# - milestone1 directory created based on template repo
# - updated configs with new milestone and push everything to GitLab
# - also created directories for code/test repositories in GitLab.
```

Create further milestone with the help of the wizard

```bash
divekit milestone --wizard
$ "creating a milestone for st1-2023"
$ "please number for the milestone (default: 2)"
$ # <user_input>
$ "Do you want to use a already existing milestone as template?"
$ "Please enter the groupId otherwise the milestone2 will be based on milestone1"
$ # <user_input>
$ "Milestone created - pls configure the origin repository"
# Result:
# - milestone2 directory created based on milestone1 repo
# - updated configs with new milestone and push everything to GitLab
# - also created directories for code/test repositories for milestone2 in GitLab.
```

### `divekit patch`

Edit the current or given milestone.

#### Options

* `-m / --milestone <numer>` number of milestone you want to edit (default: latest existing milestone)
* `-g / --g <[groupId]>` list of groupIds where to publish edits (default: student repositories of the milestone)
* `-f / --files <[names]>` list of files to edit/create
* `-c / --commit <msg>`: custom commit msg (default: "fix 42")
* `-d / --debug`: sets loglevel to debug

### Use Cases and other ideas - which should be considered

- create repos for latecomers, e.g. because they passed the previous milestone after a discussion or forgot to register.
- divide the config structure between technical and domain configuration

## How to test a CLI in Go?

### Introduction

This chapter covers the clarification of various aspects to testing this CLI. This includes
determining what should be tested, the methodology of the tests, the form of test implementation, and
the origin of test data based on the Test Origin Repository.
Additionally, the chapter includes general practices for writing tests in Go, to be able to test CLI
applications developed in Go and Cobra.

### What should be tested in this project?

### How should something be tested?

### How are tests written?

#### Prerequisites

It's worth mentioning that the following packages are utilized in this project for testing code.

##### The testing package

The standard library provides the testing package, which is required to support testing in Go. It offers different
types from the testing library [[1, pp. 37-38]](#references):

* `testing.T`: To interact with the test runner, all tests must use this type. It contains a method for
  declaring failing tests, skipping tests, and running tests in parallel.

* `testing.B`: Similar to the test runner, this type is a benchmark runner. It shares the same methods for failing
  tests, skipping tests and running benchmarks concurrently. Benchmarks are generally used to determine performance
  of written code.

* `testing.F`: This type generates a randomized seed for the testing target and collaborates with the `testing.T` type
  to provide test-running functionality. Fuzz tests are unique tests that generate random inputs to discover
  edge cases and identify bugs in written code.

* `testing.M`: This type allows for additional setup or teardown before or after tests are executed.

##### The testify toolkit

The testify toolkit provides several packages to work with assertions, mock objects and
testing suites [[4]](https://www.jetbrains.com/help/go/using-the-testify-toolkit.html). Primarily, the assertion
package is used in this project for writing assertions more easily.

#### Test signature

To write unit or integration tests in Go, it is necessary to construct test functions following
a particular signature:

```go
func TestName(t *testing.T) {
// implementation
}
```

According to [[1, p. 40]](#references) this test signature highlights following requirements:

* Exported functions with names starting with "Test" are considered tests.
* Test names can have an additional suffix that specifies what the test is covering. The suffix must
  also begin with a capital letter. In this case, "Name" is the specified suffix.
* Tests are required to accept a single parameter of the `*testing.T` type.
* Tests should not include a return type.

#### Unit tests

Unit tests are small, fast tests that verify the behavior of specific functionalities in isolation.
They ensure that individual units of code produce the correct and expected output for various inputs.

To illustrate unit tests, a new file named `divide.go` is generated with the following code:

```go
package main

func Divide(a, b int) float64 {
	return float64(a) / float64(b)
}
```

By convention tests are located in the same package as the function being tested.
It's important that all test files must end with `_test.go` suffix to get detected by the test runner.

Accordingly `divide_test.go` is also created within the main package:

```go
package main

import (
	"github.com/stretchr/testify/assert"
	"testing"
)

func TestDivide(t *testing.T) {
	// Arrange
	should, a, b := 2.5, 5, 2
	// Act
	is := divide(a, b)
	// Assert
	assert.Equal(t, should, is, "Got %v, want %v", is, should)
}
```

Writing unit or integration tests in the Arrange-Act-Assert (AAA) pattern is a common practice. This pattern establishes
a standard
for writing and reading tests, reducing the cognitive load for both new and existing team members and enhancing
the maintainability of the code base [[1, p. 14]](#references).

In this instance, the test is formulated as follows:

* ***Arrange***: All preconditions and inputs get set up.

* ***Act***: The ***Act*** step executes the actions outlined in the test scenario, with the specific actions depending
  on the type of test. In this instance, it calls the Add function and utilizes the inputs from the ***Arrange*** step.

* ***Assert***: During this step, the precondition from the ***Arrange*** step is compared with the output.
  If the output does not match the precondition, the test is considered failed, and an error message is displayed.

It's worth noting that the ***Act*** and ***Assert*** steps can be iterated as many times as needed, proving beneficial,
particularly in the context of table-driven tests.

#### Table-driven tests for unit and integration tests

To cover all test cases it is required to call ***Act*** and ***Assert*** multiple times. It would be possible to write
one test per case, but this would lead to a lot of duplication, reducing the readability. An alternative approach is to
invoke the same test function several times. However, in case of a test failure, pinpointing the exact point of
failure may pose a
challenge [[2]](https://blog.jetbrains.com/go/2022/11/22/comprehensive-guide-to-testing-in-go/#writing-a-simple-unit-test).
Instead, in the table-driven approach, preconditions and inputs are structured as a table in the ***Arrange*** step.

As a consequence `divide_test.go` gets adjusted in the following steps [[1, pp. 104-109]](#references):

##### Step 1 - Create a structure for test cases

In the first step a custom type is declared within the test function. As an alternative the structure could be declared
outside the scope of the test function. The purpose of this structure is to hold the inputs and expected preconditions
of the test case.

The test cases for the previously mentioned `Divide` function could look like this:

```go
package main

import (
	"math"
	"testing"
)

func TestDivide(t *testing.T) {
	// Arrange
	testCases := []struct {
		name     string  // test case name
		dividend int     // input
		divisor  int     // input
		quotient float64 // expected
	}{
		{"Regular division", 5, 2, 2.5},
		{"Divide with negative numbers", 5, -2, -2.5},
		{"Divide by 0", 5, 0, math.Inf(1)},
	}
}

```

The `struct` type wraps `name`, `dividend`, `divisor` and `quotient`. `name` describes the purpose of a test case
and can be used to identify a test case, in case an error occurs.

##### Step 2 - Executing each test and assert it

Each test case from the table will be executed as a subtest. To achieve this, the `testCases` are iterated over and
each `testCase` is executed in a separate goroutine [[3]](https://golangdocs.com/goroutines-in-golang) with `t.Run()`.
The purpose of this is to individually fail tests without concerns about disrupting other tests.

Within `t.Run()`, the ***Act*** and ***Assert*** steps get performed:

```go
package main

import (
	"github.com/stretchr/testify/assert"
	"math"
	"testing"
)

func TestDivide(t *testing.T) {
	// Arrange
	testCases := []struct {
		name     string  // test case name
		dividend int     // input
		divisor  int     // input
		quotient float64 // expected
	}{
		{"Regular division", 5, 2, 2.5},
		{"Divide with negative numbers", 5, -2, -2.5},
		{"Divide by 0", 5, 0, math.Inf(1)},
	}

	for _, testCase := range testCases {
		t.Run(testCase.name, func(t *testing.T) {
			// Act
			quotient := Divide(testCase.dividend, testCase.divisor)
			// Assert
			assert.Equal(t, testCase.quotient, quotient)
		})
	}
}

```

#### Setup and teardown

#### Write integration tests

### How to run tests?

### From where does the test data originate?

## CI-pipeline

## References

[1]
A. Simion,
[Test-Driven Development in Go](https://www.amazon.de/Test-Driven-Development-practical-idiomatic-real-world/dp/1803247878),
Packt Publishing Ltd,
2023

[2]
"Comprehensive Guide to Testing in Go | The GoLand Blog,"
The JetBrains Blog,
Nov. 22, 2022.
https://blog.jetbrains.com/go/2022/11/22/comprehensive-guide-to-testing-in-go/#writing-a-simple-unit-test
(accessed Nov. 8, 2023).

[3]
S. Chakraborty,
“Goroutines in Golang - Golang Docs,“
Mar. 13, 2020.
https://golangdocs.com/goroutines-in-golang
(accessed Nov. 8, 2023).

[4]
“Using the Testify toolkit | GoLand,”
GoLand Help.
https://www.jetbrains.com/help/go/using-the-testify-toolkit.html
(accessed Nov. 10, 2023).
