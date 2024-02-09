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

### What should be tested in this project?

Given that this CLI is the entry point for the user to interact with Divekit, it is essential to test all commands.
Currently, there is only one command `patch`, but all commands should be tested with the following aspects in mind:

* ***Command Syntax***: Verify that the command syntax is correct
* ***Command Execution***: Ensure that executing the command produces the expected behavior or output
* ***Options and Arguments***: Test each option and argument individually to ensure they are processed correctly
  and test various combinations of options and arguments
* ***Error Handling***: Test how the command handles incorrect syntax, invalid options, or missing arguments

Additionally, testing the utility functions is necessary, as they are used throughout the entire project.
For that the following aspects should be considered:
* ***Code Paths***: Every possible path through the code should be tested, which should include "happy paths" 
(expected input and output) as well as "edge cases" (unexpected inputs and conditions).
* ***Error Conditions***: Check that the code handles error conditions correctly. For example, if a function is
supposed to handle an array of items, what happens when it’s given an empty array? What about an array with only one
item, or an array with the maximum number of items?
* ***State Changes***: Ensure that the code changes the state of the system as expected. For example,
modify a global variable and verify that the variable has been updated correctly.

### How should something be tested?

Commands should be tested with integration tests since they interact with the entire project. 
Integration tests are utilized to verify that all components of this project work together as expected 
in order to test the mentioned aspects.

To detect early bugs, utility functions should be tested with unit tests. Unit tests are used to
verify the behavior of specific functionalities in isolation. They ensure that individual units of code produce the
correct and expected output for various inputs.

### How are tests written in Go?

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
testing suites <a href="https://www.jetbrains.com/help/go/using-the-testify-toolkit.html" target="_blank">[4]</a>.
Primarily, the assertion package is used in this project for writing assertions more easily.

#### Test signature

To write unit or integration tests in Go, it is necessary to construct test functions following
a particular signature:

```go
func TestName(t *testing.T) {
// implementation
}
```

According to this test signature highlights following requirements [[1, p.40]](#references):

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
failure may pose a challenge
<a href="https://blog.jetbrains.com/go/2022/11/22/comprehensive-guide-to-testing-in-go/#writing-a-simple-unit-test"
target="_blank">[2]</a>.
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
each `testCase` is executed in a separate goroutine
<a href="https://golangdocs.com/goroutines-in-golang" target="_blank">[3]</a> with `t.Run()`.
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

##### Setup and teardown before and after a test

Setup and teardown are used to prepare the environment for tests and clean up after tests have been executed.
In Go the type `testing.M` from the testing package fulfills this purpose and is used as a parameter for the
`TestMain` function, which controls the setup and teardown of tests.

To use this function, it must be included within the package alongside the tests, as the scope for functions
is limited to the package in which it is defined. This implies that each package can only have one
`TestMain` function; consequently, it is called only when a test is executed within the package
<a href="https://medium.com/goingogo/why-use-testmain-for-testing-in-go-dafb52b406bc " target="_blank">[5]</a>.

The following example illustrates how it works [[1, p. 51]](#references):

```go
package main

func TestMain(m *testing.M) {
	// setup statements
	setup()

	// run the tests
	e := m.Run()

	// cleanup statements
	teardown()

	// report the exit code
	os.Exit(e)
}

func setup() {
	log.Println("Setting up.")
}
func teardown() {
	log.Println("Tearing down.")
}
```

`TestMain` runs before any tests are executed and defines the `setup` and `teardown` functions. The `Run` method
from `testing.M` is used to invoke the tests and returns an exit code that is used to report the success or failure
of the tests.

##### Setup and teardown before and after ***each*** test

In order to teardown after each test, the `t.Cleanup` function can be used provided by the testing package
<a href="https://blog.jetbrains.com/go/2022/11/22/comprehensive-guide-to-testing-in-go/#test-teardown-and-cleanup"
target="_blank">[2]</a>.
Since there is no mention to `setup` before each test, it can be assumed that the `setup` function is
called at the start of a test.

This example shows how this can be used:

```go
package main

func TestWithSetupAndCleanup(t *testing.T) {
	setup()

	t.Cleanup(func() {
		// cleanup logic
	})

	// more test code here
}

```

#### Write integration tests

Integration tests are used to verify the interaction between different components of a system. However, the mentioned
principles for writing unit tests also apply to integration tests. The only difference is that integration tests involve
a greater amount of code, as they encompass multiple components.

### How to run tests?

To run tests from the CLI, the `go test` command is used, which is part of the Go toolchain
<a href="https://www.thegowiki.com/wiki/Go_Toolchain" target="_blank">[6]</a>.
The list shows some examples of how to run tests:

* To run a specific test, the `-run` flag can be used. For example, to run the `TestDivide` test from
  the `divide_test.go` file, the following command can be used: `go test -run TestDivide`. Note that the argument for
  `-run` is a regular expression, so it is possible to run multiple tests at once.

* To run all tests in a package, run `go test <packageName>`. Note that the package name should include a relative path
  if
  the package is not in the working directory.

* To run all tests in a project, run `go test ./...` . The argument for test is a wildcard, matching all
  subdirectories; therefore, it is crucial for the working directory to be set to the root of the project
  to recursively run all tests.

Additionally, tests can be run from the IDE. For example, in GoLand, the IDE will automatically detect tests
and provide a gutter icon to run them
<a href="https://www.jetbrains.com/help/go/performing-tests.html" target="_blank">[7]</a>.

### How the command `patch` is tested?

#### Prerequisites

Before `patch` can be tested, it is necessary to do the following:

1. Replace the placeholders in the file `.env.example` and rename it to `.env`. If you have no api token, you can
   generate one <a href="https://git.archi-lab.io/profile/personal_access_tokens" target="_blank">here</a>.
2. Run the script `setup.ps1` as administrator. This script will install all necessary dependencies
   and initialize the ARS-, Repo-Editor- and Test-Origin-Repository.

#### Test data

To test `patch`, it was necessary to use a
<a href="https://git.archi-lab.io/staff/testing/divekit-origins/generic-origin/divekit-origin-test-repo" target="_blank">
test origin repository</a> as test data. In this context the test origin repository is a repository that contains
all the necessary files and configurations from ST1 to test different scenarios.

Additionally, a <a href="https://git.archi-lab.io/staff/thomas-testgroup" target="_blank">test group</a> was created
to test if the Repo-Editor-repository actually pushes the generated files to remote repositories.
Currently, the test group contains the following repositories:

```
coderepos:
    ST1_Test_group_8063661e-3603-4b84-b780-aa5ff1c3fe7d
    ST1_Test_group_86bd537d-9995-4c92-a6f4-bec97eeb7c67
    ST1_Test_group_8754b8cb-5bc6-4593-9cb8-7c84df266f59
    
testrepos:
    ST1_Test_tests_group_446e3369-ed35-473e-b825-9cc0aecd6ba3
    ST1_Test_tests_group_9672285a-67b0-4f2e-830c-72925ba8c76e 
```

#### Structure of a test case

`patch` is tested with a table-driven test, which is located in the file `patch_test.go`.

The following example shows the structure of a test case:

```go
package patch

func TestPatch(t *testing.T) {
	testCases := []struct {
		name           string
		arguments      PatchArguments  // input
		generatedFiles []GeneratedFile // expected
		error          error           // expected
	}{
		{
			"example test case",
			PatchArguments{
				dryRun:       true | false,
				logLevel:     "[none] | info | debug | warning | error",
				originRepo:   "path_to_test_origin_repo",
				home:         "[none] | path_to_repositories",
				distribution: "[none] | code | test",
				patchFiles:   []string{"patch_file_name"},
			},
			[]GeneratedFile{
				{
					RepoName:    "repository_name",
					RelFilePath: "path_to_the_generated_file",
					Include:     []string{"should_be_found_in_the_generated_file"},
					Exclude:     []string{"should_not_be_found_in_the_generated_file"},
				},
			},
			error: nil | errorType,
		},
	}

	// [run test cases]
}

```

The `name` field is the name of the test case and is used to identify the test case in case of an error.

The struct `PatchArguments` contains all the necessary arguments to run the `patch` command:

* `dryRun`: If true, generated files will not be pushed to a remote repository.
* `logLevel`: The log level of the command.
* `originRepo`: The path to the test origin repository.
* `home`: The path to the divekit repositories.
* `distribution`: The distribution to patch.
* `patchFiles`: The patch files to apply.

The struct `GeneratedFile` is the expected result of the `patch` command and contains the following properties:

* `RepoName`: The name of the generated repository.
* `RelFilePath`: The relative file path of the generated file.
* `Include`: The strings that should be found in the generated file.
* `Exclude`: The strings that should not be found in the generated file.

The `error` field is the expected error of the `patch` command. It can be `nil` when no error is expected or
contain a specific error type if an error is expected.

#### Process of a test case

The following code snippet shows how test cases are processed:

```go
package patch

func TestPatch(t *testing.T) {
	// [define test cases]

	for _, testCase := range testCases {
		t.Run(testCase.name, func(t *testing.T) {
			generatedFiles := testCase.generatedFiles
			dryRunFlag := testCase.arguments.dryRun
			distributionFlag := testCase.arguments.distribution

			deleteFilesFromRepositories(t, generatedFiles, dryRunFlag)       // Step 1
			_, err := executePatch(testCase.arguments)                       // Step 2

			checkErrorType(t, testCase.error, err) // Step 3
			if err == nil {
				matchedFiles := matchGeneratedFiles(t, generatedFiles, distributionFlag) // Step 4
				checkFileContent(t, matchedFiles)                                        // Step 5
				checkPushedFiles(t, matchedFiles, dryRunFlag)                            // Step 6
			}
		})
	}
}
```

Each test case runs the following sequence of steps:

1. `deleteFilesFromRepositories` deletes the specified files from their respective repositories. Prior to testing,
   it is necessary to delete these files to ensure that they are actually pushed to the repositories,
   given that they are initially included in the repositories.

2. `executePatch` executes the patch command with the given arguments and return the output and the error.

3. `checkErrorType` checks if the expected error type matches with the actual error type.

4. `matchGeneratedFiles` checks if the found file paths match with the expected files and
   returns a slice of `MatchedFiles`. Each `MatchedFile` contains various information about a file,
   which is needed to check its correctness.

5. `checkFileContent` checks if the content of the files is correct.

6. `checkPushedFiles` checks if the generated files have been pushed correctly to the corresponding repositories.


## How is this project released?

### What is GoReleaser?

GoReleaser is a tool designed to streamline the process of releasing Go applications. It automates various
tasks associated with the release process, such as compiling binaries, creating release notes, updating
version information, and packaging the application for multiple platforms. GoReleaser aims to simplify the often
complex and manual steps involved in releasing software written in the Go programming language. By using GoReleaser,
developers can save time and ensure a consistent and reliable release process for their Go projects
<a href="https://goreleaser.com/" target="_blank">[8]</a>.

### How is GoReleaser used in this project?

GoReleaser is used to release the CLI binary with the ARS- and Repo-Editor-repository for windows,
but can be extended to other platforms as well. Additionally, the release can be published to any package
manager, such as Scoop, Chocolatey, Homebrew, etc. Currently, it is released to Scoop only, but it is possible to
add more package managers if needed.

To achieve this, GoReleaser provides a simple configuration file that can be used to
configure the release process. The configuration file for this project is located in the root directory and is named
`.goreleaser.yaml`.

The following code snippet shows how the repositories gets included in the release:

```yaml
before:
  hooks:
    - powershell "if (test-path "./repositories") { remove-item  -path "./repositories" -recurse -force }"
    - powershell new-item  -path "./repositories" -type directory
    - powershell ./scripts/setupRepositories.ps1 -destination $PWD/repositories

archives:
  - name_template: "{{ .ProjectName }}_{{ .Version }}_{{ .Os }}_{{ .Arch }}"
    format: zip
    files:
      - src: ./repositories/divekit-automated-repo-setup
        dst: divekit-automated-repo-setup
      - src: ./repositories/divekit-repo-editor
        dst: divekit-repo-editor
```

The `before` section contains a list of hooks that are executed before goReleaser starts the release process.
In this case, the `setupRepositories.ps1` script is executed to clone and setup the ARS- and Repo-Editor-repository
in the `repositories` directory.

The `archives` section defines how the release is packaged and which files are added.
In this case, the CLI binary, ARS- and Repo-Editor-repository are added to the release as zip archives.

The next snippet shows how the release is published to Scoop:

```yaml
scoops:
  - name: divekit
    url_template: "https://github.com/divekit/divekit-cli/releases/download/{{ .Tag }}/{{ .ArtifactName }}"
    homepage: "https://divekit.github.io"
    description: ""
    license: MIT
    skip_upload: true
    depends: [ "umlet", "oraclejre8", "nodejs" ]
```

The `scoops` section contains a list of Scoop properties that defines how the installation with Scoop is handled.
At the end of the release process, a json manifest with these properties plus some manually added properties
gets generated and pushed to the divekit-cli repository. Most properties are self-explanatory,
but some are worth mentioning:

* The `skip_upload` property is set to true to avoid pushing the generated file right away, because some
  properties must be added manually and then pushed. The reason for that is that GoReleaser does not support
  all scoop properties at the moment, such as an environment variable.

* The `depends` property defines which dependencies are required to run the CLI and installs them automatically
  if they are not already installed.

### Why was Scoop chosen for the release of this project?

Scoop is a command-line installer for Windows that is used to install applications from the command line.
It is similar to other package managers, such as Homebrew, but is designed specifically for Windows.

Compared to other Windows package managers such as Chocolatey, Scoop also provides a wide range of apps.
However, it stands out for its simplicity in releasing new versions of applications, because Scoop just reads plain
JSON manifests that describe how to install an application
<a href="https://github.com/ScoopInstaller/Scoop/wiki/Chocolatey-and-Winget-Comparison" target="_blank">[10]</a>.
Additionally, the release process is much faster, because a manual review process is not involved,
which can take several days when using chocolatey.

### What steps are taken to release a new version?

In order to release a new version a workflow file for GitHub is required, which `release_and_test.yml` fulfills.
This gets executed in the CI/CD pipeline, when a commit gets pushed or pulled into the main branch:

1. Cache or restore previously installed dependencies to expedite the installation process.

2. Initialize the project with the `setup.ps1` script. This script installs all necessary dependencies
   and initializes the ARS-, Repo-Editor- and Test-Origin-Repository.

3. Run the tests and if the tests fail, the pipeline gets aborted.

4. Determine the version number automatically based on the commit history of the repository.
   The advantage of this approach is that the version number gets incremented automatically, which reduces the
   risk of human error <a href="https://github.com/PaulHatch/semantic-version#git-based-semantic-versioning"
   target="_blank">[9]</a>.

5. Create a version tag with the determined version number and push it to the repository.

6. Attach the tag to GoReleaser and release the CLI binary with the ARS- and Repo-Editor-repository for windows.

7. Manually add the missing properties to the generated Scoop manifest and push it to the repository.

## How to install the CLI?

Currently, the CLI is only available for Windows. To install the CLI, follow these steps:

1. Open a PowerShell window as user.
2. Allow scripts to be executed: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`
3. Install scoop: `iwr -useb get.scoop.sh | iex`
4. Add buckets with the following commands to find the required apps:
    * `scoop bucket add divekit https://github.com/divekit/divekit-cli`
    * `scoop bucket add extras`
    * `scoop bucket add java`
5. To install Divekit along with its dependencies, use: `scoop install divekit`

## References

[1]
A. Simion,
<a href="https://www.amazon.de/Test-Driven-Development-practical-idiomatic-real-world/dp/1803247878" target="_blank">
Test-Driven Development in Go</a>
Packt Publishing Ltd,
2023

[2]
<a href="https://blog.jetbrains.com/go/2022/11/22/comprehensive-guide-to-testing-in-go" target="_blank">
"Comprehensive Guide to Testing in Go | The GoLand Blog,"</a>
The JetBrains Blog
(accessed Jan. 29, 2024).

[3]
<a href="https://golangdocs.com/goroutines-in-golang" target="_blank">"Goroutines in Golang - Golang Docs,"</a>
(accessed Jan. 29, 2024).

[4]
<a href="https://www.jetbrains.com/help/go/using-the-testify-toolkit.html" target="_blank">"Using the Testify toolkit |
GoLand,"</a>
GoLand Help.
(accessed Jan. 29, 2024).

[5]
<a href="https://medium.com/goingogo/why-use-testmain-for-testing-in-go-dafb52b406bc" target="_blank">"Why use TestMain
for testing in Go?"</a>
(accessed Jan. 29, 2024).

[6]
<a href="https://www.thegowiki.com/wiki/Go_Toolchain" target="_blank">"Go Toolchain - Go Wiki"</a>
(accessed Jan. 29, 2024).

[7]
<a href="https://www.jetbrains.com/help/go/performing-tests.html" target="_blank">"Run tests | GoLand,"</a>
GoLand Help.
(accessed Jan. 29, 2024).

[8]
<a href="https://goreleaser.com/" target="_blank">"GoReleaser"</a>
(accessed Jan. 29, 2024).

[9]
<a href="https://github.com/PaulHatch/semantic-version#git-based-semantic-versioning" target="_blank">
"PaulHatch/semantic-version"</a>
(accessed Jan. 29, 2024).

[10]
<a href="https://github.com/ScoopInstaller/Scoop/wiki/Chocolatey-and-Winget-Comparison" target="_blank">"Chocolatey and
Winget Comparison"</a>
(accessed Jan. 29, 2024).
