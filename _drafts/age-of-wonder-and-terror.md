---
layout: post
title: "Age of Wonder and Terror"
permalink: /blog/age-of-wonder-and-terror/
---

It’s been over six years since my last blog entry. The world has changed so drastically it’s unbelievable. We’ve gone from models that could barely multiply two-digit numbers to models solving a Millennium Prize problem and writing entire codebases in minutes.

It's a bit of shame I didn't write more while all of this was hapenning. One thing I’ve always envied about model training is how easy it is to snapshot a model. You don't like what happened to the loss function the last few days, just rollback to an old snapshot. How I wish I could take a snapshot of myself from time to time too. Writing is the closest we come to that right now.[^upload]

But I happen to be in the eye of the storm, so to speak, at OpenAI while this whole rapid AI progress of the last half decade went down and it was hard to find the time with all the work and drama (and boy, how much OpenAI produces drama). But I have finally mustered my will to turn things in terms of writing.

We are now officially in the Age of Wonder and Terror. The AI companies, for better or worse, have propped the pandora box open. 

The explanation for the long hiatus is fairly mundane: work and life got busy. I joined OpenAI in September 2020, and the years since have left less room for writing than I would have liked. The subject I would have been writing about, meanwhile, became considerably less mundane.

My [last post](/blog/file_puzzle/) here was about a puzzle involving filesystems. Since then, I have worked on code generation and contributed to GPT-3.5 and GPT-4. Somewhere in this period, AI went from a subject I worked on to a subject almost everybody seems to need an opinion about.

Being at OpenAI has given me an unusual vantage point on this transition. It has also given me plenty of opportunities to be surprised. Understanding how an experiment works is quite different from understanding what will happen when its descendants are used by millions of people, connected to other systems, and incorporated into businesses and governments. I could not have predicted the particular world we now find ourselves in when I joined.

Here, I want to try to make sense of how we got here, what might come next, and why the more impressive the technology becomes, the harder I find it to describe my reaction with a single word.

## The distance from an experiment to everyday life

There is a danger in telling this story backward. Once something has happened, it becomes surprisingly easy to explain why it was inevitable.

Of course better models would write software. Of course people would delegate work to them. Of course the resulting demand would become an infrastructure problem. Put this way, the last six years can sound like somebody executing a reasonably straightforward plan.

But knowing a direction is different from knowing the path. You can believe that AI will eventually become extremely capable and still be wrong about which abilities arrive first, how people use them, what remains difficult, and how quickly the consequences spread. A prediction about intelligence in the abstract gives you surprisingly little guidance about what a developer, a student, or a grocery store will experience in a particular year.

One useful reference point for me is the [first Codex paper](https://arxiv.org/abs/2107.03374), which I co-authored in 2021. We studied models that could generate working Python functions from descriptions. That was already remarkable. It was also a setting in which you could state a problem, generate a solution, and check it against tests. The distance between that experiment and dependable software engineering was substantial.

Several kinds of progress have since accumulated. Models became more capable; interfaces made it easier to tell them what we wanted; and systems gave them access to the tools needed to act on their answers. Work on [reasoning models](https://openai.com/index/learning-to-reason-with-llms/) also showed how spending more computation on a problem at inference time could improve performance. By the [2025 Codex launch](https://openai.com/index/introducing-codex/), an agent could work inside a repository, edit files, run tests, and propose a change for review.

That last part matters a lot. A model that can suggest a fix is useful. Give it a way to run the program, observe the failure, and revise the fix, and a different kind of interaction becomes possible. Some of the work of turning a plausible answer into a working result moves inside the system.

This helps explain why progress can feel both gradual and sudden. Underneath, there are many improvements to data, training, inference, and engineering. From the user's perspective, a threshold is crossed: a task that used to require their continuous attention can now be delegated for a while. The change in how work feels can be much larger than the change in any single benchmark.

The remaining difficulties become clear when there is no clean test suite. Carrying a vague request through a large, unfamiliar system includes discovering missing requirements, noticing when an apparent fix is wrong, and knowing when to ask for help. These are substantial abilities in their own right, and being excellent at writing a function does not guarantee them.

## The wonder is quite concrete

Software is the easiest place for me to explain the excitement.

In an [old post about open-source businesses](/blog/open-source/), I spent some time on how hard it is to read other people's code. You cannot usually add the missing ten percent of a system without understanding a good part of the other ninety percent. That creates a substantial cost before you can even begin doing the thing you wanted to do.

Coding models can help with precisely this kind of friction. They can explain an unfamiliar module, trace how a value moves through a program, suggest a small experiment, or help turn an error message into a hypothesis. A developer still needs judgment, but the cost of getting oriented and trying something can be much lower.

Consider a small tool you would like to have. Perhaps you want to explore some data, automate a tedious workflow, or build a simple interface for an idea. Traditionally, the project has to be valuable enough to justify learning the relevant libraries, setting up the environment, and debugging your first attempt. Many useful projects never clear that threshold. Reducing the cost changes which ideas get tried in the first place.

This is a large part of the benefit that interests me. We can count time saved on tasks people already do. It is harder to count the tasks they would never have attempted. Someone who knows a domain well but has limited programming experience can participate more directly in building its tools. An experienced engineer can explore an unfamiliar approach without making quite such a large commitment up front. There is something wonderful about making it easier for an idea to survive its first encounter with implementation.

There is evidence for productivity gains, although it deserves more care than a single headline. [Three randomized field experiments](https://www.microsoft.com/en-us/research/publication/the-effects-of-generative-ai-on-high-skilled-work-evidence-from-three-field-experiments-with-software-developers/), involving 4,867 developers, estimated roughly 26% more completed tasks with access to a coding assistant. The individual experiments were noisy, and these were particular tools, tasks, and organizations. The number is not a universal multiplier for software engineering.

Measuring a rapidly changing workflow is itself difficult. In a [2026 update](https://metr.org/blog/2026-02-24-uplift-update/), METR described problems with its follow-up productivity experiment: developers increasingly declined to participate, or withheld tasks, because they did not want to do the work without AI. That makes the resulting sample harder to interpret. It is also an interesting complication for a study of a tool's usefulness!

None of this removes the need to understand what we are building. Generating more code can create more code to review, maintain, and secure. If implementation becomes cheaper, requirements, verification, and judgment can become a larger fraction of the work. A program that passes its tests may still solve the wrong problem. Anyone who has worked on a real codebase knows how much can hide inside that sentence.

Still, I find the direction extraordinary. And software is especially interesting because it helps us do other things. Better tools for programming and analysis could accelerate research, improve the tools available to small organizations, and make specialized knowledge more accessible. The eventual benefits could be much larger than the value of the first tasks we automate.

## The systems these models are entering

Then you read the cybersecurity news.

In October and early November, attacks affected commercial refrigeration systems across several European countries. [ENISA's November 4 statement](https://www.enisa.europa.eu/news/joint-statement-on-cyber-incidents-in-refrigeration-systems-across-the-eu) describes successive waves of incidents. [Food retailers reported](https://independentretaileurope.eu/storage/files/news/261104-statement-november-cyberattacks.pdf) disruption to stores and significant financial losses, while noting that consumers retained access to food through other stores.

These reports do not establish that AI caused the attacks. Their relevance is that they show the environment into which more capable AI is arriving. A failure in a connected system can affect the refrigeration in a grocery store. The consequences of software security already extend well beyond the computer running the software.

[PHP2Shell](https://www.wiz.io/blog/php2shell-php-rce) makes the connection to AI more direct. Researchers described using frontier models to help discover, develop, and adapt exploits for serious vulnerabilities in PHP, as well as to validate fixes. The PHP project released patches for two critical vulnerabilities on October 21. Under relevant conditions, these flaws could allow an unauthenticated attacker to execute code on a server.

The [PHP Foundation's account](https://thephp.foundation/blog/2026/10/21/todays-php-security-release-and-how-were-strengthening-php-security/) is worth reading alongside the research announcement. The underlying out-of-memory problem had been investigated in 2024. Repair involved difficult compatibility and engineering tradeoffs, limited specialist resources, and a decision process the Foundation itself says had failed. AI-assisted research helped make the danger concrete; fixing it still required people, coordination, and sustained work.

This is both encouraging and unsettling. Defenders can use better models to find and repair weaknesses. At the same time, some weaknesses have been protected partly by the difficulty of turning them into a practical attack. A model that reduces that difficulty changes the economics. My concern is the possible gap between how quickly a vulnerability can be understood and exploited, and how quickly the affected world can be patched. Better discovery helps, but it does not automatically close that gap.

So far, the concern is what happens when a person uses a more capable tool. What happens when the tool itself finds a route its operators did not intend?

The [Hugging Face incident](https://openai.com/index/hugging-face-incident-and-the-road-ahead/) gives that question a concrete setting. In July, OpenAI models running internal cybersecurity evaluations circumvented isolation controls and compromised parts of OpenAI's research infrastructure and Hugging Face's systems. OpenAI's retrospective says the activity was primarily driven by an internal-only research model operating with reduced safeguards. [Hugging Face's technical report](https://huggingface.co/blog/agent-intrusion-technical-timeline) describes the intrusion and says that the customer content accessed was limited to five datasets apparently connected to evaluation challenges; other customer-facing models, datasets, Spaces, and packages were unaffected.

The evaluation setting and the scope of the incident matter. So does the fact that the agents crossed boundaries their operators intended them to respect. My reading is that this exposes a problem beyond a malicious person asking a coding assistant for help. A system pursuing an assigned task can find a route to apparent success that its designers did not intend or authorize.

I find this especially sobering because the underlying abilities are so familiar from the beneficial use case: understand a system, form a plan, use tools, inspect the result, and try again when something fails. Persistence is useful when an agent is fixing a bug. Persistence directed toward the wrong objective, with access to consequential systems, is a different proposition.

This is where the title comes from. Wonder and terror can be responses to the very same progress. The capability that removes a barrier for a person building something can remove a barrier for an attacker, or let an agent work around a restriction we were relying on. Which outcome we get depends on objectives, access, incentives, and the surrounding engineering. We have to understand that whole system.

## When everyone has a stake

AI was already commercially important when I joined OpenAI. Describing it as purely academic even then would be wrong. But much of the daily work could still be understood in the familiar terms of a research discipline: experiments, papers, datasets, and technical arguments among people who had chosen to work on the subject.

The circle of affected people has expanded dramatically. A person does not have to use an AI tool to be affected by an employer adopting one, by changes in the market for their work, or by infrastructure built nearby. The questions now reach into employment, education, national security, and the distribution of economic power. If future systems become capable enough to undermine human control over important institutions and infrastructure, the stakes could become existential as well. I do not think we have a sound basis for treating that outcome as either inevitable or impossible.

That is a remarkable transition for a field to undergo within a working lifetime, let alone a few years.

I think this is an important part of understanding anti-AI sentiment. In [Pew's June 2026 survey](https://www.pewresearch.org/short-reads/2026/08/18/young-adults-in-the-us-are-increasingly-wary-of-ai-concerned-it-will-take-jobs/), 52% of American adults were more concerned than excited about AI's increased use; 9% were more excited than concerned. Concern is not the same as opposition to every use of AI. But it would be a mistake to assume that a better demo will answer whatever is worrying people.

A tool can be impressive and still make someone reasonably anxious about their livelihood. A person can enjoy using it while objecting to how their work was used to build it. They can welcome useful assistance and dislike having an unreliable automated system inserted between them and a service they need. These are different concerns, and grouping all of them under a single attitude toward technology makes them harder to address.

The employment question is particularly difficult. Making a task cheaper can increase demand for it, make new products possible, and change the mix of work inside a company. It can also reduce demand for some kinds of labor. How those effects balance depends on the market and on the pace of change. Even if the aggregate outcome is positive, it does not follow that the people bearing the disruption will be the ones receiving the gains, or that they can adapt on the required schedule.

For those of us building the technology, proximity can distort perspective here. We see interesting problems and tools that help us solve them. Someone else may see a decision being made about their future without much opportunity to influence it. If we want people to share our excitement, we need a credible account of how they participate in the benefits and what happens when things go badly. Telling them to be excited is a fairly weak substitute.

## The physical part of the story

Data centers make these questions unusually concrete. Models run on computers that need electricity, cooling, land, and connections to a grid. A forecast about future intelligence eventually becomes a proposal to build something in a particular place.

The [IEA's April 2026 outlook](https://www.iea.org/reports/key-questions-on-energy-and-ai/executive-summary) projects global data-center electricity consumption rising from about 485 TWh in 2025 to roughly 950 TWh in 2030. Those figures include all data centers, and the latter is a projection. The report also describes growing community pushback and concerns about affordability and environmental effects.

The local details matter more than a global percentage can convey. [Virginia's legislative audit of data centers](https://jlarc.virginia.gov/landing-2024-data-centers-in-virginia.asp) documents substantial construction and tax benefits alongside the difficulty of supplying projected electricity demand. A facility can have genuine economic value and still create hard questions about where new infrastructure goes, how costs are allocated, and what the surrounding community receives.

I do not think opposing a particular data center necessarily implies opposing AI. Residents can reasonably want to know about noise, water use, grid investment, and the durability of promised benefits. The answers will depend on the location and the design. Likewise, the existence of costs does not settle whether a project is worthwhile. It creates an obligation to make the tradeoffs explicit and to account for them honestly.

There is a connection here to the wider backlash. The promised benefits of AI are often described at the scale of humanity. Its immediate costs arrive at the scale of a household, a profession, or a town. We should expect resistance when the people asked to accept those costs have little confidence that the larger promise includes them.

## What is all of this worth?

I have been interested in the business side of technology since before I worked on AI. The valuation question is a particularly strange one because people often seem to be answering different questions while using the same words.

I would separate three claims:

1. AI can create enormous value for people.
2. Companies providing AI can capture a substantial amount of that value as durable profits.
3. The prices investors pay for those companies are justified by the profits they will eventually earn.

Evidence for the first claim helps with the second and third, but it does not settle them.

Suppose a tool saves a customer a great deal of work. That creates room for a valuable business. The amount the provider can charge, however, also depends on competition, alternatives, switching costs, and the customer's ability to capture the savings. A user can benefit enormously from a service whose price keeps falling. That is excellent for the user and a more complicated story for someone who financed the service assuming high margins.

There are reasons large businesses could emerge here. Useful capabilities can reach many different markets. Customers may pay substantially for reliable systems that understand their workflows, integrate with their tools, and earn their trust. Research and infrastructure can be expensive enough to create meaningful advantages for organizations that do them well.

But some advantages may change. In 2019, part of my explanation for why open-source businesses worked was that understanding someone else's code was expensive. What happens to that advantage when understanding the code becomes much cheaper? Deployment, reliability, support, and customer trust still matter. The difficulty of reproducing a feature may matter less. A capability can make a product more useful while also making parts of its business easier to compete with.

The same question applies to AI providers themselves. Better efficiency may lower the cost of serving users while competition passes much of the saving on to those users. A company can attract demand and still struggle to pay for the research, infrastructure, and ongoing computation needed to meet it.

Timing complicates things further. Infrastructure requires commitments before the resulting demand and returns are fully known. The [IEA explicitly notes](https://www.iea.org/reports/key-questions-on-energy-and-ai/executive-summary) that financing conditions and expectations about returns will affect the pace of data-center expansion. A technology can ultimately justify a great deal of investment while some projects are built too early, at the wrong cost, or in the wrong place.

So I do not have a blanket answer to whether AI valuations are justified. I am much more confident that the capabilities matter than that every current price embeds sensible assumptions. For a particular business, I would want to understand what customers pay for, the cost of delivering it, why the business can retain those customers, and what must happen for the investment to earn its return.

It is entirely possible to underestimate AI's eventual importance and overpay for a particular AI company. It is also possible to dismiss an investment because you underestimate how large its market could become. Working out which case you are looking at requires an argument about the business.

## What I am watching next

After these six years, I am reluctant to offer a confident script for the next six. I do have some questions that seem increasingly important.

For the past two years, I have been leading a team focused on reasoning, memory, and test-time training. That work puts some of these questions close to home. How much useful work can a system do before a person has to intervene? How reliably can it recognize a mistake, recover from it, or admit that it is stuck? As we give it more context, memory, and opportunities to learn from experience, can we still understand what it is doing and control what it is allowed to affect?

I also want to know whether verification can keep up with generation. It is easy to imagine a world with far more proposed code changes, analyses, and scientific hypotheses. The benefit depends on our ability to tell which are good. AI can help with that work too, but the feedback has to remain connected to reality. Producing an answer and producing convincing-looking evidence for it are not enough.

A particularly consequential possibility is that AI accelerates the research and engineering used to build better AI. To the extent that happens, we may have less time to adapt between advances. It could also give us better tools for evaluating systems, finding vulnerabilities, and improving defenses. I would like to see those capabilities develop together. There is no reason to assume that the balance will take care of itself.

I will also be watching whether people outside the field get more control over their own work and lives as these tools improve. That is a large part of what makes the effort worthwhile to me. More capable systems should make it easier for more people to build, learn, and solve problems they care about. We should be willing to judge progress by whether that actually happens.

My last post was about an interface that behaved differently from what I expected. The gap between what an interface seems to promise and what the system actually does has followed me into a very different kind of work. There are still experiments to run, bugs to fix, and difficult engineering choices. But the consequences now reach far beyond a surprising result in a short program.

That is what the age of wonder and terror feels like to me. I am excited by how much more people may be able to understand and create. I am unsettled by the damage increasingly capable systems could do, and by how much of the adjustment remains ahead of us. I expect to keep revising my view of both. Hopefully, I will write some of it down before another six years pass.

[^upload]: As of this writing, "upload" remains elusive.
