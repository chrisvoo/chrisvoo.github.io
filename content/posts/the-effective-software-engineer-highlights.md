---
title: 'The Effective Software Engineer: my highlights'
date: 2026-11-17T21:30:00
draft: false
tags:
  - books
  - AI
summary: My notes and highlights from "The Effective Software Engineer" by Addy Osmani. I read it on O'Reilly between late August and mid November, and I focus on the parts about anti-patterns, communication and working with AI.
comments: true
see_also:
  - /posts/axon-remote-ai-assistance/
  - /posts/automating-monthly-band-checks-with-github-actions-and-github-pages/
---

I finished reading [The Effective Software Engineer](https://effective.addy.ie/) by Addy Osmani. I read it on the O'Reilly platform, a few pages at a time, from the end of August to the middle of November.

This is not a review. It's a walk through the things I highlighted, because the highlights say more about me than a rating would. I exported them as a CSV (more than 200 of them), and the exercise of grouping them was already useful.

I'm not following the book's chapter order. I grouped my highlights by theme instead.

### The anti-pattern chapters hit hardest

Chapter 5 is the one with the most highlights, by far. It's a catalogue of habits that limit an engineer, and the author wrote the list so you can recognise yourself in it. I did, more than once:

- **Analysis paralysis**: endless research and planning without moving forward
- **Perfectionism and gold-plating**: refining the work and never shipping it
- **Not-invented-here syndrome**: building a custom solution when a tool or library would do
- **Over-engineering versus YAGNI (_You Aren't Gonna Need It_)**: a future-proof framework when a simple function would suffice
- **Tool obsession**: chasing shiny objects
- **Context-switching addiction**: no deep focus
- **Meeting overload**, **feedback resistance**, **imposter syndrome paralysis**, and a few more

Two remedies stuck with me because they are so practical:

1. **Time-box the research.** Give yourself two days, then commit to start implementing, even without perfect information.
2. **Define "done" before you start**, and stick to those boundaries.

Funny enough, most of my personal projects need this advice. The [band checker]({{< ref "automating-monthly-band-checks-with-github-actions-and-github-pages" >}}) started as a JSON file and a script. It's still that, plus a cron job. I'm happy with it, and I'm not going to build a notification platform around it (yet).

I also underlined this sentence: *"Shipping first time code is like going into debt... Every minute spent on not-quite-right code counts as interest on that debt."* It sits in chapter 3, and it balances the perfectionism warning nicely. "Good enough" is not "sloppy". The difference is whether you know what you are borrowing.

### Silos, heroes and the bus factor

A second group of highlights is about people who become single points of failure, and I counted three variations of the same idea:

- the **knowledge silo**, where critical information lives in one head
- the **hero** ("white knight syndrome"), a single point of failure in high-pressure situations
- the **rubber stamp** review, which happens when code ownership is siloed. Peers trust the author, or feel unqualified to critique the work

The suggested fixes are boring, and that's why I believe them: rotate ownership areas, make sure at least two people know any given system, write at least a high-level README for each component, and automate processes to reduce reliance on memory or personal skill. The goal is not to make everyone an expert in everything. It is to avoid *single-expert fragility*.

For my home server this is quite literal. I'm the only person who knows how it works. Writing it down in skills and scripts is also a way of onboarding my future self.

### Communication is part of the job

Chapter 11 is where I highlighted the most about communication and visibility, and I think it's the part I needed most:

- _"We often assume our work will speak for itself, but that's rarely true."_
- Frame the problem and why it matters before you dive into the solution. Start high-level, then drill down.
- Teaching or presenting forces you to organise your thoughts and anticipate questions. It also raises your visibility and credibility.
- **Visibility is not vanity. It's making sure your work has the influence it should have.**

The remote-work part goes in the same direction: the best remote engineers are proactive communicators, self-disciplined and empathetic. Writing is the main tool, and being patient with async answers is part of it.

I admit this blog is a small attempt to follow that advice. Writing about a project forces me to explain it.

### Strategy, career and rest

Some short highlights worth keeping together:

- **Strategic thinking**: the 80/20 rule, and the impact/effort matrix. Quick wins first, high-effort projects get planned, low-impact and high-effort work gets dropped. Include data in technical discussions. And "a strategic thinker is not stubborn".
- **Career growth**: tenure alone is not enough. Track your accomplishments, and understand your company's ladder. The jump from senior to staff is about organisational influence.
- **Burnout**: *"Remember, you are your most important tool."* Set stopping points and accept them. And my favourite: "Rest is part of work, not the opposite of it."
- **Retros**: begin each one by checking the action items of the last one. Every action item needs an owner and, ideally, a deadline. Limit and prioritise them.

### The AI part

I'm tagging this post with AI because the last chapters are where the book talks about it directly, and my highlights there are the most "of the moment".

**What to delegate.** The book gives a list of tasks where AI helps a lot:

- boilerplate and scaffolding
- test drafts
- summaries and explanations
- migration scripts and repetitive edits
- structured documentation and search

**What to keep.** The author says to stay hands-on (or at least eyes-on) for:

- architecture and design decisions
- failure modes and critical logic
- anything without tests or monitoring
- the novel, critical or sensitive tasks

I like the split. It's not "AI good" or "AI bad". It says that the more a task is routine and verifiable, the more you can hand it over.

**How to ask.** My highlights here read like a checklist for a prompt:

1. Goal/outcome: start by clearly stating what you want to achieve
2. Relevant context
3. Constraints/requirements

And the sentence that summarises it: the more you frame the task for the AI, including context from your codebase and specific requirements, the better the results. This matches my experience with [Axon]({{< ref "axon-remote-ai-assistance" >}}). I said there that a well-structured prompt was enough to solve my problem, and I didn't need a full spec-driven approach. The book gives me the words to explain why: goal, context, constraints.

**Keep your safety nets.** One highlight says to integrate SAST, software component analysis (dependency vulnerability scanners), secret scanners and other security tooling into the CI pipeline. It's in the AI chapter for a reason. If a machine writes more of your code, the automated checks matter more, not less.

**Use evidence, not hype.** The last highlight I made, a few days ago, says that conversations with colleagues or managers about adopting AI are most productive when grounded in evidence rather than opinion or hype. I find this a healthy attitude in a period where everybody has a strong opinion.

Two smaller ones also stuck with me. The first: you might deliver features faster because AI wrote 30% of the trivial code, which is a modest and believable claim. The second is in the career chapter: you can feed an AI your completed Jira tickets, pull request links and design documents from the past quarter, and get a draft of your accomplishments. I plan to try it.

The book's description of the future IC fits in four lines: empowered by AI, working across disciplines (code, product thinking, DevOps), always learning, and leading through technical expertise. I'm not sure about the "quasi-management" part, but the rest sounds right.

### Conclusions

What I take from the book is not a technique. It's a list of mirrors. Many of the anti-patterns are mine: too much research, too much polish, too little visibility. The AI chapters add a new rule on top: delegate what is routine and checkable, and keep your hands on what is critical.

If I had to pick one sentence from all my highlights, it would be the one from the second chapter: **"Clear code, clear tests, clear docs, clear communication."** It's short enough to remember, and it works with or without AI.
