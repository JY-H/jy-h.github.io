---
layout: post
title: "Coercing OpenAI's ChatGPT to do Leetcode :smiling_imp:"
author: "JY"
tags: [software, leetcode, chatgpt, openai, chatbot, interviewing]
---


Over the past week, I've been bombarded with messages by friends about OpenAI's new chatbot, ChatGPT. If you haven't heard of it yet, check it out [here](https://openai.com/blog/chatgpt/).

One of my friends was overjoyed that he no longer has to hand-craft paragraph-long responses to annoying client questions; he can just have ChatGPT do it. My coach on the other hand, asked ChatGPT 'how to be a great boxer', and it noted the importance of mental and emotional strength, in addition to the physical training. I like this bot already.

With that said, I wanted to put ChatGPT through a far less useful, but far more entertaining endeavor -- Leetcode-style interview questions.

### Leetcode for the Uninitiated
Leetcode, at its core, is just a platform where you can solve programming puzzles of varying difficulty that cover a wide range of data structures and algorithms topics. These types of programming questions are often asked as part of a rigorous, multi-part, interview process by most technology firms. Used generally, the term “Leetcode” (or, in its verb form, to Leetcode) doesn’t necessarily refer to that particular platform. Rather, it refers to a range of platforms -- including Hackerrank, CodeSignal, etc, which are all meant to help you practice solving technical interview problems and coding puzzles. From now on, we will refer to these programming puzzles on various platforms simply as "Leetcode-style" problems.

A "full-loop" of interviews at a mid-to-large sized firm will typically involve the following:
* At least one, but any combination of: an online assessment (a few hours, containing 2-4 Leetcode questions of varying difficulty), one or two technical phone screens (containing similar programming puzzles, just with a human at the other end of the connection), a take-home project (often taking half a day to a full day to complete).
* A full day of interviews containing: 2-3 Leetcode-style questions, at least one system design question, and an experience / values interview.


As you can see, in order to pass a full loop of interviews, you need to be at least comfortable with these "Leetcode-style" programming puzzles. There's a lot of debate surrounding whether asking these types of questions is a good way to gauge a candidate's abilities, but we will table that for another time (read: never).

### Why am I doing this?
Because I can, and because I just had to go through quite a few of these interview loops myself, so I want to exact a similar level of pain and suffering while doing no harm to any actual human.

(Joke's on me though, read on).

### The Interview Problem
Here's the interview problem that I've decided to use:

<pre  style="white-space: pre-wrap; word-break: keep-all;">
There are some numbers which can be rotated 180 degrees and still be the same number. Write an algorithm that determine whether a number is rotatable by this definition.
</pre>

This is the first part of an old (now retired) interview problem that we used to use at Google. I picked this problem because it is sufficiently simple to explain without use of excessive visuals (such as in graph or tree problems), while remaining the slightest bit ambiguous. As a reader, you may find this problem a little vague, and that is intentional. A reasonable candidate will clarify what I meant by rotation, to which I will say: 

<pre  style="white-space: pre-wrap; word-break: keep-all;">
To understand the rotation, imagine that we are writing the number on a piece of paper, putting the paper on the desk, and then rotating the piece of paper clockwise or counterclockwise by 180 degrees while keeping the paper on the desk. Does that make sense?
</pre>

I will then ask that they provide a few examples of rotatable numbers as well as non-rotatable numbers to ensure that they have understood the definition correctly. Candidates might ask some combination of a few more clarifying questions:
* Is the input represented as a string or as a number? (You can pick).
* Will there be any negative numbers? (No).
* Will 1 be written just like a stick, or in the fancy way? (A stick).
* ...

And on we go.

We expect that candidates should get through this first part of the problem rather quickly (no more than 15 minutes), in order to have sufficient time to discuss a more complex follow-up. I decided that this was a good enough starting point for ChatGPT.


### Our Candidate: Chatbot Boi
#### Understanding the problem
To start, I subjected ChatGPT to the exact a similar experience as what I would expect with human candidates, with a few alterations such that no code is written until a good understanding of the problem statement has been developed.

Here's what happened:

<iframe width="520" height="315" src="https://www.youtube.com/embed/NC3ngFeGDYk" frameborder="0" allowfullscreen></iframe>

We had a nice little chat, but it's clear that the bot was quite set on the idea of "symmetry", regardless of the number of counterexamples I try to give it. I figured that I needed to be a little more rigorous in the initial problem statement, so this time, I gave it the full problem statement, include the definition of "rotation" above. Once again, we fail quite spectacularly as the bot continues to apologize profusely for its misunderstanding, while also not actually correcting its definition of "rotatable numbers".

<iframe width="520" height="315" src="https://www.youtube.com/embed/dee1Oc6JHkk" frameborder="0" allowfullscreen></iframe>

As you can see, after each subsequent counter exmaple, ChatGPT claims that it understands, but fail to provide examples with more than 5 digits that adhere to the problem requirements. At this point, I come to the realization that in addition to providing counterexamples, I needed to tell the bot why the example violated the problem statement. For example, I needed to explicitly state that ```2 is not a rotatable digit because looking at it upside down would give us gibberish```. It was not able to make reasonable inferences based on the examples alone.


#### Writing the Code

After a bit more finagling, I finally got Mr.Chatbot to agree with me on a definition of rotatable numbers. From here on out, it's just writing the code, which should be a simple translation of understanding to algorithm, right?

Here's what happened...

<iframe width="520" height="315" src="https://www.youtube.com/embed/3Sj7Ce950xo" frameborder="0" allowfullscreen></iframe>

The initial solution was a good start -- it checks that all digits in the number are individually rotatable, but fails to check that after rotation, the number remains the same. As soon as I wrote out an counterexample and saw its subsequent response, I regretted hitting 'send' too quickly. My original intention of giving it a counterexample of 666 was to show that the corresponding rotation, 999, is not the same number. However, Chatbot boi took it to mean that there should not be duplicated digits :cry:. I tried to make a few more clarifications, but I think I broke him.

<iframe width="520" height="315" src="https://www.youtube.com/embed/eqjRXFDvpAU" frameborder="0" allowfullscreen></iframe>

Fine, my fault for not properly supporting my counterexamples -- I should have learned already. Let me do better. I will be the ideal interviewer. Be the change you want to see in the world, they say.


#### Successful Run :D
At this point we were three and a half hours in, and I needed to get to the gym. Fortunately, in this run, I made sure to back up my counterexamples with _EVIDENCE_, so Chat boi can make adjustments accordingly.

Here it is, in all its glory.

<iframe width="520" height="315" src="https://www.youtube.com/embed/Okqt2IsfjFk" frameborder="0" allowfullscreen></iframe>

### Conclusion
No doubt I played myself in this endeavor :face_with_head_bandage:. 

In all seriousness though, ChatGPT will definitely save some of us a ton of time when playing around with side projects, or even just learning new tooling as part of the job. It thrives when asked non-ambiguous, "how-to" style questions, but unfortunately is yet to make reasonable inferences that are a simple hop or two away.

That's probably a good thing :face_in_clouds:
