---
layout: post
title: "Game Streaming: How it Works and Who it's for"
author: "JY"
tags: [code, software, stadia, ps4, remote play, game streaming, streaming]
image: bt.png
---

The first time I experienced an instance of game streaming was when I asked my friend Zan to stream Titanfall gameplay. Rather than streaming directly to a platform like Twitch or YouTube, Zan turned on share play on his PS4, and in my bewilderment, handed me the controls to the game. At the time, I didn't have the slightest clue on how game networking worked, so I was amazed at the fact that I could control a character in a game I do not own, from 1800 miles away, in a relatively seamless
fashion.

Unfortunately aside from a few tutorials on how to setup SharePlay, there are no white papers out there explaining how the technology was built (which makes sense, given it's Sony IP). However, with a basic understanding of netcode models, we can establish a pretty good mental model of Playstation's SharePlay function. And this same model is at the core of game streaming technology.

### How it Works
#### Client Hosted Network Model
In my previous two posts on netcode mechanics, we talked about a dedicated server network model (commonly used by MMOs and FPS games), and a peer-to-peer network model (commonly used by fighting games). There is a third approach where the client or console of an individual player becomes the host of the game, making them the server that other peers can connect to. So in my experience above, my friend Zan's console became the server, and my console became a client to his console, receiving the game state and sending inputs remotely.

This is often confused with the peer-to-peer network model. The key difference here is that the player serving as the host does not have to send their inputs anywhere; so if two players are playing a competitive shooters games under this network model, the player serving as the host would have a significant advantage.

#### Game Streaming
At its core, a game streaming service is just a client hosted network with dedicated machines storing an extensive game library as the host. To list out a few examples:
* With Steam Link, your own gaming PC serves as the host and streams games over a home network to another device in the network.
* With Nvidia GameStream, your gaming PC (with a Nvidia graphics card) serves as the host and streams games over a home network to a Nvidia Shield TV set-top box.
* With PlayStation Now, Sony's servers serve as the host and streams games over network to subscribers' consoles.
* With Stadia, Google's servers serve as the host and streams games over network to subscribers' chosen platforms.
* ...

Note that the first two examples are simply streaming devices which make games more easily accessible, much like chromecast or airplay.

In essence, the technology has the same framework across the board regardless of which streaming service you pick, where the only distinguishing factors contributing to the gaming experience are processing power of the host machines and bandwidth of the network. This means that aside from ensuring that your internet service has sufficient bandwidth, you will want to pick the cheapest option with the most reliable and proximal set of hosts. 

### Who it's for
For the purposes of this section, we will consider only the game streaming services (and not the cast devices).

#### The Same Latency Problem
Game streaming services will present the same latency problems that we've discussed in previous articles on netcode. That is, a player's game inputs have to be sent over the network to the game server to be processed, and the resulting game state must be sent back over the network to the player to be rendered. The key difference is that with a game streaming service, the requirements for bandwidth is a lot higher because the entire game (including
audios and visuals) must be sent over the network, rather than just a carefully constructed package of in-game data.

In the case of an online multiplayer game, the perceived latency will not be all that different. In the case of singleplayer games, players might experience previously unseen input lag, as inputs now have to be sent over network rather than directly simulated on a local machine.

#### No More "Not Enough Free Space"
Every PS4 owner probably has seen this message at some point: "Cannot download X because there is not enough free space." And so we are often forced to delete some old games in order to make space for a shiny new game, even on 1TB of storage.

Game sizes nowadays regularly exceed 100GB: Red Dead Redemption 2 for example, sucks up 150GB, and Call of Duty Modern Warefare takes around 230GB of storage, excluding patches. This makes streaming services a more attractive option for those who don't have the disk space to spare on their PC, or are simply too tired of deleting and reinstalling games on their console.

#### Hardware "Paywall"
Gaming can be an expensive hobby. Consoles and gaming PCs / laptops are expensive and only represent a fraction of the cost of entry (e.g. a solid gaming setup will cost you at least $600 if you build it yourself). Each current AAA title will cost you another arm and leg, and your gaming hardware will need to be upgraded every few years to keep up with the hardware specifications required to run newer games.

As streaming services solidify their spots in the gaming industry, an increasing number of major publishers are releasing AAA games on streaming platforms such as Stadia. This makes streaming services an extremely simple path of entry into experiences that are typically locked behind a $500+ paywall.

Moreover, streaming services will offer better game sharing experiences as there is no need for a shared physical machine. Stadia, for example, lets you share games with "family members". This means that effectively a single person in a friend group can get Stadia Pro or any given game, and spread the cost with a group of five other friends, making it an even more affordable experience.

#### Bottom Line
A game streaming subscription might be a good idea if more than one of the following items ring true:
* You do not own current-generation gaming setup or consoles.
* You game casually and do not care to play platform exclusives.
* You mainly play singleplayer campaigns.
* You do not play online multiplayer competitively. The term teleporting mean nothing to you, and you do not understand people who rage at the occasional lag spike.
