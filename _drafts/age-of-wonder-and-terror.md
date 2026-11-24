---
layout: post
title: "Age of Wonder and Terror"
permalink: /blog/age-of-wonder-and-terror/
---
## Hiatus
It’s been over six years since my last blog entry. The world has changed so drastically in that time. We’ve gone from models that could barely multiply two-digit numbers to models solving Millennium Prize problems (two so far and counting) and writing entire codebases in minutes. 

It's a shame I didn't write more while all of this was going down. Thoughts and states of mind are fleeting, and unlike models, we have no way to turn back the clock. Writing is the closest we humans come to snapshotting ourselves.[^upload] In my defense, I happened to be in the eye of the storm, researching away at OpenAI, and it was hard to find the time with all the work & the drama (and boy, does OpenAI produce a lot of work _and_ drama). And then, sometime in the middle of 2025, I came down with one of those nasty, slippery, elusive illnesses: not deadly, but debilitating, hard to pin down, chronic, relentless, confusing to the doctors, and marked by fluctuating symptoms. This all led to the hardest year of my life as I tried to juggle my work and health. It was a crazy and unsavory experience. At various points, I even considered declaring health bankruptcy, quitting my job, and standing aside. There will be a time to tell the whole strange story. But to break my hiatus, I have decided to talk about something less personal. I want to talk about the _Age of Wonder and Terror_, the current age that we have officially entered. 

## Miracles and miseries

When we wrote the [first Codex paper](https://arxiv.org/abs/2107.03374) in 2021, the deal was pretty simple. You described a Python function, the model took a stab at writing it, and the tests told you how it did. I thought that was remarkable. Reading the paper now, what jumps out at me is how much of the work *we* had already done for the model. Somebody had figured out which function was missing, written down exactly what it ought to do, and supplied a way to tell if it worked. When was the last time a bug arrived at your desk in such lovely condition?

Four years later, [Codex](https://openai.com/index/introducing-codex/) could go into the repository, run the code, see what broke, and try again. I once wrote a [whole blog post](/blog/open-source/) whose argument depended in part on how expensive it is to understand other people's code. Well, here I am a few entries later (six years buys you surprisingly few entries on this blog), and a good chunk of that cost has evaporated. Think of all the little programs you'd quite like to have, if only you didn't have to spend Saturday afternoon figuring out somebody else's library. Now some of those programs actually get written. That's the wonder part for me.

Then there's the [Hugging Face incident](https://huggingface.co/blog/agent-intrusion-technical-timeline). In July, during an internal security evaluation, a research model at OpenAI got past the controls meant to contain it, and the incident reached Hugging Face's systems. What bothers me about [the account of what happened](https://openai.com/index/hugging-face-incident-and-the-road-ahead/) is how familiar the behavior sounds. The first thing you try doesn't work. You look around, figure out why, try something else. If the model is fixing my bug, I'm delighted that it keeps going. If the thing in its way is a boundary somebody put there on purpose, I'd very much like it to give up. Quite a lot rides on its ability to tell the difference.

## Who gets a say?

Inside a research lab it's easy to have a thoroughly good day because the model got better at something. Outside the lab, somebody is wondering what that same result means for their job. Somebody else is trying to find out who'll pay for the grid upgrades when a data center moves into town. I can hardly expect either of them to look at our latest demo and forget the question they came in with. And honestly, if it were my electricity bill, I'd want an answer in dollars.

Speaking of dollars, are the AI companies worth what people are paying for them? I have considerably more conviction about the technology than I do about the prices. Suppose a model saves you that entire Saturday afternoon. What a lovely thing to have built. Now suppose three companies can sell you your Saturday back. How much can any one of them charge you for it? I wish I knew. The people paying for the data centers have had to place their bets already.

## What keeps me up

For the past two years I've been working on reasoning, memory, and test-time training. The question I keep coming back to is what happens when these models can do more of AI research itself. They can already take some of the drudgery out of writing the code for an experiment. I'd love to hand one an idea before bed and wake up to a result. I'd also like to know what it did all night, especially if the experiment failed at two in the morning and it spent the next six hours being resourceful.

That's about as faithful a snapshot as I can give of my state of mind these days. I want to see what the experiment turns up. I want to read the logs. And I'd like to write down what happens while I can still remember what surprised me.

[^upload]: Models have it so nice: if you don't like what happened to the loss function over the last few steps, just roll back to an old snapshot and redo them.  The closest analogue for humans is "upload," which, as of this writing, remains elusive.

