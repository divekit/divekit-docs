---
title: "Testrepo"
linkTitle: "Testrepo"
weight: 2
description: >
  In the test repo, various functionalities of the student's source code can be tested. This pages decribes the various
  functionalities with simple examples
---


{{% pageinfo %}} The documentation is not yet written. Feel free to add it yourself ;)
{{% /pageinfo %}}

## Testing Package structure
[ExampleFile](https://git.st.archi-lab.io/staff/divekit-example/example-origin/-/blob/master/src/test/java/thkoeln/divekit/archilab/PackageStructureTest_testrepo.java)
```java
static final String PACKAGE_PREFIX = "thkoeln.divekit.archilab.";

@Test
public void testPackageStructure() {
    try {
        Class.forName(PACKAGE_PREFIX + "domainprimitives.StorageCapacity");
        Class.forName(PACKAGE_PREFIX + "notebook.application.NotebookDto");
        Class.forName(PACKAGE_PREFIX + "notebook.application.NotebookController");
        Class.forName(PACKAGE_PREFIX + "notebook.domain.Notebook");
        // using individualization and the variableExtensionConfig.json this could be simplified to
        // Class.forName("$entityPackage$.domain.$entityClass$");
        // ==> Attention: If used, the test can't be tested in the orgin repo itself
    } catch (ClassNotFoundException e) {
        Assertions.fail("At least one of your entities is not in the right package, or has a wrong name. Please check package structure and spelling!");
    }
}
```

## Testing REST Controller
[ExampleFile](https://git.st.archi-lab.io/staff/divekit-example/example-origin/-/blob/master/src/test/java/thkoeln/divekit/archilab/ControllerTest.java)

```java
@Autowired
private MockMvc mockMvc;

@Test
public void notFoundTest() throws Exception {
    mockMvc.perform(get("/notFound")
        .accept(MediaType.APPLICATION_JSON))
        .andDo(print())
        .andExpect(status().isNotFound());
}

@Transactional
@Test
public void getPrimeNumberTest() throws Exception {
    final Integer expectedPrimeNumber = 13;
    mockMvc.perform(get("/primeNumber")
        .accept(MediaType.APPLICATION_JSON))
        .andDo(print())
        .andExpect(status().isOk())
        .andExpect(jsonPath("$", Matchers.is(expectedPrimeNumber))).andReturn();
}
```


## Testing ...
