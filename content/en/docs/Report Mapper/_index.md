---
title: "Report Mapper"
linkTitle: "Report Mapper"
weight: 2
description: >
  The report-mapper is an integral part of the GitLab-CI/CD-pipeline for all test repositories.
  The report-mapper ensures that various inputs from Surefire-Reports, PMD and Checkstyle codestyle checks are converted
  into a uniform format.
  
  The produced result is an XML file and subsequently used by the [report-visualizer](TODO) for a readable output.
resources:
---

## Architecture overview
![](architecture.png)


## Getting started

### Install
Clone the repository and install everything necessary:

```bash
# HTTP
git clone https://github.com/divekit/divekit-report-mapper.git
# SSH
git clone git@github.com:divekit/divekit-report-mapper.git

cd ./divekit-report-mapper

npm ci # install all dependencies

npm test # check that everything works as intended
```

### Provide input data

Surefire-Reports should be placed here: `target/surefire-reports/` 

PMD and Checkstyle results should be placed here: `target/`

_You can find some examples for valid and invalid inputs in the tests: `src/test/resources`_

```bash
npm run dev
```

### Understand the Output

The result from the divekit-report-mapper is a XML-File (`target/unified.xml`).
It contains the result of all inputs sources in a uniform format. This also includes errors if some or all inputs
provided invalid or unexpected data.

Example with only valid data:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<suites>
  <testsuite xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation=""
             name="E2CleanCodeSolidManualTest" failures="0" type="JUnit" status="failed">
    <testcase name="testCleanCodeAndSolidReview" status="failed" hidden="false">
      <error message="-%20break%20pipeline%20%3C--%0A" type="java.lang.Exception"><![CDATA[java.lang.Exception:
                - break pipeline <--
            	at thkoeln.st.st2praktikum.exercise1.E2CleanCodeSolidManualTest.testCleanCodeAndSolidReview(E2CleanCodeSolidManualTest.java:13)]]>
      </error>
    </testcase>
  </testsuite>

  <testsuite name="Clean-Code-Principles by PMD" status="failed" type="CleanCode">
    <testcase name="Keep it simple, stupid" status="passed" hidden="false"></testcase>
    <testcase name="Meaningful names" status="failed" hidden="false">
      <error type="LocalVariableNamingConventions" location="Line: 90 - 90 Column: 13 - 22"
             file="C:\work\gitlab-repos\ST2MS0_tests_group_d5535b06-ae29-4668-8ad9-bd23b4cc5218\src\main\java\thkoeln\st\st2praktikum\bad_stuff\Robot.java"
             message="The local variable name &apos;snake_case&apos; doesn&apos;t match &apos;[a-z][a-zA-Z0-9]*&apos;"></error>
    </testcase>
  </testsuite>

</suites>
```

For further examples see tests `src/test/resources`.

### Deployment
All pipeline script normally use the latest version from
[npmjs.com](https://www.npmjs.com/package/@divekit/report-mapper).

The repository is setup with three different GitHub Actions workflows wich trigger 
on pushes to the branches `main`, `stage` and `development`.

- main: Build, run tests and publish new npm package. Fails if:
  build/tests fail, the version is a beta version or
  the version has not been updated
- stage: same as main but the version must be a beta-version and the package is
  tagged as beta
- development: Build and run all tests

## Version
Complete packages available at [npmjs.com](https://www.npmjs.com/package/@divekit/report-mapper).
The versioning is mostly based on [semantic versioning](https://semver.org/lang/de/).

### 1.1.1

### 1.0.9-beta.1

- update scripts according to new report-visualizer naming

### 1.0.8

- Parameters processing added, which allow a restriction of the used mappers
- Error handling: If a mapper does not deliver a valid result, an error is indicated in the unified.xml. 
