
---
title: "Documentation"
linkTitle: "Documentation"
weight: 20
menu:
  main:
    weight: 20
---

{{% pageinfo %}}
The documentation is in a very early stage and some parts might be outdated.
{{% /pageinfo %}}

# Divekit

The tool *Divekit* implements a set of features which are used to automatically provide programming exercises in form of repositories on the platform Gitlab. Optionally exercises can be individualized using the integrated mechanism for individualization. Besides the Divekit there are additional tools which extend the toolchain by building on top of Divekit functionality. In the following said tools are listed:

| Tool                                                                                            | Function                                                                                                                      | Dependencies                      |
|-------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| [Test Library](https://github.com/divekit/divekit-test-library)                                 | Library which contains a set of generic tests for different testing purposes                                                  | Java (Kotlin)                     |
| [Report Mapper](https://github.com/divekit/divekit-report-mapper)                               | Generates a unified.xml based different sources (currently supported: maven surefire reports, maven pmd report, checkstyle)   | Gitlab, Java (Kotlin), Maven      |
| [Report Visualizer](https://github.com/divekit/divekit-report-visualizer)                       | Generates a test page which is hosted by Gitlab Pages based mapper results                                                    | Gitlab, Java (Kotlin), Maven      |
| [Divekit Language Server](https://github.com/divekit/divekit-language-server)                   | Supports Code-Completion for Divekit generated individualization variables                                                    | Divekit                           |
| [Divekit Language Plugin IntelliJ](https://github.com/divekit/divekit-language-plugin-intellij) | see above                                                                                                                     | Divekit Language Server, IntelliJ |
| [Divekit Language Plugin VSCode](https://github.com/divekit/divekit-language-plugin-vscode)     | see above                                                                                                                     | Divekit Language Server, VSCode   |
| [Access Manager](https://github.com/divekit/divekit-access-manager)                             | Manages repository rights for students and supervisors on Gitlab repositories                                                 | Gitlab                            |
| [Plagiarism Detector](https://github.com/divekit/divekit-plagiarism-detector)                   | Detects variations of defined variables which should not be contained in specific student repositories                        | Divekit, Gitlab                   |
| [Evaluation Processor](https://github.com/divekit/divekit-evaluation-processor)                 | Counts points of detailed evaluation schemes and generates a file which includes the points per exercise and the total points | Gitlab                            |

An example project using Divekit functionality can be found here: [Example Project](// TODO link zu einem beispielprojekt)

