---
layout: post
title: "Understanding Netcode Mechanics: Delay-based and Rollback Netcode in Fighting Games"
author: "JY"
tags: [code, software, smash, fighters, fighting games, delay-based netcode, rollback netcode, netcode, fps, games, shooters, melee, fizzi, slippi]
---

Let's continue our chat on netcode mechanics! If you haven't read my last post on netcode mechanics in FPS games, go give it a read [here](https://jy-h.github.io/fps-netcode-mechanics.html).

### What's different?
Unlike FPS games which often rely on dedicated servers, fighting games follow a peer-to-peer model where players communicate directly with each other. However, in the same vein, fighting games still encounter the issue of input delay, where the opposing player's inputs must be sent over the network before the results of those inputs can be
displayed. The problem is the same, but the tools we have at our disposal to resolve the problem are different, as in the case of the peer-to-peer model,there is no central authority (the server) to hold the canonical game state.

#### Deterministic Lockstep
Since there is no central authority in the form of a dedicated server, fighting games use something called deterministic lockstep. Deterministic lockstep is a networking mechanism where peers communicate by sending only the inputs that control the system (in this case, the inputs of your controller, rather than say, your character's positioning on the 2D grid). The simulation on both machines must be identical: given the same initial condition and the same set of inputs, the simulations must return identical results. This is the
"deterministic" part of the protocol. If at any time, peers disagree on the simulation results (in this case, the current game state), the game desyncs. This is the "lockstep" part of the protocol and typically done via sharing the checksums of the game state between peers.

### How Latency is Dealt with in Fighting Games
Let's set the problem statement.

Given fighting games have to process inputs for a given frame from both players to correctly simulate game state, devise a strategy to account for network latency (which causes the opposing player's inputs to arrive late) to keep the gameplay as smooth as possible. Here we define frame to be the unit of time it takes for the game to execute a single processing loop (process controller inputs, run AI for CPU players, render end result, etc). 

#### Naive Solution
The naive solution is to simply wait for the remote inputs to arrive before either peer can proceed with the gameplay. This means that the game loop is paused until all information are available: you make a move, the game lags for a few frames until your opponent's inputs arrive, then the game continues. The consequence is that the frame rate at which your game can run becomes limited by the latency between you and your peer. For example, if the connection has a latency of 200ms, then your game would have to run at a measly 5Hz (or 5 frames per second).

No sane developer would do this. This is the solution you give in interviews to "at least have code on paper" :)

#### Delay-based Netcode
In simple terms, delay-based netcode artificially delays the local player's inputs by the amount of time it would take for the remote player's inputs to arrive. The key difference here is that instead of pausing the entire game processing loop, we only delay the input processing so the rest of the game can run at whatever frame rate it desires. Let's go over an example:
* Assume a setting where P1's ping to P2 is 90ms. This means that on average, we expect P2's inputs to be transmitted within 45ms. On a game operating at 60 frames per second, this means our expected delay is around 3 frames.
* P1 presses jump on frame 1.
* P1's input is not animated immediately in online play because the local game has not yet received confirmation on whether P2 performed any actions on frame 1.
* Instead, P1's jump input is delayed for 3 frames and animated on frame 4 alongside any actions that P2 had taken, assuming that inputs from P2 arrived within the expected time.

At first glance, the solution seems reasonable: a 3 frame delay is typically not noticeable to the untrained eye, since the average reaction time is around 16 frames. However, this is only the case if the network latency remains consistent between two peers (i.e. remain at 90ms for the entire duration of the match). Unfortunately, this is often not the case. When latency spikes, P1's game has no choice but to pause the game entirely until it has information from
both players, effectively reverting us back to the naive solution from time to time. While we can optimize the delay-based solution by monitoring the network connection and change the delay to match the network connection, this is often not effective because latency spikes are difficult to predict.


#### Rollback Netcode
Rollback netcode approaches the problem in a greedy manner:
* All inputs from the local player are processed immediately as if the game is running offline.
* If the remote input is missing for a given frame N, the game predicts the remote input and continues the simulation. More on the "prediction" later.
* When the remote input eventually arrives for a given frame N, two things can happen. If the prediction was correct, we simply continue. If the prediction was incorrect, the game rolls back the simulation to frame N and applies the corrected input to the game state, then renders the results immediately on screen. 

This means that when the game's prediction of remote input is incorrect, the local player may see an unnatural jump in game state on screen. This might sound undesirable at first, but depending on the game, "prediction" is actually easier than you might think. Even at the [highest level of competition](https://youtu.be/pdahogyJCNI?t=1034){:target="_blank"} for a game like DBFZ, it's a safe assumption that if down-back was held at frame N, it will likely continue to be held for the next
few hundred frames. So more often than not, "prediction" is simply the assumption that the remote player continued holding the previous input.

### Challenges of Rollback Netcode
In late June, the Smash melee community was shaken by the introduction of rollback netcode to [Slippi](https://slippi.gg/){:target="_blank"}, and consequently there has been a lot of anger towards Nintendo's refusal to take any realistic action in fixing Ultimate's online experience. Masahiro Sakurai, SSBU's game director, has been quoted saying that "the side effects were simply too big."

I'm not going to talk about whether Nintendo _should_ support the desires of the competitive community. Instead, I'm going to discuss the engineering challenges of implementing rollback netcode for a game as uniquely complex as Smash. 

The bottom line is, Sakurai's statement does hold true from a development standpoint.

#### Game State Serialization
Under delay-based netcode, the game only has to keep enough game state in memory to simulate from one frame to the next. However, with rollback, the game must now be prepared to return to any point in time in game history in case its prediction of the remote input for a given frame was incorrect. This means that serialization -- the process of converting game state into a format that can be easily saved and reloaded -- must be implemented and optimized for performance.

This can be especially complicated for a game like Smash.

Let's take a second and consider how Smash's gameplay differs from other traditional fighting games. In DBFZ for instance, you are limited by the game engine in the sense that you and your opponent cannot be further apart than the screen allows you, you cannot face the opposite direction of your enemy, and your jumps do not have a lot of variety due to the lack of things like platforms. Smash is not limited by these
factors. Moreover, compared to traditional fighting games where block strings and predefined combos are the core mechanics in one's gameplay, Smash grants players much more freedom: outside of a few true combos, there are no predefined input chains, knockback changes as life percentages go up, and mechanis such as edge guarding introduce far more creative opportunities than what's allowed in traditional HP reduction mechanics. This means that compared to traditional fighting games, Smash is in an unique position where:
1. The game has to handle more state, making optimization of game state serialization more difficult.
2. Inputs are more difficult to predict due to the significant amount of creative freedom the game offers to its players. This means that rollback becomes a more frequent occurrence, making optimizations even more paramount.

#### Code Architecture and Other Edge Cases
Those of you who are SWEs likely have lived through the pain of combing through some heavily entangled code. In order to introduce rollback netcode, the simulation of the game logic must be independent from everything else in the gameplay loop, including rendering, communication with I/O devices, etc. This means that if a game was not originally designed with rollback netplay in mind, the architecture might have to be altered entirely to accommodate this new
requirement. Unfortunately, redesigning the architecture of a large existing code base is never an easy feat.

We can go on with many other edge cases that would have to be considered when a rollback occurs, as examples:
* Audio. The game needs to keep track of the sounds that have been played, as well as whether the sounds are still in progress when a rollback occurs, and whether the SFX have to be stopped or replaced with something else. 
* Object life cycle. An incorrect prediction may result in the need to recreate a disappeared projectile, such as R.O.B's laser beam or Sheik's burst grenade, which requires a precise history of its positioning, velocity, and progression.
* ...

#### But what about Project Slippi?
So rollback is hard to implement, but what about Melee and Project Slippi?

The defining feature of rollback netcode is that the game state can be rolled back to an earlier point in time, which means the game engine would need to be natively designed to incorporate it. In other words, unless you have access to the source code (you are Nintendo), rollback is impossible. In the case of Melee, emulation makes rollback possible.

But honestly, Fizzi is out of this world for somehow making Project Slippi work. In order to introduce rollback netcode for Melee, one essentially has to do rollback with Dolphin savestates, which store the entire state of the emulated GameCube or Wii rather than just the game states; the extra load makes it that much harder for the savestating and reloading process to be performant enough for the game to remain playable.

I'm poking through some of Project Slippi's source code to understand exactly how this was accomplished. If you're interested in joining me, [this](https://github.com/project-slippi/Ishiiruka/blob/slippi/Source/Core/Core/HW/EXI_DeviceSlippi.cpp#L1467){:target="_blank"} might a good place to start to understand general entrypoints, or you can look at commits prior to v2.0.0 release to see the initial netplay progression from Dolphin.

I will report back again if I ever get to the bottom of it.
