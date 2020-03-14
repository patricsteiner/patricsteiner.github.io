---
layout: post
title: Unit Tests for Software Architecture
image: architecture.jpg
categories: [testing, architecture]
---

Code reviews are an important practice for quality assurance in software projects.
They should be taken seriously and the reviewers should try to review code as soon as possible to minimize the developers cost of context-switching.

But as a friend of automation and efficiency, it still often bothers me to see unnecessary discussions about trivial issues in code reviews.

## The problem

Consider this fictional pull request for a Java project and its troublesome review discussion:

**Pull request:**
```java
package com.acme.domain.service;
import com.acme.infrastructure.repository.MongoFooRepository;

@ApplicationService
class FooService {
  public MongoFooRepository fooRepository;
  
  String retrieveBar(Integer barId)
  {
    return fooRepository.get(barId).orElseThrow(() -> new RuntimeException("not found")); 
  }
}
```

**Code review:**

> **Reviewer A:** Please move opening brace on the same line as the method declaration.
>> **Autor:** Sorry, my bad, I'm still used to C# style braces...
>
> **Reviewer B:** We use 4 spaces for indentation, not 2.
>> **Autor:** Might as well use tabs, while we're at it..?
>>> **Reviewer B:** We decided to use spaces to assure the indentations have a uniform width everywhere.
>>>> **Autor:** I think tabs might be better because it's more accessible for visually impaired people...
>>>>> **Reviewer B:** ...🤔
>
> **Reviewer C:**
> - ApplicationServices should be suffixed with _ApplicationService_, not just _Service_.
> - The core domain should not have any dependencies on the infrastructure, therefore you should not use the concrete Mongo repository implementation directly. Use an interface instead. Note that the field should be private, not public.
> - Lastly we should not use generic exceptions like `RuntimeException` but use or create a more specific one instead.
>> **Autor:** Ok.
>
> **Reviewer D:** Why use `Integer` instead of `int`?
>> **Autor:** Because the value might be null.
>>> **Reviewer D:** Then please add a null check.
>>>> **Autor:** Can I also use `Optional`?
>>>>> **Reviewer D**: Optionals are not supposed to be used as parameters.

...

**Inefficiency level:** over 9000

Ugh...😵 

## The solution

Luckily, there is a wide variety of tools that help enforce certain guidelines and standards for projects.
Most of the issues mentioned by the reviewers A, B and D can be avoided altogether by using static code analyzers, code formaters, linters etc. 
They can either be configured in the IDE, integrated in a git hook or somewhere in the build pipeline to make the build fail when any policy is not followed.

However, the concerns of Reviewer C are legit and cannot really be avoided with the previously mentioned tools.
And this is where [ArchUnit](https://www.archunit.org/) comes in.


ArchUnit is a library for testing the code architecture of Java projects using simple unit tests. Let's see how it works.

First, add the dependency and a new class for our tests:

```xml
<dependency>
    <groupId>com.tngtech.archunit</groupId>
    <artifactId>archunit</artifactId>
    <version>0.12.0</version>
    <scope>test</scope>
</dependency>
```

```java
@AnalyzeClasses(packages = "com.acme", importOptions = {
    ImportOption.DoNotIncludeTests.class,
    ImportOption.DoNotIncludeJars.class
})
public class AcmeArchitectureTest {
   // TODO: That's were we will add our ArchTests
}
```

Now, let's address the concerns of reviewer C one by one by adding simple `@ArchTest`s to the test class:

Concern #1: Classes annotated with `@ApplicationService` must have the proper suffix:

```java
@ArchTest
public static final ArchRule applicationServicesShouldHaveProperSuffix = ArchRuleDefinition
        .classes().that().areAnnotatedWith(ApplicationService.class)
        .should().haveSimpleNameEndingWith("ApplicationService");
```

While we're at it, let's also add a test to assure all ApplicationServices reside in the proper package:

```java
@ArchTest
public static final ArchRule applicationServicesShouldResideInApplicationPackage = ArchRuleDefinition
        .classes().that().areAnnotatedWith(ApplicationService.class)
        .should().resideInAPackage("com.acme.application");
```     
     
Concern #2: No dependencies from core to infrastructure:

```java
@ArchTest
public static final ArchRule domainShouldNotHaveAnyDependenciesOtherThanJdkAndItself = ArchRuleDefinition
        .classes().that().resideInAPackage("com.acme.domain..")
        .should().onlyDependOnClassesThat().resideInAnyPackage("com.acme.domain..", "java..");

```
Concern #3: Fields should be private:

```java
public static final ArchRule nonStaticFieldsShouldBePrivate = ArchRuleDefinition
        .fields().that().areNotStatic()
        .should().bePrivate();
```

Concern #4: No generic exceptions:

```java
@ArchTest
private final ArchRule noGenericExceptions = NO_CLASSES_SHOULD_THROW_GENERIC_EXCEPTIONS;
```

... and that's it. Boy, that was easy 😮

## Summary
With ArchUnit we can assure that certain predefined architectural patterns and rules are followed by everyone working on the project.
Failure to comply with the ArchUnit tests, of course, will cause the build to fail.
As we see in the examples above, ArchUnit has a very semantically easy API. By using the IDEs intellisense we barely even need to read any documentation 😃.
Check out [these examples on Github](https://github.com/TNG/ArchUnit-Examples) to get some more inspiration.

Happy coding, and have a great start in 2020! 👨‍💻🎉


_PS: Adding ArchUnit tests to your java project could be a great new year's resolution 😉_
