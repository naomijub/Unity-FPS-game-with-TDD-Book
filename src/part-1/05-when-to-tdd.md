# Chapter 4: When to use TDD?

As I said before, TDD is not a silver bullet, but it does have a strange effect on you. Although it is not always necessary, once you get used to it, you will probably do it anywhere. TDD can greatly be applied to the following scenarios:

**Browser front-end development**. Whenever someone is developing a web page, it may be very interesting to develop it with TDD as web pages usually have different usage possibilities and you want to make sure that the user will have a great experience using it. Also, it is important to make sure your system is able to handle the data received from the back-end and correctly renders it. Here you can have a whole pyramid of tests with unit tests, integration tests, functional tests, UI tests.

**Microservices and back-end**. They run the web, but now imagine your bank doesn't test how your money will be handled and saved. This means that any bug can cause you to lose a great amount of money. Backends are the origin of TDD usage and they are critical for games as well. Most common tests for backends are unit tests, integration tests, contract tests and some end-to-end testing.

**Mobile apps**. This is very similar to front-end requirements, as mobile apps can cause your user to have a terrible experience, but more than that, they are constantly updated, so having a good CI is important for them. 

**Games**. Well, if we test UIs for mobile and for browsers, why can't we test UIs for games? There is the random state factor, but this can easily be solved by pure functions which would allow our logic functions not to have side effects and they will generate a precise response and a precise UI. With precise states for UIs we can define how we want our game UI to look like. Also we can test the connection to our servers and how they affect the general game state. Again, you can have a whole pyramid of tests with unit tests, integration tests, functional tests, UI tests.

**IoT and Embedded**. Just imagine the chaos it would be if your television had a bug that turned on your microwave, which you left something inside, when you are not expecting it to happen. Or just imagine a critical bug failure in the airplane you're flying. Testing these for unit level and integration level seems quite important.

But I said that it couldn't be applied to all scenarios, right?

**Famous and known algorithms**. You can use TDD to solve a merge sort algorithm, but you probably won't get to a better solution than the classical algorithm. So, although testing can give you confidence that you wrote the correct solution/algorithm, TDD won't actually help you to find the best solution every time. Though it does help to have a cleaner code. 

**Research and Development projects**. These kinds of projects usually are just spikes or proof of concepts, and they are made just to prove a point or show a new technology.  Although testing them could be beneficial, for later usage, it can be time consuming.

There are more cases for both sides, but I see this as the most important scenario to discuss TDD.
