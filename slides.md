---
theme: ./theme
colorSchema: light
title: Don't build a slopware factory
author: Filip Hric
event: Prague 2026
transition: none
drawings:
  persist: false
---

# Don't build a<br>*slopware*<br>factory

Filip Hric

::image::

![Paper collage of mechanical arms producing software packages on a conveyor belt.](/images/software-factories.png)

<!--
- thank you for having me
- we’ll talk about software factories today, how to NOT turn it into a Slopware factory
- we’ll discuss quality and verification, boundaries and so on
- my name is Filip, I’m from company called Qodo, where we provide a platform for AI code quality and code governance
- I myself spent more than a decade in various QA positions before I moved to software development and then later into developer relations
- so quality is something close to my heart and I love to discuss
-->

---
layout: section
---

# what is <em style="color: var(--signal-deep)">software</em> factory?

<!--
- let’s answer this question
- truth is, everyone has their own definition, slightly different idea of software factory
- I’m sure in this room we could find a few
- the broad idea is to have a repeatable system that turns approved intent into working software
- and this idea got really popular in past couple of months, but it’s not really that new, in fact some of the first mentions date back to 1968
- the reason why it’s catching a new wave these days is that many of the human bottlenecks can now be removed with work of AI
-->

---
layout: sdlc
clicks: 14
---

<SdlcLoop />

<!--
- before AI, we already had something that would resemble a software factory, only it included a lot of manpower
- got through steps
- now of course, with the rise of AI, the build part got incredibly effective, AI is great at writing code and with models like Fable, Astra and other frontier models we’ve reached a point where AI is able to write better code than most humans, and it can also write it faster
- now I know some of you may twitch when I say that, and that’s probably because you already have some battle scars and seen AI output some really crappy code
- and this is at the core of the problem - how do we ensure that AI actually outputs good code, that meets the intent, and are we actually able to automate that?
-->

---
layout: sdlc
clicks: 3
name: Loop, harness, factory
---

<LoopHarnessFactory />

<!--
- but before we answer that, let’s first answer the previous question about software factories
- found this great article  by Addy Osmani, where he talks about building blocks of a software factory
- [click] he calls loop an atom of a software factory - the principle is "gather context, take an action, check the result"
- so instead of prompt, result prompt result, you set a goal and let the agent run
- this is what many teams already do. If you're using Codex or Claude Code, you can set a pretty broad goal, and the agent will iterate until it reaches that goal
- [click] harness is basically a loop within certain constraints - this means the environment, sandbox, standards enter the loop and help it define what done means
- a good way to explain the difference between a loop and a harness would be that in the loop *the agent* or prompt decides when a task is done, in harness, "done" is defined *by the environment*, so e.g. until integration tests pass, we are not done
- I find that many engineering teams have some sort of combination of these two going, where engineers no longer just prompt their agent, but have already defined standards and give the agent the right tools to complete a task - that tool can be your documentation, Linear or Jira ticket etc. we know have agent rules and skills, and these drive the agent output
- [click] - finally, the software factory is basically a system of harnesses, continuously delivering software
-->

---
layout: sdlc
clicks: 9
name: Agentic software factory
---

<AgenticSoftwareFactory />

<!--
- in practice this may modify your SDLC into something like this
- [click] your managers and engineers define the Intent / vision 
- [click] ends up in the queue defines what to build.
- [click] The harness orchestrates the model and sandbox to build the change.
- [click] Automated checks: CI/CD, unit tests, static and security scans.
- [click] Review combines agentic code review with human review of the change.
- [click] Deploy rolls the change out to production.
- [click] Monitor production telemetry.
- [click] Signals feed complaints, feature requests, and incidents back into the queue.
- this still has a human in the loop
- [click] there is this the idea of a dark factory, where essentially you don’t need any humans in the loop, so you can turn off the lights and get rid of review
- and getting rid of the review is probably the hardest thing to automate
- some teams decide to remove it alltogether, which I think is a really bad idea
- I think the thinking is that once a model or a harness is really really good, the review just becomes a ceremony and we can pop it right out, and get ourselves a dark factory
- but that’s how you end up with a slopware factory in my opinion
- verification and review part is the hardest one to get right, and there are several reasons for it
-->

---
layout: verification
clicks: 4
name: Verification in software factory
---

# <em style="color: var(--signal-deep)">Verification</em> in software factory

<ul>
  <li v-click="1">Generation is uncertain</li>
  <li v-click="2">The factory can standardize the wrong thing</li>
  <li v-click="3">Second agent is not automatically independent evidence</li>
  <li v-click="4">Verification cannot establish that customers want the product</li>
</ul>

::image::

![A hand inspects a software package with a magnifying glass beside a checklist.](/images/verification/inspection.png)

<!--
[click] **Generation is uncertain**
- when we use AI to generate code, we don’t know in advance what kind of result are we going to get
- even with acceptance criteria clearly defined, there are open questions - does the code cross some  boundaries we didn’t predict? did we create duplications in the implementation? 
- because of the nature of AI, we need to introduce some sort of determinism or governance, because just because AI produced working code, it doesn’t mean it’s the right code

[click] **The factory can standardize the wrong thing**

- which actually brings me to the next point - Randomness is only part of the problem
- because even if your AI factory learns from your past decisions, those might not be the right ones
- you can have agent implement an old pattern because you are in the middle of refactoring
- even if your tests pass and you go through all parts of validation, you don’t have a guarantee that  your codebase is going in the right direction
- this can also have a compounding effect where you can slowly create a standards drift, or codify the wrong thing
- verification in software factory needs to have some sort of feedback loop where you strenghten the standards you want, and weaken the patterns you want to avoid

[click] **second agent is not automatically independent evidence**

- I’ve seen companies use Claude Code for code generation and them use Claude Code for code review - which essentially means that you’ll get the same blind spots
- choosing a different model is a really good next step, but even that may not necessarily mean we get the right evidence
- slapping an AI at the diff to give you a vibe-check is not verification
- also each time you ask AI to find bugs, it will find bugs
- what you need is a review that scores high on precison and on recall
- recall is how many of the real issues introduced by a change were found, so ideally review finds them all 
- precision is how many of the things that tool finds are actual bugs and what is noise
- I argue that when building a software factory, the verification is a much more complex problem than code generation, because the concern is actually much wider
- I actually argued with Lauren Tan this morning, because she said review is a waste of tokens, I actually think it can be, but only if you don’t set it up properly - review is not a markdown file, you need a system

[click] **Verification cannot establish that customers want the product**

- and then finally, having a review is good, but we may be building the wrong thing, or bulding it in wrong way - in a way customers don’t want
- and the biggest challenge for this one, is that this thing actually doesn’t live in your code, it lives in all of the orher parts of the world
- your product went through some historical changes, you might have had a meeting about what is right, maybe you had a customer call, you may have had a ticket, maybe there some other signal, you name it
-->

---
layout: section
name: Software factory in enterprise
---

# Software factory in <em style="color: var(--signal-deep)">enterprise</em>

<!--
- this brings me to the big question, how do we actually tackle this in the enterprise set up?
- I’ve seen a lot of companies handing out Claude code licenses to the developers in hopes of 10x growth in productivity
- and then, few months later everyone realizes that we actually don’t get 10x productivity, because all of that productivity stopped at the code review and now someone has to read all that code line by line, which is a horrible idea and makes none of the investment worth it, in fact it makes things worse
- so what many companies do, as all lean companies should, is to focus on the next bottleneck, which is code review
- but as we know from previous slide, just slapping an AI on a diff and calling it a day doesn’t really work
-->

---
layout: default
name: Generation and verification
clicks: 4
---

<GenerationVerification />

<!--
- the way I like thing about how to solve this problem
- is to loosely split my flow into generation vs. verification
- so my coding agent will take all these kinds of contexts in and create a feature
- but because coding agents make mistakes, they have limited context windows, they don't always stick to the instructions and rules, sometimes ignore things that are written in the ticket, I need to verify them
- the easiest way to verify it without running tests is to have a code review agent
- and it's a separate agent, different model, and it's going to take a look at whether:
- Rules were not being ignored
- Ticket was fully implemented
- Skills were used
- Whether there were no bugs or security issues
- and it also looks at the blast radius of the change, so e.g. whether the change in this repository is not breaking something in another repository
-->

---
layout: default
name: Review at every step
clicks: 4
---

<ReviewAtEveryStep />

<!--
- so in an attempt to build a software factory in an enterprise setup, you can think of the code generation and code verification as this constant feedback loop
-  if we do the verification just on the code review, the **feedback loop is pretty wide**
- So it's much better to **tighten it** and to actually shift left and bring it to earlier stages of development
- so instead of running that check on pull request, the same set of agents can be run locally **before the code is committed**
  - and it can actually go even further
  - the agent also **review the plan** generated by the agent, assessing if it’s solid and actually aligns with the **current state of the codebase.**
  - and why stop at the plan, the same agent system can be applied to **reviewing the ticket** where requirements are defined and I can see **how it holds up** against the rest of the codebase.
- this will dramatically shorten the feedback loop, but I already mentioned that this is not enough, because we can actually be consistently building the wrong thing
-->

---
layout: section
name: From continuous delivery to continuous improvement
---

# From continuous <em style="color: var(--signal-deep)">delivery</em><br>to continuous <em style="color: var(--signal-deep)">improvement</em>

<style>
.editorial.section h1 {
  font-size: 60px;
  line-height: 1.1;
}
</style>

<!--
- Build a factory that learns what good means.
-->

<!--
- if we want to avoid building a slopware factory, verification has to have another role - it needs to improve the whole system
- ideally, build a factory that learns what **good** means
- the most important mental shift when building software factories is that we are not optimizing for continuous delivery, but for continuous improvement
-->

---
layout: default
name: Build quality checks around team experience
---

# Build quality checks around<br><em>your team’s experience</em>

<v-clicks>

- **Capture team knowledge:** decisions, reviews, past incidents.
- **Check early and often,** with independent review before shipping.
- **Review related PRs together,** including changes across repos.
- **Keep engineers in control:** show changes and their impact.
- **Plan for more review:** faster coding creates more to verify.

</v-clicks>

<!--
- every few weeks we get a new release of smarter and more capable model, but Better models alone won’t know the decisions and tradeoffs that shaped your system.
- Turn the team’s experience into rules and context that both coding and review agents can use.
- Feed accepted and rejected review findings back into those checks, so they reflect current judgment.
- Use that same experience throughout development, with an independent check before shipping.
- Review the whole change across repositories, and give engineers visibility into its impact.
- This is how faster code generation can translate into faster delivery
- I promised not to pitch anything, after all I’m not in Sales, but if anything out of this is interesting to you, come talk to me, this is what we are building at Qodo
-->

---
layout: default
class: qodo-community
name: Qodo free for open source
---

<div class="editorial-label">Qodo ♥ the community</div>

# Qodo is free for<br><em>open-source maintainers.</em>

<div class="qodo-community-grid">
  <section class="qodo-oss">
    <p class="qodo-oss-tag"><mark>FREE FOR OPEN SOURCE</mark></p>
    <p class="qodo-oss-support">+ Sponsoring OSS community projects</p>
    <div class="qodo-oss-action">
      <a class="qodo-signin-qr" href="https://app.qodo.ai/signin" aria-label="Log in to Qodo"><img src="/images/qodo-community/signin-qr.png" alt="QR code to log in at app.qodo.ai/signin" /></a>
      <div>
        <h2>Interested?</h2>
        <p>Log in to Qodo and reach out to Dana Fine.</p>
        <a href="mailto:dana.f@qodo.ai">dana.f@qodo.ai</a>
        <a class="qodo-signin-url" href="https://app.qodo.ai/signin">app.qodo.ai/signin ↗</a>
      </div>
    </div>
  </section>
  <aside class="qodo-community-programs">
    <section>
      <span class="editorial-label">01 / Meet the community</span>
      <h2>Qodo Dev Community</h2>
      <p>Co-hosting &amp; sponsoring meetups</p>
    </section>
    <section>
      <span class="editorial-label">02 / Become an ambassador</span>
      <h2>Super Qodoer</h2>
      <p>Ambassadors Program</p>
    </section>
  </aside>
</div>

<!--
- we are also providing it for free to open source projects, so if that’s you let’s get connected, we’ll give you the tools to help you handle all of the AI code submissions you are getting
-->

---
layout: illustration
class: thank-you
---

# Thank *you!*

Filip Hric

[filiphric.com](https://filiphric.com)

<div class="thank-you-qr-links">
  <a href="https://slides.filiphric.com/dont-build-a-slopware-factory/">
    <span class="qr-print"><img src="/images/thank-you/slides-qr.png" alt="QR code to get this presentation at slides.filiphric.com/dont-build-a-slopware-factory/" /></span>
    <span>get the slides</span>
  </a>
  <a href="https://links.filiphric.com">
    <span class="qr-print"><img src="/images/thank-you/connect-qr.png" alt="QR code for Filip Hric's links" /></span>
    <span>connect with me</span>
  </a>
</div>

::image::

![Filip Hric in the editorial portrait from filiphric.com.](/images/thank-you/filip-hric.png)
