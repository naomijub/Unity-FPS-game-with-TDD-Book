# Chapter 2: What is TDD?

TDD is a software development practice developed by Kent Beck alongside the eXtreme Programming (XP) methodology. TDD's idea is to develop a system based on its test cases. Therefore, you should elaborate your test cases and then elaborate them into tests in such a way that the planned functionality should evolve from the demands of each test written. As the code solution is implemented right after the test is written, TDD is a continuous cycle of testing and coding.

TDD has a few important characteristics:
1. **Frequently execute your tests**. If your tests are being executed frequently it is easy to know the general state of the code. This doesn't mean that you need to run all your test pipeline all the time, but it is useful to run them every time you finish a feature.
2. **Keep your code tested**. It is important to keep your code tested, especially at the unitary level. The smallest code units you test, the easier will be to fix bugs.
3. **Think of a failing test as if it was a failing project build**. If a developed code makes its respective test fail or even another test fails, you should deal with it as if you are putting a bug into the game. Some people just delete the written code and try again from scratch. Finding what works for you is crucial.
4. **Keep your tests simple**. Complex tests may not be testing the units they were supposed to test.
5. **Test cases should be independent from one another**. Each test should only test its unit, component, functionality or how two specific parts work together.

Some advantages of TDD can be:
* Code becomes cleaner, more readable and more maintainable.
* A more modular code. Which also increases code testability.
* You will have a higher test coverage.
* You will possibly find one of the simplest solutions to your problem.
* You will have less defects.
* You will have less bugs.
* You will have extensive documentation of what your code does.
* A more eloquent design of the code.

As there is no silver bullet, here are some disadvantages of TDD:
* TDD won't prevent bugs that your test cases introduced or that you have not thought of, this is why things like regression tests can help.
* To some people, TDD is a slower process. In my opinion, if you don't do TDD you will be required to work more finding bugs.
* All team members got to do it. If only one member includes untested code, that can become a problem to refactor and control in the future. Code reviews help with this item.
* When a requirement changes, your tests need to change as well.

## Why are there so many poorly tested codes?

Would you use a bullet proof vest that was not tested? If not, you should start seeing test development the same way. Let me tell you a small secret, a bunch of years ago I did not like TDD, unit testing and many other test cases that I, as a software engineer, should be writing. I would mostly test code, global variables and local variables printing data to the terminal, and now, here I am evangelizing in the name of TDD. What happened?

As a game developer, in the past, I wrote many untested lines of code, and the result was that I always had to rewrite part of it, sometimes due to some manual testing and most often due to code so complex that was unreadable. This became one problem less the moment I adopted TDD. Of course, in the beginning I took way longer to write my code, but in general there was no need for rework and I could deliver more value through my code.

Besides that I realized a few reasons why testing was so poorly made in the gaming industry and the software industry as a whole:
* If tests are not easily comprehensible, bugs can be added to the program generating catastrophical situations.
* Tests are usually written after the code, which is a problem because whoever wrote the algorithm doesn't want to go back to coding if from the beginning to find a solution, so the test is usually made to fit the current code.
* Tests are usually written by people different from the ones that wrote the code, which can mean that they may have the wrong interpretation of what the code should do or even are just thinking of different requirements. Also, it is harder to test unitary blocks.
* If the person that writes the tests writes it based on documentation or artifacts, any upgrades can make the tests become obsolete. 
* Non automated tests don't run frequently and may not be executed the same way each time. 
* Fixing a bug in one place may cause a problem elsewhere. Test coverage guarantees some safety over this. 

And TDD may solve all of these problems as the person that develops the code will test it first, guaranteeing test coverage and testability. It is easier to execute tests in an automated manner. Bugs can be identified quickly and with punctuality, which will be guaranteed by test coverage. And at last, whenever the code is delivered, the tests are delivered alongside it, which improves maintainability. 
