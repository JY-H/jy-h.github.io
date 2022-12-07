---
layout: post
title: "Coercing OpenAI's ChatGPT to do Leetcode :smiling_imp:"
author: "JY"
tags: [software, leetcode, chatgpt, openai, chatbot, interviewing]
---


Over the past week, I've been bombarded with messages by friends about OpenAI's new chatbot, ChatGPT. If you haven't heard of it yet, check it out [here](https://openai.com/blog/chatgpt/).

One of my friends was excited about it simply because he no longer has to draft paragraph-long responses to annoying client questions himself; he can just have chatGPT do it. My coach asked the bot 'how to be a great boxer', and it even noted the importance of mental and emotional strength. I like this bot already.

With that said, I wanted to put ChatGPT through a far less useful, but far more entertaining endeavor -- Leetcode-style interview questions.

### Leetcode for the Uninitiated
Leetcode, at its core, is just a platform where you can solve programming puzzles of varying difficulty that cover a wide range of data structures and algorithms topics. These types of programming questions are often asked as part of a rigorous, multi-part, interview process by most technology firms. Used generally, the term “Leetcode” (or, in its verb form, to Leetcode) doesn’t necessarily refer to that particular platform. Rather, it refers to a range of platforms—including Hackerrank, CodeSignal, etc, which are all meant to help you practice solving technical interview problems and coding puzzles. From now on, we will refer to these programming puzzles on various platforms simply as "Leetcode-style" problems.

A "full-loop" of interviews at a mid-to-large sized firm will typically involve the following:
* Any combination of: online assessments (a few hours, containing 2-4 Leetcode questions of varying difficulty), one or two technical phone screens (containing similar programming puzzles, just with someone else at the other end of the connection), a take-home project (often taking half a day to a full day to complete).
* A full day of interviews containing: 2-3 Leetcode-style questions, and other interviews such as system design and experience interviews.

As you can see, in order to pass a full loop of interviews, you need to be at least comfortable with these "Leetcode-style" programming puzzles. There's a lot of debate surrounding whether asking these types of questions is a good way to gauge a candidate's abilities, but we will table that for another time.

### Why am I doing this?
Because I can, and because I just had to go through quite a few of these interview loops myself, so I want to exact a similar level of pain and suffering while doing no harm to any human.

(Joke's on me though, read on).

### The Interview Problem
Here's the interview problem:

<pre  style="white-space: pre-wrap; word-break: keep-all;">
There are some numbers which can be rotated 180 degrees and still be the same number. Write an algorithm that determine whether a number is rotatable by this definition.
</pre>

This is the first part of an old (now retired) interview problem that we used to use at Google. You might find this problem a little vague, and that is intentional. A reasonable candidate will clarify what I meant by rotation, to which I will say: 

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


### Our Candidate: ChatGPT
#### Understanding the problem
To start, I subjected ChatGPT to the exact a similar experience as what I would expect with human candidates, with a few alterations such that no code is written until a good understanding of the problem statement has been developed.

Here's what happened:

Insert video here.

We had a nice little chat, but it's clear that the bot was quite set on the idea of "symmetry", regardless of the number of counter-examples I try to give it. I realized that I needed to be a little more rigorous in the initial problem statement, so this time, I gave it the full problem statement, include the definition of "rotation" above. Once again, we fail quite spectacularly as the bot continues to apologize profusely for its misunderstanding, while also not actually correcting its definition of "rotatable numbers".

Insert video here.

As you can see, the bot will adjust its understanding, but fail to provide examples with more than 5 digits that adhere to the problem requirements. At this point, I come to the realization that in addition to providing counter-examples, I needed to tell the bot why the example violated the problem statement. For example, I needed to explicitly state that ```2 is not a rotatable digit because looking at it upside down would give us gibberish```.
