---
layout: post
title: "Understanding Netcode Mechanics: Delay-based and Rollback Netcode in Fighting Games"
author: "JY"
tags: [code, software, smash, fighters, fighting games, delay-based netcode, rollback netcode, netcode, fps, games, shooters]
---

Let's continue our chat on netcode mechanics! If you haven't read my last post on netcode mechanics in FPS games, go give it a read [here](https://jy-h.github.io/fps-netcode-mechanics.html).

### What's different?
Unlike FPS games which often rely on dedicated servers, fighting games follow a peer-to-peer model where players communicate directly with each other. However, in the same vein, fighting games still encounter the issue of input delay, where the opposing player's inputs must be sent over the network before the results of those inputs can be
displayed. The problem is the same, but the tools we have at our disposal to resolve the problem are different, as in the case of the peer-to-peer model,there is no central authority (the server) to hold the canonical game state.

#### Deterministic Lockstep
Since there is no central authority in the form of a dedicated server, fighting games use something called deterministic lockstep. Deterministic lockstep is a networking mechanism where peers communicate by sending only the inputs that control the system (in this case, the inputs of your controller, rather than your character's positioning on the 2D grid). The simulation on both machines must be identical: given the same initial condition and the same set of inputs, the simulations must return identical results. This is the
"deterministic" part of the protocol. If at any time, peers disagree on the simulation results (in this case, the current game state), the game desyncs. This is the "lockstep" part of the protocol and typically done via sharing the checksums of the game state between peers.

### Problem Solving
Let's pretend this is a SWE interview. Given fighting games have to process inputs for a given frame from both players to correctly determine game state, devise a strategy to account for network latency (which causes the opposing player's inputs to arrive late) to keep the gameplay as smooth as possible.

#### Naive Solution
The naive solution is to simply wait for the remote inputs to arrive before either peer can proceed with the gameplay. This means that the game loop is paused until all information are available. The consequence is that the frame rate at which your game can run becomes limited by the latency between you and your peer. For example, if the connection has a latency of 200ms, then your game would have to run at a measly 5Hz (or 5 frames per second).

In other words, make online play so miserable the only solution is offline play (or just let the game die). Forgive me, I'm writing this after playing an online smash game where I could take a sip of water in between frame lags :(

#### Delay-based Netcode
In simple terms, delay-based netcode artificially delays the local player's inputs by the amount of time it would take for the remote player's inputs to arrive. The key difference here is that instead of pausing the entire game processing loop, we only delay the input processing so the rest of the game can run at whatever frame rate it desires. Let's go over an example:
* Assume a setting where P1's ping to P2 is 90ms. This means that on average, we expect P2's inputs to be transmitted within 45ms. On a game operating at 60 frames per second, this means our expected delay is around 3 frames.
* P1 presses jump on frame 1.
* This input is not animated immediately in online play because the local game does not have information on whether P2 performed any actions during that same frame.
* Instead, P1's jump input is delayed for 3 frames and animated on frame 4 alongside any actions that P2 had taken.

At first glance, the solution seems reasonable; a 3 frame delay is typically not noticeable to the untrained eye, since the average reaction time is around 16 frames. However, this is only the case if the network latency remains consistent between two peers (i.e. remain at 90ms for the entire duration of the match). Unfortunately, this is often not the case. As a result, when latency spikes, P1's game now has no choice but to pause the game entirely until it has information from
both players. We can optimize the delay-based solution by monitoring the network connection and change the delay to match the network connection. 
