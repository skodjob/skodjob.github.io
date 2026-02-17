---
layout: post
title: Good bye Test-frame and welcome kubetest4j
date: 2026-02-17 10:00:00
categories: tools
image: /assets/kubetest4j.jpg
---

## Why we renamed Test-frame to kubetest4j

When we started the Test-frame project, we wanted to create a library for easier testing of Kubernetes applications and operators.
However, after using it in multiple projects and getting feedback from the community, we realized that our naming was not following Java ecosystem standards.

Most Java libraries use lowercase names with hyphens or follow the standard Maven naming conventions.
Names like `junit`, `mockito`, `testcontainers`, and `kubebuilder` are common in the Java world.
Our original name `Test-frame` with capital letters and a hyphen was not following these patterns.

So we decided to rename the project to `kubetest4j`. The name follows Java naming trends where:
- `kube` refers to Kubernetes
- `test` shows it is for testing purposes
- `4j` is a common suffix meaning "for Java" (like log4j, neo4j)

## What changed with the rename

The rename is not just about the project name. We also changed the Maven coordinates to follow Java conventions:

### Old coordinates (Test-frame)
```xml
<dependency>
    <groupId>io.skodjob</groupId>
    <artifactId>test-frame-common</artifactId>
    <version>1.4.0</version>
</dependency>
```

### New coordinates (kubetest4j)
```xml
<dependency>
    <groupId>io.skodjob.kubetest4j</groupId>
    <artifactId>kubetest4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

The API remains the same. If you were using `KubeResourceManager` or other classes, they work exactly the same way.
The main change is the package names that now use `io.skodjob.kubetest4j` instead of `io.skodjob.testframe`.

## New junit-extension for declarative testing

With the rename, we also added a new module called `junit-extension` that makes Kubernetes testing much easier.
The junit-extension provides a declarative approach with less boilerplate code compared to using `@ResourceManager` annotations directly.

### Old way with Test-frame
```java
@ResourceManager
@TestVisualSeparator
class MyTest {
    @Test
    void testNamespace() {
        Namespace ns = new NamespaceBuilder()
            .withNewMetadata().withName("test").endMetadata().build();
        KubeResourceManager.get().createResourceWithWait(ns);

        // your test code

        // automatic cleanup handled by @ResourceManager
    }
}
```

### New declarative way with kubetest4j
```java
@KubernetesTest(namespaces = {"test"})
class MyTest {
    @InjectKubeClient
    KubeClient client;

    @InjectResourceManager
    KubeResourceManager resourceManager;

    @Test
    void testNamespace() {
        // namespace "test" is already created and ready
        // your test code here
        // automatic cleanup is handled by the extension
    }
}
```

The `@KubernetesTest` annotation provides:
- Declarative namespace creation and cleanup
- Automatic resource manager injection
- Automatic Kubernetes client injection
- Built-in visual test separators
- Log collection when tests fail
- YAML resource loading from files

For complete documentation and examples of the junit-extension features, see the [junit-extension README](https://github.com/skodjob/kubetest4j/blob/main/junit-extension/README.md).

## More features in junit-extension

The junit-extension also supports multiple namespaces and advanced configurations:

```java
@KubernetesTest(
    namespaces = {"frontend", "backend", "monitoring"},
    collectLogs = true,
    logCollectionStrategy = LogCollectionStrategy.ON_FAILURE,
    storeYaml = true
)
class MultiNamespaceTest {
    @InjectNamespaces
    Map<String, Namespace> namespaces;

    @InjectResource("deployment.yaml")
    Deployment deployment;

    @Test
    void testCrossNamespaceWorkflow() {
        // all namespaces are created
        // deployment.yaml is loaded and applied
        // logs are collected if test fails
    }
}
```

You can also test multiple Kubernetes clusters at the same time:

```java
@KubernetesTest(
    namespaces = {"local-test"},
    additionalKubeContexts = {
        @KubernetesTest.AdditionalKubeContext(
            name = "staging",
            namespaces = {"stg-app"}
        )
    }
)
class MultiClusterTest {
    @InjectKubeClient
    KubeClient defaultClient;

    @InjectKubeClient(kubeContext = "staging")
    KubeClient stagingClient;

    @Test
    void testBothClusters() {
        // test logic for both clusters
    }
}
```

## Migration from Test-frame

If you are using Test-frame in your projects, migration to kubetest4j is straightforward:

1. Update Maven dependencies to use new coordinates
2. Update import statements to use new package names
3. Optionally migrate to the new `@KubernetesTest` annotation for simpler test setup

The Test-frame repository has been archived and the latest release is 1.4.0. We do not continue with patching Test-frame and focus only on kubetest4j development.

### Real-world migration example

The [Strimzi Kafka operator](https://github.com/strimzi/strimzi-kafka-operator) successfully migrated from Test-frame to kubetest4j in [PR #12408](https://github.com/strimzi/strimzi-kafka-operator/pull/12408). The migration shows how simple it is - they kept using the raw `@ResourceManager` approach and other existing configurations, just updated the dependency coordinates and package import paths.

No changes to test logic were needed, demonstrating that kubetest4j maintains full compatibility with existing Test-frame code patterns.

## Repository and documentation

The new kubetest4j project is available at [https://github.com/skodjob/kubetest4j](https://github.com/skodjob/kubetest4j).
It contains updated documentation, examples, and migration guides.

All functionality from Test-frame is available in kubetest4j. We added the new junit-extension module for declarative testing, but you can still use the core kubetest4j modules with `@ResourceManager` annotations if you prefer more control.

## Conclusion

The rename to kubetest4j makes our library more aligned with Java ecosystem naming standards.
The new junit-extension provides a declarative approach that removes boilerplate code and makes test setup simpler, while keeping all the same powerful functionality for managing Kubernetes resources during tests.

We believe this change will make the library more approachable for Java developers and help with adoption in the community.