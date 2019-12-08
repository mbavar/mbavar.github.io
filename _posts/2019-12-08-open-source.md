---
layout: post
title: Open Source as Business
tags: [open source, podcasts]
---
Since the start of my work in tech, I've been fascinated with *both* the technological and the business aspects of it. A big open problem in the latter for me has been about the open source businesses. I love the concept. The sense of community and working together with a world-wide team on a common goal, sharing knowledge, writing great code and sharing it freely, it all sounds amazing. And I low-key think a lot about working on a major open source project in the future.

What I didn't get is how it worked as a business. Sure, Google and Facebook can open source *Chromium*, *Golang*, *React*, etc. because they have different cash cows and are not trying to extract revenue from these projects. But how could startups dwell in this and even base their major offerings on open source? Why would these cash & resource strapped entities give away most of their work for free? It seemed to defy logic and go against the invisible hands of Adam Smith and nobody goes against Adam Smith for a long time. Anyway, while listening to this podcast [episode](https://a16z.com/2019/10/21/free-software-and-open-source-business/) from a16z, things started to make sense to me. Here, I will try to capture my perspective in a fancy Socratic fashion for your pleasure. 

***Me***: *Why would anyone pay for your stuff if most of your features are open source? Why wouldn't the customer just fork it and use it?*

**Ans**:  Suppose,  I release 90% of the features and keep 10% proprietary. Who has the advantage in building that 10% without creating an absolute mess? People who wrote the 90% of the project and have been with the project all along or some random engs this company re-orged or threw into this. Remember, to write that 10% you need to READ A LOT of code (i.e. most of the 90%). You know how hard reading other people's code is? Why bother with that when you can buy the the whole service from us (including the proprietary 10%) instead of dealing with a nightmare internal project.

Furthermore, think of running and supporting all that code written by someone else 🤣 good luck with that!

***Me***: *Okay that's reasonable. But still I can't help but wonder... Why release the 90% to begin with? Why lose all the money you could've got from the customers who are happy with just the 90%? Why not just keep everything closed source to eliminate possibility of competitors getting fruit of your labor for free and to also take the money from freeloaders. What are you achieving by being open source? Are you trying to be cute? (Or are you secretly a communist? :P)*

**Ans**: Hadoop, Spark, Kubernetes, Linux wouldn't have reached the massive scale of today unless a lot of people could engage with them freely and use them in smaller projects without having to buy. Basically, in the words of [Ali Ghodsi](https://en.wikipedia.org/wiki/Ali_Ghodsi) Open Source is the best lead gen machine there is. Once an engineering org starts using your work for tiny projects and likes it, it's a small step from there to its use at a more massive scale, where they would likely need all the enterprise feature and support offered by your  premium version of the product 💸 On the other hand, if your project was fully closed-source, chances are it would've died in obscurity to begin with.

***Me***: *I see. So basically Open Source follows a freemium business model. But still... I fear the competition you're signing up for by open sourcing so much code. Once your project has momentum, what stops a dedicated company in a close vertical to jump in and offer a similar product on top of the project you labored for so hard.*

**Ans**: This is subtle and actually happens often. But... any good market will inevitably draw competition. That being said, a lot of the time the original team has a lot of advantages over the new entrants. Beside the first mover advantage, you may also have the founding team and core contributors in the project in your team. This is a **massive** advantage. They are the ones that control what gets merged in the main OSS repo, i.e. they approve pull requests and overall product roadmap. They have the best insight into the existing codebase and can see difficulties and weed out hopeless or dumb features far in advance. The competitor can do what it wants with its fork but if the community is engaged and obsessed with your version of the project, they would have a hard time keeping up.   
Another advantage of having the founders of the OSS in your company is in *sales*. Think of the customer. Would they prefer to go with the company which boasts the services of the original founders of the project who have been at it for years or some new entrant who's trying to hustle its way only after it started to smell the money? For most people, the answer is the former! 

<!--
Of course everything is not going to be easy. The new entrants might have existing relationships with customers from their other products, and overall better distribution, or even bundling offers or just a lot more money than you. But the above should convince you that the founders of OSS still have a lot of ammunation once things start getting heated. 
-->


