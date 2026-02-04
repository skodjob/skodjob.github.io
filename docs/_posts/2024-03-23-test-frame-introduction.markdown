---
layout: post
title: Make your testing easier with Test-Frame
date: 2024-03-23 16:00:00
categories: tools
image: /assets/test-frame-introduction/test-frame-introduction.jpg
---

## Testing of applications running on Kubernetes

Today, multiple popular languages are used for writing Kube-native applications.
The most popular are Java and Go, mainly in developing applications based on the [operator pattern](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/).
Additionally, there are multiple libraries for both of the languages mentioned above, making the work
with Kubernetes API much easier.
One of them is [Fabric8's kubernetes-client](https://github.com/fabric8io/kubernetes-client), which allows developers
direct communication with the Kubernetes API.
Because of this library (and also many others like [JUnit5](https://junit.org/junit5/)),
we decided to write the test suite in Java, which is also the language that our
application is written in.

## More repositories, more redundancy

Usually, all the test logic is in the application's repository.
However, once we want to extend the product and add new components that will also need system testing,
we start a fight with redundancy, where you will need to copy the testing-related classes across multiple repositories,
mixing things and always trying to refactor the code without success.
At least, that is the situation in which we ended up.

Yes, you can use the test classes from the main repository as a Maven dependency, in case you are releasing it together
with other modules to the Maven Central (or anywhere else).
However, there are situations when you cannot do that - one of the examples is cyclic dependency.

Let's say you have the main repository containing the code for an operator and a few other repositories for the components 
managed by the operator.
Each component has a different release cadence, so having them in the operator's repository is not the correct approach.
In this particular case, if you would use the system test dependency from the operator's repository inside the component's repository (and the component
is used inside the operator's repository), then you create a cyclic dependency, where the component waits for the release of the operator
with new things in the test classes, and the operator waits for the component to be released, so it can be included in the controller's code.
The cyclic dependency is depicted on the following picture:

![Cyclic redundancy](/assets/test-frame-introduction/test-frame-cyclic-redundancy.png) {.wp-post-image}

Or, you can have one repository for the test logic, that will be shared across multiple repositories, and thanks to that you will remove
the cyclic dependency.
Nevertheless, with a new separate repository, there are different challenges.
You need to think about how well it will be maintained - do you and your team have time to actively contribute to this repository and release a new
version of it regularly, without breaking the logic of the test suites in each of the repositories?
How frequent will the releases be?
What about the situation when you work on different repositories outside the current organization and this module cannot be imported?

## Test-Frame as the solution

Because **Skodjob** consists of engineers working on different projects, but with the same passion for testing the integration between the components,
we were finding a proper solution for sharing the code between organizations, that would help us with removing redundancy and duplicated code, which
was anyway present throughout the repositories.
Given these questions, we decided to create a library called [Test-Frame](https://github.com/skodjob/test-frame/).
The library aims to provide common functionality for testing applications running on Kubernetes.
It also tries to make the overall process as easy as possible.

### What it provides?

The core of the Test-Frame is `KubeResourceManager`, which is responsible for managing resources during the test phases.
It handles Fabric8-based objects, that are extending the `HasMetadata` class.
Depending on the `ExtensionContext`, `KubeResourceManager` creates a new stack for each context, where it stores all resources created in the test case.
After the test is finished (no matter if it failed or passed), `KubeResourceManager` deletes the resources in reverse order, following the LIFO
method.
This removes the need for manual clean-up after each test case and overall management of the resources.

Besides that, Test-Frame provides classes for easier handling of fundamental Kubernetes resources, such as Secret, Service, NetworkPolicy, and
many more.
It also has a built-in `KubeClient`, which creates a basic encapsulation around the Fabric8's client, together with the possibility to
specify `KUBE_CONTEXT` for the cluster where the tests should be executed.

The library is still under development, and we are planning to add more functionality like a log collector (which will collect also YAMLs of resources, Pod description, and more)
or metrics collector, for easier gathering metrics from the pre-defined endpoints.

### How I can use it?

During the development, the Test-Frame library is being pushed to GitHub's Maven repository.
If you would like to try the Test-Frame in your test suite before it is released, you can do that by including
the GitHub's Maven repository and the Test-Frame dependency in your `pom.xml`:

```xml
    <dependency>
        <groupId>io.skodjob</groupId>
        <artifactId>test-frame-common</artifactId>
        <version>0.10.0</version>
    </dependency>
```
where the `test-frame-common` module contains the core functionality - like the `KubeResourceManager`.
Additionally, there are more modules that you can use and explore in our [GitHub repository](https://github.com/skodjob/test-frame).

Where the `id` of the server has to match with the `id` of the repository in the `pom.xml`.

Once you are all set, you can try to experiment with the library in the tests by annotating the test classes with
`@ResourceManager` annotation, determining that the `KubeResourceManager` should manage the resources created in the tests.

```java
@ResourceManager
class NamespaceCreationST {
    
    @Test
    void testThatNamespaceExists() {
        KubeResourceManager.get().createResourceWithWait(
                new NamespaceBuilder().withNewMetadata().withName("test").endMetadata().build());
        assertNotNull(KubeResourceManager.getKubeCmdClient().get("namespace", "test"));
    }
}
```

In this example, the Namespace with the name `test` is created in the test case `testThatNamespaceExists`, and after the test
is finished, it is also deleted by the `KubeResourceManager`.
This will ensure that the Namespace will not affect any other tests, which will start again in a clean environment.

## Conclusion

Writing tests with all kinds of helper classes and methods can be a repetitive task filled with duplicates and 
redundancy when it comes to a project containing multiple repositories that need testing.
But Test-Frame, a library providing common helper methods for work with tests and objects running on Kubernetes, is here
to help you with it.

New contributors are welcome to shape our project's future; share your thoughts on the library or suggest what's next – your feedback matters!