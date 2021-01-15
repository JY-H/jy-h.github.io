---
layout: post
title: "Understanding the Mechanics of a Game Bot: Memory Editing and its Applications in RPGs"
author: "JY"
tags: [code, software, games, mmorpg, world of warcraft, wow, bot, hack]
---

Back in June of last year, Blizzard announced that it had suspended over [74,000](https://us.forums.blizzard.com/en/wow/t/actions-taken-to-address-exploitative-gameplay/558339){:target="_blank"} _World of Warcraft Classic_ accounts that were found to be automating gameplay via botting. Bots attack the integrity of a game like WoW (stealing resources, inflating the server economy, monopolizing rare drops) that is built on time investment and patience. When I read this
announcement, I was, to some extent, amused. After all, the game has been around for over 16 years, yet the botting problem appears to be one of the same.

So I got curious. How do you build a bot for a game like World of Warcraft? What about aimbots for FPS games? This will likely be broken up into a series of posts because there's a lot of interesting shit to talk about. For now, we will go over the basics of memory scanning and editing.

My hope is that my writing here will be accessible to those of you without a programming background. Feel free to yell at me if that's not the case.

### Terminologies
Per usual, let's introduce some basic terminologies to make future discussions a little bit easier.

* Game Loop: The game loop is the overall flow control for the entire game program. At each turn of the loop, the game processes user inputs, updates the game state, and renders the game.
* Memory: A generic term for different types of data storage technology that may exist on a computer. For the purposes of this post, we will equate memory with RAM (random access memory). This is where data currently in use by programs are kept, intended for fast access by the processor. RAM will not store permanent data (i.e. you restart the machine, you will lose everything in memory).
* Program: A collection of instructions that can be executed by a computer to perform a task. A game is a program.
* Function: A function encapsulates a task in a program. Functions are self-contained (function A cannot access the variables in function B); often takes in some input, processes the data, and returns some result. For example, a player in a game might "take damage", or "heal", these could be separate functions.

### How Things Work
To understand how a game bot is written, we have to understand memory editing. To understand memory editing, we first have to understand what happens when a game, or any program, is executed.

#### Virtual Memory
Modern operating systems uses a memory management scheme called virtual memory.

Unfortunately virtual memory is a bit of an overloaded term. In the technical sense, it is a technique whereby the operating system provides programs with an idealized abstraction of the available physical resources on a given machine. In this scheme, every process has its own virtual address space, and memory addresses in that address space are mapped to physical memory addresses by the kernel via techniques beyond the scope of this post. If you're curious, I recommend reading
[this](http://pages.cs.wisc.edu/~remzi/OSTEP/vm-paging.pdf){:target="_blank"} (WARNING: do not click if you have PTSD from OS).

In the non-technical sense, virtual memory is disk space used in place of RAM when compensating for shortages of physical memory (data are transferred from RAM to disk). In our case, we are interested in the technical defnition. This is important because we need to know what the virtual memory address space looks like.

#### The Address Space
A generic address space might look something like this:
```
+---------+
|  stack  |  remember the function thingy we defined above? The stack 
|         |  holds data that's local to the function. For example, 
|         |  a "take damage" function might calculate the damage based
|         |  on the player's defense, and the opponent's attack power,
|         |  we can do some calculations within the function, apply the
|         |  damage to our player's health, then forget about it.
+---------+
| shared  |  shared libraries loaded by a program, not important for 
|  libs   |  our discussion here.
+---------+
|  hole   |  unused memory allocated between the heap and stack "chunks"
|         |  
+---------+
|  heap   |  dynamic data that you create on the fly are placed here.
|         |  Unlike stack variables, which are local to a function
|         |  (like the "dmg" value in the "take damage" function example),
|         |  heap variables are accessible by any function.
+---------+
|   bss   |  basically the same as below, but a mere "placeholder" as
|         |  we don't know the value yet. We called this "uninitialized"
|         |  global variables.
+---------+
|  data   |  for globals and static variables that are initialized.
|         |  These are data that are accessible by any function in a 
|         |  program. Unlike variables on the heap (which are created
|         |  on the fly), these values are known since the beginning
|         |  of the program. The memory address of a global variable
|         |  here will remain the same through the lifetime of a program
|            execution.
+---------+
|  text   |  program code, this is the actual code that is running.
```

#### An Example
Let's go through what a simple, example program might look like on this address space to hammer the idea home. Imagine a very simple game:
* A player starts off with health at 100.
* A game loop, where on each loop of the game, the player "takes damage" equal to a random value, ranging from 1 to 50.
* If the player presses space bar, the player can "heal" with +5 to the health.
* The game is lost if the player's health drops to 0.

I know this is a terrible game design and no one would ever play this game, but for the purposes of demonstration, please bear with me. This is what the address space for the game process might look like when the game is run:
```
+---------+
|  stack  |  the random "dmg" value for "take damage" will live here
|         |  each time the function "take damage" is executed.
|         |  
+---------+
| shared  |  not important.
|  libs   |  
+---------+
|  hole   |  not important.
|         |  
+---------+
|  heap   |  before the game loop starts, a "Player" object with health
|         |  data is allocated on the heap. Note that "Player" cannot
|         |  be on the stack because both our "take damage" and "heal"
|         |  function need to be able to modify the player's health.
|         |  
+---------+
|   bss   |  nothing here.
|         |  
|         |  
+---------+
|  data   |  given our healing prowess is known since the very start,
|         |  for demonstration's sake, let's say a healing amount variable 
|         |  resides here with the value of 5.
|         |  
|         | 
+---------+
|  text   |  the actual game program gets put here when it's executed.
```

#### Memory Scanning and Editing
So, how would you cheat the simple game I described above? You could theoretically do it one of the following three ways:
1. Somehow edit the "dmg" value so that we take no damage on each game loop. This seems a little complicated since the "dmg" variable disappears and reappears on each game loop (remember it's a function-local variable).
2. Somehow edit the Player's health value directly so that it's always set to 100. This also seems easier than (1), since the Player is a globally accessible object.
3. Edit the "heal" value such that on each space bar press, we heal for a massive amount, making it impossible for our health to ever drop to 0. This seems like the best option, since the healing amount variable is in the data segment and its memory address will remain the same for the duration of the program's execution.

Let's take strategy (3) for example. We will want to find the memory address of the global variable that denotes the healing amount, with a value of 5, and then edit that memory address to contain a value of say, 50, instead.

To do this, you might use something like [Cheat
Engine](https://www.cheatengine.org/){:target="_blank"}, which will look for the memory address of an object in a running program if you give it hints. In our case, we might tell it to look for a variable with an exact value of "5". We might press space bar a few times to give Cheat Engine opportunities to land on the same memory address, every time we heal.

Let's say Cheat Engine finds an answer for us and we are confident in the memory address of the variable denoting the healing amount. Now what? The tricky thing here is that each time the game program is restarted, the base memory address of the program changes. So we can't simply reuse the same memory address each time. But we also wouldn't want to have to look for the address of the desired variable every time the game is restarted -- that would make our cheat practically useless.

Fortunately however, assuming the program code (the text itself) doesn't change in between runs, a global variable will always be placed at the same offset from the base memory address of the program. So in our cheat, we'd want to retrieve the base memory address of the process, and edit the memory address at `game_base_address + offset` to be 50, instead of a measly 5.

Inject this cheat into the game program using DLL injection (let's not get into
it here or I'm going to be writing for ages), and voila, as long as we keep pressing space bar, we will never lose the game!

### Application to Botting
I gave the example above to illustrate how memory scanning and memory editing serve as the foundation for hacking a game. But in reality, the task would be much more daunting than that.

For one, most games would be much, much more complicated in structure than what I've described above. Moreover, hacks for single player games might work this way, but something like this will not work for multiplayer games. This is because a lot of multiplayer games use the [dedicated game server
network model](https://jy-h.github.io/fps-netcode-mechanics.html){:target="_blank"}, where the game server holds the state of the world; as such, changing say, your character's statistics, in the local copy of the game would not affect what other players see online.

All of this doesn't even take into consideration of the other side of the same coin -- cheat detection. Blatantly changing your character's statistics will likely get you banned in a multiplayer game. So in programming game cheats, the developer would have to be creative in avoiding detection as well. Perhaps more on cheat prevention and detection techniques in a later post.

Botting however, is not a cheat in the traditional sense of the word. Instead of manipulating the game state to a competitive advantage, what you're doing is writing a program that, when injected, would call the functions in the actual game in an automated, logical manner. For example, in the context of World of Warcraft, creating a program for a gathering bot would involve some combination of the following tasks:

Memory scanning:
* Finding our character object.
* Finding the memory address of a function to detect nearby gathering nodes. Or iterating through game objects near our character to look for gathering nodes ourselves.
* Finding memory addresses of functions (residing in the "text" segment of the address space) for moving our character, for accessing a gathering node, and for picking up loot.

Memory writing:
* Devising some assembly to invoke the functions with addresses / offsets found above.
* Inject the assembly into the game client.
* Execute the assembly.

Logic to automate the bot:
* A loop where we:
    * Call function to look for nearby gathering nodes. Retrieve the closest point.
    * Call function to move our character towards the point found above.
    * Call function to gather and pick up loot.
* To get fancier you can program in auto-storage or auto-auction, or even some route optimization for a gathering path.

### Closing Thoughts
To be honest, writing this post has taken far longer than what I had originally anticipated, and even at over two thousand words and an oversimplification of many of the technical details, I've barely scratched the surface. So I'm going to end it here for now.

With that said however, this topic, as an application of reverse engineering, assembly code analysis, memory manipulation, code injection, etc, has gotten me hooked to some extent. They say low-level programming is good for the soul... So I shall go learn more, and report back in due time.
