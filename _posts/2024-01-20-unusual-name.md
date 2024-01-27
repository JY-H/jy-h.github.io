---
layout: post
title: "What is the cost of an unusual name?"
author: "JY"
tags: []
---

Imagine this.

You are sitting in a social Zoom call with a few coworkers with whom you've had limited interactions.

You each introduce yourself, talk about what you work on, and what you like to do outside of work. Conversations, though somewhat awkward, begin to flow. A few minutes go by, you hear John call out to you, with what seemed to be a butchered attempt to pronounce your name. You pause for a moment, just to confirm that the question was indeed directed towards you before answering.

You don't attempt to correct the mispronounciation. <i>Because that would be incredibly rude</i>, you think to yourself.

...

If this feels familiar to you, then congratulations, you probably have an unusual name. I found myself in this exact situation a few weeks ago. I didn't think much of it in the moment, but after exiting the meeting, I found myself distracted by the following sequence of thoughts:
- <i>How many variations of my name have I heard at this point?</i>
- <i>At what point did I stop trying to correct folks?</i>
- <i>I applaud John for trying, but how many people unconsciously avoid engaging with me in meetings simply because they cannot pronounce my name?</i>
- <i>How many people have I unconsciously avoided engaging with because I cannot pronounce their names?</i>

These thoughts haunted me for the rest of the workday, so I decided to do some research.


### 2023 [Publication](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4031991) on Impact of Name Fluency on Labor Market Outcomes for economic PhD job candidates
Qi Ge, an Associate Professor of Economics from Vassar College, teamed up with a fellow economist, Steven Wu, from Hamilton College, to dig into a similar set of questions. In their research, Stephen and Qi curated a list of 1500 names of graduate students who were getting their PhD in economics and who were actively searching for a job to understand the implications of name fluency on these students' success in the job market.

To begin, they needed a way to measure name fluency. To do this, Qi and Stephen used several methods.

First, the researchers developed an algorithm to rate the difficult of pronouncing words from the vantage of a native English speaker. The algorithm develops two measures of difficulty in pronouncing words: one based on sequence of letters and the other based on sequence of phonemes, or distinct units of sound. Occurrence dictionaries of common letter and phoneme combinations were then used to develop one difficulty rating based on the frequency of letter combinations and another rating
based on the frequency of phoneme combinations embedded in various names. This was done for both first names and last names.

#### N-gram Model
Based on the attached code in the technical appendix of the paper, the algorithm relied on using N-grams models, which is commonly used in Natural Language Processing.

N-gram is simply a sequence of N words. For example:
1. New York, is a bigram (or 2-gram).
2. New York City, is a 3-gram.
3. I have failed spectacularly, is a 4-gram.

You may have seen (1) and (2) more frequently than (3). So (3) is an example of an N-gram that has a lower probability of occurrence. If we assign a probability to the occurrence of an N-gram, it can be very useful in say, predicting the next word, or correcting spelling mistakes. Consequently, an N-gram model is simply a model that predicts the occurrence of a word based on the occurrence of its previous N-1 words. This is the basis for things like Gmail's sentence suggestions as you typed an email.

In the case of the research, the unit of "gram" is letters or phonemes, instead of words. If a model has been trained to predict common letters or phonemes that follows a prior letter or phoneme, then we can just as easily understand when a combination is unusual, and therefore likely more difficult to pronounce.


Continuing with the research, the algorithm was not the sole method used in determining name fluency. In addition to the objective method of running names through an algorithm, Qi and Stephen also used two subjective methods of measurement. In one case, they hired ten undergraduate resesarch assistants to click through a survey of names, pronouncing the name and then immediately clicking onto the next page. The survey contained timing functions to understand how long a person spends on a page -- the longer someone spends on a page, the more likely the name was uncommon. A second subjective method merely involved asking people to rate a name as "easy" or "difficult" to pronounce.

#### Results
The results presented in the paper were rather damning -- those with harder to pronounce names were 10% less likely to land an academic job; for those applying for private sector jobs, candidates with harder to pronounce names got fewer callbacks from employers.

Moreover, when these candidates did land positions, they often ended up in less prestigious positions. Qi and Stephen noted in addition that the penalty for name fluency was less severe for PhDs who came from a top-20 research university.

This was a pretty depressing paper for me to read. Although the research left some biases unaccounted for (as an example, we do not have insights into the background of the research or hiring committee admitting candidates), the results suggest that there are definitively some level of cognitive bias against those with more uncommon names.

### 2017 University of Toronto [Research](https://hireimmigrants.ca/wp-content/uploads/Final-Report-Which-employers-discriminate-Banerjee-Reitz-Oreopoulos-January-25-2017.pdf) on Impact of Organization Size on Treatment of Racial Minorities
I will admit that after reading the previous rather depressing, 80-page report, I only read the abstract on this one 😅.

Here, the researchers performed an audit by submitting around 13,000 computer-generated resumes to a sample of 3,225 jobs offered online in Toronto and Montreal. The research concluded the following:

> An organization-size difference in employer response to Asian names on the resume exists when the Asian-named applicant has all Canadian qualifications (20% disadvantage for large employers, almost 40% disadvantage for small employers) and when they have some or all foreign qualifications (35% disadvantage for large employers, over 60% disadvantage for small employers). Discrimination in smaller organizations is most pronounced in considering applicants for jobs at the highest skill levels.

Having experienced hiring practices in both larger and smaller organizations, this conclusion did not come as a surprise. Larger organizations often have hiring practices that involve unconscious bias trainings, writing interview feedback that involve removing markers that may reveal information such as gender or race. Smaller organizations simply do not have the resources to standardize these practices.

### Closing Thoughts
I went down a bit of a rabbit hole on this one 🙃, ending with no answer better than "it probably is happening to some extent" to my set of original questions.

In any case, noting when these sets of biases are occurring, and shamelessly forcing more interactions with those people is likely the simplest starting point.
