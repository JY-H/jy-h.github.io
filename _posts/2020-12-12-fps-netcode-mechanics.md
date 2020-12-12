---
layout: post
title: "Understanding Netcode Mechanics: Lag Compensation in FPS Games"
author: "JY"
tags: [code, software, smash, fighters, fighting games, delay-based netcode, rollback netcode, netcode, fps, games, shooters]
---

Since the start of quarantine in March, my coach has dragged me to play smash ultimate. We have been playing for an average of an hour per day, everyday, for the past nine months. Suffice to say, as we got better at the game, frustrations grew when the occasional lag spike ruined a tightly contested match.

As someone who grew up playing MMOs and FPS games, my mental model of game networking was that of a dedicated game server network model, where dedicated machines provide enough bandwidth to handle all players that connect to it. This network model is expensive to maintain (you either need to own data centers, or contract it out to tech giants who offer cloud services), but makes it easy to put forth optimizations like restrictions against high-ping players, anti-cheats, etc, which are indispensable to modern gameplay. Fighting games on the other hand, utilize a peer-to-peer network model. Smash in particular, uses delay-based netcode. More on that later.

In any case, I realized that I understood very little about how game networking works, hence began my journey in attempting to understand netcode mechanics. Today we will focus on netcode mechanics in the dedicated game server model typically used by MMOs / FPS games.

### Basic Terminology
Here are some basic terminology that will keep us on the same page if you decide to follow me down this rabbit hole :)

* Netcode: An umbrella term for game networking.
* Ping: The time between sending a request to a server and receiving the response. Your proximity to the game server, as well as routing (as the data packet does not take a direct path, but rather takes a few hops before it arrives at the destination) determines your ping.
* Update rate: The frequency at which the client can process updates from the server.
* Tick rate: The frequency at which the server updates the game state.

Note that both the client's update rate and the server's tick rate need to be high for the game to feel smooth. For example, if the client's update rate is 20, even if the server's tick rate is 64, the client will not be able to process the updates quickly enough for the tick rate to matter. Similarly, simply increasing the tick rate or update rate will not resolve delay caused by a high ping. This is because changing the frequency at which updates are sent does not affect the distance that the
packets must travel.

### How Things Work
For now we will follow an extremely simple mental model of two clients, player A and player B, and a single central server.

#### Interpolation
Let's say that the server receives data from player A on its positioning, and sends that data to player B at a rate of 64Hz. If we assume that player B has a client update rate capable of handling the received data, the game would still be unplayable without additional processing because in the view of B, A would simply be teleporting to a new position every ~15 milliseconds. The game would be choppy and virtually unplayable.

To make the game playable, you must smooth out this movement via a technique called entity interpolation. In order to animate movement, we need to know the starting position as well as the ending position. This means that the information a client receives from the server is held for a tick before it is animated on the screen. This causes delay in addition to the client-server ping delay. We call this the interpolation delay.

Ultimately this means that every player in the session sees a slightly different rendering of
the game world, where each player sees itself in the present, but sees other entities in the past.
For those who are mathematically inclined: the latency between the canonical server game state and an individual player's game state is `Latency = (1/2 * Ping_player) + interpolation_delay_player`, where `interpolation_delay_player` is typically two update rate ticks of the player client in order to protect against packet loss.

#### Lag Compensation
Since each player is affected by a certain amount of latency, resulting in a different individual game state for each player, the game server must compensate for the latency to simulate damage. Otherwise, you would have to shoot ahead of the player's current displayed positioning, almost like [Shroud's insane PUBG longshots](https://youtu.be/hV-4cuzK1wE?t=13){:target="_blank"}, except you would need to do this magical deed even for the players standing right in front of you.

So the game server does something called lag compensation, wherein the server accounts for clients' latency values to determine the canonical positioning of both players at the time an action is taken to determine whether the action has inflicted any damage. Let's demonstrate this with an example:
1. Player B comes into the view of player A.
2. Player A fires a shot, and the client sends this information to the server.
3. After `1/2 * Ping_A`, the server receives information that player A has fired a shot. Assuming Player A doesn't have horrible aim like I do, the server looks at player B's position in `T - (1/2 * Ping_A) - interpolation_delay_A`, determines that shot intersects the hitbox, and calculates the damage dealt.
4. In the next server tick, the server relays this information back to both players. After an interpolation delay, player A sees the damage dealt (e.g. via blood splash), and player B sees their health reduced.

If the latency for both players are similar and low, then the gameplay would feel natural. However, this can be problematic if say, player A's ping (135ms) is significantly higher than that of player B (7ms), since player A's latency is high enough for player B to perform multiple actions within the time frame of `1/2 * Ping_A`, before the results of A's actions are relayed. This means that player B might see the damage being dealt despite already having hidden behind cover. You can see this demonstrated in [this video](https://youtu.be/-Wk9lW8zQLk?t=375){:target="_blank"}.

While this would be tilting, the player with the lower ping will still have an advantage. Generally game servers will accept the results of the first action received as the canonical one. So in the above scenario, if both players shot and hit each other, player B, with its lower ping, would receive the favorable decision. A good strategy to prevent overcompensation for players with extremely high ping is to simply put a threshold on the amount of lag compensation done by the
game server. In other words, instead of looking back in time for game state to `T - 1/2 * Ping_A`, look back to `T - min(threshold, 1/2 * Ping_A)`.

### What does all of this mean for the player?
* Tick rate will not solve all of your problems: There's often a lot of talk in online communities about server tick rates. Blizzard took a lot of heat for Overwatch's server tick rate back in 2016. However, the predominant factor in high latency will more likely than not still be the ping, or sometimes issues within the player's the local network if the player is using WiFi. Think about it this way -- your ping is the minimum lag, and tick rate / update rate changes only
affect the additional delay on top of your ping delay.
* "High speed internet" does not mean low ping: "High speed" often refers to bandwidth (or the capacity of your download / upload stream). Your downloads are faster because you can download more data at a given velocity. In gaming, the packets will be relatively small, so it is the velocity of the transfer that matters.

_P.S.: For those interested in actual numbers, here's a video which tested tick rate and update rate changes in CSGO and measured the latency results. The graph of interest is at [timestamp 11:38](https://youtu.be/pHi2DfSFFpk?t=698){:target="_blank"}_.
