---
layout: post
title: "Understanding the Mechanics of a Game Bot: Memory Editing"
author: "JY"
tags: [code, software, games, mmorpg, world of warcraft, wow, bot, hack]
<image: wow.jpeg>
---

Back in June of last year, Blizzard announced that it had suspended over [74,000](https://us.forums.blizzard.com/en/wow/t/actions-taken-to-address-exploitative-gameplay/558339){:target="_blank"} _World of Warcraft Classic_ accounts that were found to be automating gameplay via botting. Bots attack the integrity of a game like WoW (stealing resources, inflating the server economy, monopolizing rare drops) that is built on time investment and patience. When I read this
announcement, I was, to some extent, amused. After all, the game has been around for over 16 years, yet the botting problem appears to be one of the same.

So I got curious. How do you build a bot for a game like World of Warcraft? What about aimbots for FPS games? This will likely be broken up into a series of posts because there's a lot of interesting shit to talk about. For now, we will go over the basics of memory scanning and editing.

### Terminologies
Per usual, let's introduce some basic terminologies to make future discussions a little bit easier.

#### Game Loop
The game loop is the overall flow control for the entire game program. At each turn of the loop, the game processes user inputs, updates the game state, and renders the game.

#### Virtual Memory
Virtual memory is a bit of an overloaded term.

In the technical sense, virtual memory is a memory management scheme whereby the operating system provides programs with an idealized abstraction of the available physical resources on a given machine. In this scheme, every process has its own virtual address space, and memory addresses in that address space are mapped to physical memory addresses by the kernel via techniques beyond the scope of this post. If you're curious, I recommend reading
[this](http://pages.cs.wisc.edu/~remzi/OSTEP/vm-paging.pdf){:target="_blank"}.

In the non-technical sense, virtual memory is disk space used in place of RAM when compensating for shortages of physical memory (data are transferred from RAM to disk).

In our case, we are interested in the technical defnition. This is important because we need to know what the address space looks like for memory editing purposes.
