# Chapter 5: Learning the TDD Way

Now we can start speaking about TDD for games, so in this chapter we will learn about test pyramid and test levels, how to think of test cases and what would be a minimum viable game from a testing perspective.

First of all I would like to start with one definition of test levels that I find the most interesting. However there are many more and many that are different from mine, you and your team should reach a consensus over this topic before starting.
* **Unit testing**. They should be testing a fundamental part of the game like a logic, a function, a routine and some controlled behavior inside a namespace or a class. They are the most common type of tests in TDD books and they usually use some variant of the word ASSERT to define the logical boundaries of the test.
* **Functional testing**. They test if a function or an object reacts to some behaviour in an expected manner from a black box perspective. So they usually do this by using some input and observing the output.
* **UI testing**. Sometimes they are put together with the functional testing, but they represent a test that takes a screenshot and compares it with an expected image.
* **Component testing**. They test if the execution of a game object, a package, a subroutine or an internal program are working as expected. A good example would be to test the reactions of a NPC when reacting to the player.
* **Integration testing**. This can be a little confusing in the game industry as many people call component tests integration tests as well, sometimes even functional tests. But in general it tests the communication of our game with external resources, like servers, kinect, controller and audio. Testing player input can be a great example. 
* **End-to-end testing**. I would say this is the simplest test for games, it would be an automated gameplay test.

One very important aspect of TDD is the test pyramid, as it represents loosely how much of each test your project should have. In a general sense the more unitary and low level your test is, the more of this kind you need to have, while tests that take longer or are more high level should have less amount. This is due to the fact that unit tests are way cheaper than integration tests, which are cheaper than end to end and UI tests. So the pyramid base should contain a lot of Unit tests, followed by a bunch of integration tests, a few functional and UI tests and very little end-to-end tests. Projects can separate tests by functionality, classes and context or by pyramid level. Which one to choose depends on what you and your team prefer and both of them are valid. 

## Imagining simple test cases for a game
Talking about a minimum viable game I was inspired to find out which would be the test cases for a movement mechanic in a platform 2D game and what a player would expect from it. For example, in the last project I participated in, a few colleagues of mine were having trouble understanding that we should first define and validate the mechanics that we want so that we could start fine tuning them. The result was that they didn't listen to me, were only preoccupied with the fine tuning of the first mechanic, which resulted in the fact that they almost blew the deadline and the result was terrible because we had one good mechanic and a lot of terrible mechanics.

Considering the basic move mechanics of a platform 2D game, like Mario, we would have walk, jump and fall mechanics. So how could we test them?


### Walk Mechanic

| Feature  	| Character must move alongside the X axis   	|
|---	|---	|
| Input   	| Keyboard input   	|
| Test Case 1  	| Any key is pressed on the keyboard.  	|
| Test Case 2  	| Directional keys were pressed on the keyboard (left/right or a/d)  	|
| Test Case 3   	| When the left key is pressed the character moves left   	|
| Test Case 4   	| When the right key is pressed the character moves right   	|
| Test Case 5   	| When any other key is pressed, nothing happens in the X axis.   	|
| Test Case 6   	| Character collides with walls   	|
| Output   	| If the new position is as expected, the test passes, else it fails.   	|


### Jump Mechanic

| Feature  	| Character must jump   	|
|---	|---	|
| Input   	| Keyboard input   	|
| Test Case 1  	| Spacebar was pressed  	|
| Test Case 2  	| When spacebar is pressed character moves alongside the Y axis  	|
| Test Case 3   	| The character moves correctly from bottom-up. More than one assert may be needed for this test.   	|
| Test Case 4   	| The character moves correctly, when falling from the jump, from top-bottom. More than one assert may be needed for this test.   	|
| Test Case 5   	| When a directional key is pressed, the jump occurs alongside the X axis as well.   	|
| Test Case 6   	| Verifies if a jump with directional keys pressed is parabolic.    	|
| Output   	| If the new position is as expected, the test passes, else it fails.   	|

* Usually a complete jump requires at least 5 assertions to check for correct movement.

### Fall mechanic

| Feature  	| Character must fall into holes   	|
|---	|---	|
| Input   	| Character collides with scene   	|
| Test Case 1  	| Character collides with floor  	|
| Test Case 2  	| Character collides with aerial blocks  	|
| Test Case 3   	| Character falls into holes (gravity testing)   	|
| Test Case 4   	| Character falls into holes following a horizontal launch.   	|
| Test Case 5   	| Same as 4 but from aerial blocks   	|
| Output   	| If the new position is as expected, the test passes, else it fails.   	|

Engineers may find very different ways to solve these test cases. However, the most important part is to keep the test cases simple and the test resolutions and assertion even simpler. If any step gets hard or confused, a good strategy is to take a step back to the intermediary test cases and try to grow the concept from them. This also helps to refactor and evolve the code. 

So now, we can consider that you already know the basic principles of TDD and we can start developing our own game.

