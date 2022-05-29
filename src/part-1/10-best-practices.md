# Chapter 9: Best Practices Writing Tests

[C# best practices for unit testing](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices)

There are a few types of tests and best practices may vary depending on which test you are writing and how they are organized. One thing I like a lot is to separate integration and unit tests, usually doing it by putting in different folders. For Unity, you could separate unit tests from integration tests by creating two different folders with two different assembly definitions, like `UnitTests` and `IntegrationTests`, something like `UnitTests` and `PlayTests`.

Also, even in Unity, I like to avoid slow unit tests. To do so, I leave tests that will be slower, even if they are unitary, to run separately from the fast unit tests. Another thing is that I avoid too many dependencies in unit tests and if I have them, I tend to pass them as arguments, especially those that have side-effects. Speaking of side-effects, I usually try to avoid unit testing functions that have side-effects, so I usually write my functions as pure as possible, not like `PlayerLife.IsAlive`, which is mostly a getter.

Having said that we can point out a few important characteristics of a good unit test:
* Fast: good projects may have thousands or tens of thousands of unit tests, so they should execute quickly so that they can be executed more frequently.
* Isolated: they should be standalone and so be executed in isolation from other contexts, dependencies or external systems.
* Repeatable: They should be like pure functions, so for every time you run them, the same result should be expected. This, also, allows them to be executed frequently.
* Self-checking: this might seem dumb for people that are used to automated testing, but many game developers are not used to this concept. A test should be capable of detecting its success or failure without any human interaction.
* Quick execution: if a test takes too long to write or too long to execute, consider designing better tests or consider designing a code that is more testable.

## Naming Conventions

C# has a good set of best practices and naming conventions for writing code and writing test, so when we are writing a new test we should consider the following three parts:
1. The name of the method being tested. (`IsALive`)
2. The scenario under which it is being tested. (`WhenInstantiate`)
3. The expected behaviour of this scenario. (`ReturnsTrue`)

Additionally we could declare the specific component that we are testing `PlayerLife_IsAlive_WhenInstantiated_ReturnsTrue`. Also, naming conventions are important because they explicitly express the intent of the test. This explicit expression is not just about readability as it serves as documentation for when someone is reading a test file to understand exactly what are the expected behaviours of a class, namespace, component or object.

Another topic that frequently causes confusion is the words **Fake**, **Mock** and **Stub**:
* Fake - fake is the generic term used to describe an object that is not real or doesn't behave as a real object. A fake can be a stub or a mock, it all depends on the context.
* Mock - A mock is a fake object that influences the behaviour of the test depending how it is described. So, in other words, a mock starts a fake until asserted against.
* Stub - A stub is a dependency or a collaborator system that controls how it is going to be used in regards to replacing a part of our test. The general advantage of a stub is that you can execute the test without having to deal directly with a dependency.

The last thing I want to talk about before starting our game is how to write the tests themselves and the two main things that come to my mind is to keep your tests minimal and follow some kind of pattern like **Arrange, Act and Assert**. 

On the topic of keeping our tests minimal, it means that the input passed to the function being tested should always be kept the simplest possible, for example, Rust linting tool, clippy, warns you if your function has too many arguments. Another thing that is important to say about simple inputs is that they should be predictable, so we can achieve the same result for every test execution. What do we achieve with this principle? Tests become more resilient to future implementations and the test behaviour is closer to the expected functionality. 

Lastly, on the Arrange, Act, Assert topic, it is a common pattern when writing tests and, as the name implies, it breaks down into three sets of actions:
* Arrange - create necessary objects and set them to the correct state.
* Act - define the action that will be tested.
* Assert - asserts that the act action has the expected value.

Now we have a clear idea of what TDD is and how to use it, we have configured our Unity test environment and we are clear on the test practices that we are going to use in this book. I would say we are ready to starting writing some great tests for our first person shooter. 

# Summary

In this book section we learned that:
1. TDD is a continuous cycle of test, code and refactor that will always generate well tested code, a code that is probably simpler, as well as quickly identify bugs that would break other tests.
2. We learned some TDD practices, like keeping your tests simple and independent from one another.
3. Why there is so much poorly tested code, considering some of the history and biases that the game industry had.
4. Why TDD is important for game development, which discusses the fact that games have an art/entertainment relation as well as the hardships of testing game UI and Gameplay.
5. When to use TDD and the way to think about basic walk, jump and fall mechanics on the TDD side of things.
6. How and why to use CI for game development and associate TDD with it.
7. Important aspects of configuring a test environment on Unity like the test runner location and the necessary assemble definitions and references.
8. Lastly, some best practices writing tests.
