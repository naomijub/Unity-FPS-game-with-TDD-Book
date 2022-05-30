# Chapter 10: Developing a FPS game with TDD

Let us start by defining what is a FPS game. FPS means First Person Shooter and it is that kind of game where you only see a weapon and sometimes a HUD, it is first person because you play as if you were looking with your own eyes into that world. Another common type of game is third person, which allows you to see your playable character's body. Also, a FPS is a shooter game, which means that we will have some kind of weapon that shoots projectiles at something else.

#### Game Design Document

The Agile Manifesto tells us that we should aim for "**Working software** over comprehensive documentation", which I totally agree with, but on the other hand, having some documentation is also important to have a clear view of what our tests should do and how they should behave. I also agree that "**Responding to change** over following a plan" is clearly important for game development which means that this Game Design Document will only serve as an initial reference to what the game will look like so I don't really expect that the game will be just like the GDD, but I expect that the GDD will provide me with enough material and ideias to start planning ahead basic tests scenarios, scenes and implementations. As this will be a very simple GDD and a very simple game, I don't expect much conflict between them, but if it happens don't worry, it is how things happen in real life.

## The Story

TBD
<!-- The game is very simple, our player spawn inside a bathroom where they should interact with a door, opening it, to see one enemy washing their face, so we shoot and kill them. After that, our player will move into a larger room, by interacting with another door and see that two more enemies are aiming at them, so our player shoots and kills to advance into the next room. Beware! The next room has some errand enemies walking and they will follow us and when near enough, shoot at us. We may need to hide in corners so that we can shoot without being seen. There will be 3 rooms to explore, 1 of them has a secret and the others are packed with enemies, what will we do to get to our secret? -->

## The Character Controller

Our character movement will be pretty simple. It will go forward by clicking `w` or `up` and back by clicking `s` or `down`. Also we can move sideways by clicking `a,d, left, right`. Last character controller feature that I want to introduce is something that I really like from games like Rainbow Six which is bend left and bend right, which will be q and e, respectively. Bending means that our character will look to rotate a little to the side with its origin being the character bottom. By pressing the space bar the character will jump and gravity will act on it.

## The Camera

Like all FPS that I know, the camera will follow the character in a first person perspective and the character will look in the direction that the mouse is pointing. If you move when the mouse is pointing some sideways the character will move in that direction. 

## Weapons

TDB
<!-- There will be 2 weapons, which we can switch between by clicking the number `1` (rifle) and `2` (pistol), and shoot by clicking the left mouse button. Also by scrolling the mouse scroller or by keeping shift pressed you can scope the weapon. If you scope the rifle your shooting mode will change to single rounds and when not scoped you will have an automatic rifle. Pistol rounds are always single rounds. Reloading can be done by pressing `r`. Lastly, we will have grenades that can be thrown by pressing `f`. -->

## The Enemies

TDB
<!-- There will be 2 types of enemies. The first type spawns at specific, mostly, closed positions and don't move, they are like campers looking at x/y positions or doing some business of their own (like washing their hands). The second type spawns in open spaces and can walk around the base. The second type also moves around between predetermined points and if the character gets near their site, they follow the character until they can shoot. -->

## Life Systems

TDB
<!-- The life system is pretty simple, enemies have a life health bar over their head with life 100% and the player has its life in the bottom left part of the HUD. Rifle shots take 24% of a character's life, pistol shots take 18% of a character's life, grenades take 120% decreasing with range and headshots take 100% of a character's life.  -->

## How to Deliver
Delivering in this project will be done in github CI with the following steps:
1. Test
2. Build
3. Deliver artifact to WebGL platform
