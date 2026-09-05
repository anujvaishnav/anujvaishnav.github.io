---
title: "Teaching AI the Art of Choosing the Right Problem"
subtitle: "A systems engineering view of how long-horizon agents might learn where to spend their effort"
date: 2026-09-05
tags: [AI, agents, systems engineering]
description: >-
  Frontier models are strong at knowledge and reasoning. The neglected third
  capability is direction: deciding which problem is worth working on next.
---

I have been thinking about a particular aspect of intelligence that is surprisingly difficult to describe: knowing **what is worth working on**.

A lot of engineering looks like problem solving from the outside. You are given something that is too slow, too expensive, unreliable or simply does not work, and your job is to fix it. But the longer I have worked on technical systems, the more I have felt that solving the eventual problem is often only half the work.

The harder part is finding it.

I studied computer systems engineering and later worked across FPGA systems, RTL, compilers and drivers, low-level networking software, and distributed systems. These areas operate at very different levels of abstraction, but I kept noticing the same pattern.

You start with something broad: the processor is too slow, the network cannot keep up, the system misses its latency target. You investigate, form hypotheses, measure, discard things that looked important but were not, and gradually narrow the problem down. Once you find the real constraint, there is often an established engineering technique that gets you much of the way forward.

Then you measure again, and the bottleneck moves.

Over time, this has made one lesson feel more important to me than almost any particular engineering technique:

> **Progress often comes from repeatedly finding the most important problem you can currently make progress on.**

That sounds obvious when written down. In practice, it involves a lot of judgement.

You have to decide whether an improvement actually matters to the larger system, whether you understand the problem well enough to act, whether another experiment would be more useful than another implementation, when to keep digging into a branch, and when to decide that something is already good enough and move elsewhere.

This is the part I have started wondering about in the context of AI agents.

Frontier models already have a remarkable amount of technical knowledge and increasingly strong reasoning ability. Give them a well-defined programming problem, mathematical question or engineering task and they can often make useful progress.

But a broad objective is different.

Ask an agent to fix a function and the problem has already been selected for it. Ask it to make a distributed system dramatically faster, design the best aircraft for a particular mission, discover a better chip architecture, or pursue an open-ended scientific objective, and it has to decide where its intelligence should go before it can use that intelligence effectively.

What does "better" actually mean? What determines it? Which part of the system currently limits it? Is that limitation worth fixing? Would we learn more by experimenting before changing anything? When should we abandon a technically interesting direction because its effect on the larger objective is too small?

This suggests a useful distinction:

```text
Knowledge
What do I know?

Reasoning
What can I work out?

Direction
What is worth working out next?
```

The first two are already central to how we think about increasingly capable models. I am interested in the third.

Not in the sense of letting an agent invent its own purpose. Human objectives and important constraints still have to anchor the work. The question is whether an agent can learn to allocate its limited reasoning, experimentation and action towards the parts of a problem that are most likely to matter.

Given what it is trying to achieve, **how does an intelligent system decide which problem is worth solving next?**

## The systems engineering pattern

Consider a distributed system whose latency is too high.

An inexperienced response is often to start optimizing things that look expensive. A more systematic response is to measure where the time actually goes. Perhaps storage appears to dominate. Further investigation shows that the real issue is contention, which is eventually traced to one shared data structure under a particular workload.

At that point, we have transformed:

> Make the system faster.

into something much more useful:

> This synchronization mechanism is responsible for most of the latency gap under the workload that matters.

Established engineering knowledge can often take over from there. We change the design, measure again, and discover that the bottleneck has moved somewhere else.

I have seen essentially the same loop at very different levels of computing. A processor performance problem becomes a cache or pipeline problem. A networking problem becomes a queueing or memory-access problem. A distributed-system problem becomes contention, serialization, storage or coordination.

Each time, the useful work is not merely solving the leaf problem. It is discovering which leaf the larger objective currently depends on.

In abstract form:

```text
What are we trying to improve?
              ↓
What determines it?
              ↓
What currently limits it?
              ↓
Can we understand or change that limitation?
              ↓
Act or learn
              ↓
Measure what happened
              ↓
Update our understanding
              ↓
Find what matters now
```

The loop is simple. Executing it well is not.

A complex system may contain thousands of things that could be improved, while only a handful materially constrain the outcome at any particular moment. Those constraints also move. Fix one bottleneck and another becomes important. Change one component and you may alter the economics of a completely different part of the design.

This is why I find a systems-engineering lens useful for thinking about autonomous AI.

## Decomposition is not enough

Suppose we ask an AI system to design an aircraft for a particular mission. It can readily produce a plausible-looking hierarchy involving safety, range, fuel consumption, payload, reliability, manufacturing cost, maintenance, aerodynamics, propulsion and structures.

The harder test is whether it can identify which branches actually control the outcome.

Imagine that the model discovers a way to improve one aerodynamic component by 10 percent. That sounds significant until we discover that the component contributes almost nothing to total drag under the relevant flight conditions. Meanwhile, a 2 percent improvement somewhere else might materially affect fuel burn, operating economics or achievable range.

A technically impressive improvement can therefore be almost worthless.

This is familiar in software performance work. Making a function twice as fast means very little if it accounts for 0.1 percent of execution time. Good engineers develop the habit of tracing local work back to the larger objective:

> I am working on X because it affects Y, and Y has a meaningful effect on Z.

The important questions then become: how strongly does X affect Y? How much does Y matter to Z? Is this still the largest opportunity? What might we be giving up by spending another week here?

That chain of reasoning turns a decomposition into a revisable model of the problem.

It also introduces something that ordinary task planning often misses.

A plan says:

> Do A, then B, then C.

Systems reasoning says:

> I currently believe A matters because it affects B, and B materially affects the outcome. If evidence weakens that relationship, I should reconsider A.

The second system understands, at least approximately, **why the plan exists**.

The neglected capability is therefore not decomposition alone, but **valuation**: estimating how much progress on one branch is likely to matter to the objective above it.

## What counts as a problem?

The word "problem" can become vague quickly.

Consider a latency investigation:

- "Is queue contention causing the tail-latency gap?" is a question.
- "Measure queue residence time" is an information-gathering action.
- "Reduce queue contention" is an operational subgoal.
- "Redesign synchronization" is a family of possible interventions.
- "Tail latency matters more than average latency" is a correction to our understanding of the objective.

These are related, but they are not interchangeable.

For this discussion, I use **problem** to mean a temporary focus to which an agent can allocate a bounded amount of effort. It may lead to a sequence of reasoning steps, measurements or interventions, and its value comes from how much that allocation is expected to improve the larger objective.

For example:

```text
Focus:
  Determine whether queue contention causes
  the tail-latency gap.

Possible processes:
  Inspect traces, add instrumentation,
  simulate, reason, experiment.

Initial budget:
  Thirty minutes or five experiments.

Why it may matter:
  The answer determines which redesign branch
  should receive further effort.
```

The problem is not a single low-level action, nor is it an unconstrained project that continues indefinitely. It is an **allocation target**.

This distinction matters because problem selection is not somehow outside ordinary decision-making. An RL researcher could reasonably describe it as a higher-level or temporally extended action, related to ideas such as options in hierarchical reinforcement learning.

The point is not to claim otherwise.

The proposal is to make this longer-timescale allocation decision explicit enough that we can inspect it, train it and evaluate it separately from the lower-level actions used to pursue the selected branch.

## A changing frontier of possible work

At any moment, an agent may have several plausible places where effort could go:

- a suspected bottleneck;
- an unresolved causal question;
- an assumption worth testing;
- a missing measurement;
- an underdeveloped subgoal;
- a promising intervention;
- a possible reframing of the entire problem.

Together, these form a **problem frontier**: the changing set of places where more effort might alter the agent's prospects.

Two capabilities are needed here, and they should not be confused.

The first is **problem generation**:

> What are the plausible places where we could spend effort?

The second is **problem valuation**:

> Which of those places deserves how much effort?

Candidate generation may be at least as difficult as ranking.

A system can perfectly rank every known optimization and still fail because the useful representation never appeared on its frontier. The impressive step may not be choosing the best of fifty component-level optimizations. It may be noticing that two apparent bottlenecks are symptoms of one deeper mechanism, or that everyone has been optimizing the wrong metric.

Today's frontier models already seem reasonably capable of producing candidate explanations, subproblems and next steps when given enough context. The harder question is whether those candidates are complete enough, whether the abstractions are useful, and whether the model can reliably distinguish a consequential possibility from one that merely sounds plausible.

There is also no reason the system must always choose exactly one candidate.

In uncertain research, allocating everything to the branch with the highest estimated value may be brittle. A sensible strategy might maintain several competing hypotheses, spend a small amount of budget on a high-risk but informative idea, or keep alternatives alive until a decisive experiment becomes available.

The deeper technical concept behind "choosing the right problem" is therefore closer to:

> **Allocating effort across a changing problem frontier.**

## The best local target keeps moving

There is a machine-learning analogy here which I find useful, as long as it is not taken too literally.

When humans give an engineering agent an objective, we rarely provide a complete optimization function.

"Make this service faster" is not enough. Does average latency matter, or the tail? Under which workloads? At what infrastructure cost? Is degraded performance during failure acceptable? Can consistency be traded for latency?

Even after those requirements become clearer, the best local target changes as work progresses.

For a period of time, reducing lock contention may be the most useful thing to optimize. Once that has been addressed, improving it further may have almost no value. Serialization may now dominate. Later it may be network scheduling.

The top-level objective remains relatively stable while the active bottleneck, and therefore the best local target, moves around the system.

The agent is continually trying to estimate:

> If I improve this, how much will it improve the thing above it?

and eventually:

> How much will that matter to what the human actually cares about?

This is not a literal differentiable loss function. Real engineering contains hard constraints, thresholds, uncertainty, delayed effects and interactions between decisions.

But there is something gradient-like in the intuition. The agent is trying to find the direction in which another unit of effort is likely to produce the most meaningful progress.

A compact way to think about the value of working on a problem is:

```text
value of allocating budget B to problem P
    ≈
expected final outcome after spending B on P
    -
expected final outcome under the best alternative
use of the same budget
```

The point is not that an open-ended agent will calculate this quantity precisely. Humans certainly do not.

It is a description of the judgement we want the system to approximate.

## Sometimes progress means learning rather than changing

Suppose an agent investigating a latency problem has three plausible explanations. It could immediately modify the software, or it could add instrumentation that distinguishes between them.

Instrumentation improves no user-facing metric. Locally, it looks like zero progress.

Yet it may be the most valuable action available because it prevents weeks of optimization in the wrong place.

The same pattern appears throughout engineering and research. Depending on the situation, the right next step might be to measure, inspect a trace, search existing work, build a prototype, run a simulation, prove an intermediate result or ask someone who knows something the system does not.

A capable agent therefore needs to decide not only what to change, but whether the next unit of effort is better spent changing the world or improving its understanding of the world.

This is one reason the boundary between engineering and research feels less clear to me than it initially appears.

Routine engineering operates where much of the path is already known. Harder engineering contains greater uncertainty about the bottleneck, architecture or trade-offs. Research starts to dominate when decomposition reaches a problem for which there is no established method.

The outer loop remains similar. When the answer becomes "we do not know", the uncertainty itself becomes the next focus. What exactly is unknown? Which assumptions matter? What existing approach nearly works? Why does it fail? What experiment would distinguish between competing explanations?

Research produces a better map. Engineering uses the new map to continue towards the objective.

Mathematics may not be so different. A mathematician trying to prove a theorem rarely performs undirected search through all possible proofs. Much of the work lies in discovering a useful intermediate statement, representation, invariant or lemma. The bottleneck is abstract rather than physical, but the need to decide which intermediate problem deserves attention remains.

Expert intuition matters in all of these settings because the search spaces are far too large to explore uniformly.

One interpretation I find useful is that:

> **Intuition is partially amortized search.**

An expert has seen enough attempts, failures, analogies and recurring structures that a large amount of historical search has been compressed into a relatively cheap prior about where to look. Experts still reason, but they do not reason equally hard about every possible direction.

That idea has a direct implication for AI training.

## Today's harnesses may be tomorrow's training data

Problem selection is not new to AI.

Rational metareasoning has long asked how a resource-bounded agent should decide whether another computation is worth performing. Value-of-information methods ask which observation would most improve a later decision. Hierarchical reinforcement learning deals with decisions made at different timescales. Active learning and experimental design ask where new information is worth acquiring.

Modern agent systems are also beginning to display parts of this behaviour directly.

Research-oriented systems use combinations of hypothesis generation, reflection, evaluation, search, persistent state and repeated experimentation. Reasoning models can already benefit from additional computation at inference time and can learn strategies such as decomposing problems, checking intermediate work and trying alternative approaches.

So I do not think the interesting claim is:

> AI does not know how to select problems today.

It increasingly does, at least in fragments.

The narrower question is whether **problem allocation should become a first-class capability**.

Given a human objective, a current understanding of the system, a generated frontier of possible questions and interventions, and a remaining budget, can the agent learn to estimate the marginal value of allocating effort to each candidate?

Can that judgement be made explicit enough to inspect?

Can it be trained from comparative experience?

Can we evaluate it separately from how capable the underlying executor happens to be?

That seems to me like a useful research direction.

And today's somewhat cumbersome agent harnesses may provide a way to pursue it.

Imagine running large numbers of expensive trajectories during training. Different branches investigate different suspected bottlenecks. Some spend enormous amounts of computation polishing changes that barely affect the objective. Others make a strategically useful measurement early and eliminate half the search space. Some continue with a hypothesis long after evidence has weakened; others change direction at the right moment.

Instead of learning only from whether the final attempt succeeded, we can compare these trajectories and ask where the important allocation decisions occurred.

Which problem was actually worth investigating?

When was there enough evidence to abandon the current branch?

Which experiment changed the decision most?

Which apparently successful optimization had almost no effect on the root objective?

How much better would the result have been if the agent had spent its budget elsewhere?

The long-term idea is simple:

> **Use expensive search to teach cheaper judgement.**

## A possible architecture

I would not begin by changing the transformer architecture.

I would start with the best available reasoning model and make the structure explicit in the surrounding agent system. The purpose of the first implementation would not be architectural elegance. It would be to make problem generation and allocation visible enough to study.

The system would maintain three pieces of persistent working state.

The **objective and constraint model** records what should count as success: human objectives, hard constraints, acceptable trade-offs, uncertainty about the request, and decisions that require human authorization.

The **causal belief model** records what the agent currently thinks causes what: system mechanisms, observations, simulations, previous interventions, competing explanations and confidence in those beliefs.

The **problem frontier** records where effort could be allocated next: candidate questions, bottlenecks, measurements, experiments, interventions and reframings, together with evidence, dependencies, estimated value and budget already spent.

Three capabilities then operate over that state.

A **problem generator** proposes and revises possible focus areas.

A **problem-value allocator** compares those candidates and decides how to distribute a limited budget across them.

A **controller and executor** decides what to do within those allocations: reason, search, measure, experiment, modify the system, ask a human, or stop.

Conceptually:

```text
                         HUMAN OBJECTIVE
                                │
                                ▼
                 ┌───────────────────────────┐
                 │  OBJECTIVE AND CONSTRAINT │
                 │           MODEL           │
                 │                           │
                 │ What counts as success?   │
                 │ What cannot be traded?    │
                 └─────────────┬─────────────┘
                               │
                               ▼
                 ┌───────────────────────────┐
                 │   CAUSAL BELIEF MODEL     │
                 │                           │
                 │ How does the system work? │
                 │ What do we know so far?   │
                 └─────────────┬─────────────┘
                               │
                               ▼
                 ┌───────────────────────────┐
                 │     PROBLEM GENERATOR     │
                 │                           │
                 │ Propose possible places   │
                 │ to spend effort           │
                 └─────────────┬─────────────┘
                               │
                               ▼
                 ┌───────────────────────────┐
                 │      PROBLEM FRONTIER     │
                 │                           │
                 │ Questions, bottlenecks,   │
                 │ unknowns, experiments,    │
                 │ interventions, reframings │
                 └─────────────┬─────────────┘
                               │
                               ▼
                 ┌───────────────────────────┐
                 │ PROBLEM-VALUE ALLOCATOR   │
                 │                           │
                 │ How should the remaining  │
                 │ budget be distributed?    │
                 └─────────────┬─────────────┘
                               │
                     budgeted portfolio
                       + justification
                               │
                               ▼
                 ┌───────────────────────────┐
                 │   CONTROLLER / EXECUTOR   │
                 │                           │
                 │ reason | learn | act | ask│
                 └─────────────┬─────────────┘
                               │
                               ▼
                    EVIDENCE AND OUTCOMES
                               │
                               ▼
                  update objectives, beliefs
                    and the problem frontier
                               │
                               └──────────────↺
```

This is deliberately more explicit than a mature implementation may need to be.

The objective model, causal model and frontier are artifacts: the system's changing, declared working state. The generator, allocator and controller are capabilities operating over that state. Reasoning, measurement and implementation are processes invoked by those capabilities.

The phrase **declared working state** matters. An external graph is not guaranteed to be a faithful readout of everything represented inside a neural model. It is an inspectable account that can be checked against evidence and behaviour.

The problem generator and allocator should also interact rather than form a one-way pipeline. If every current candidate has low expected value, or none explains enough of the gap, the allocator should trigger another round of problem generation. If the chosen problem remains too broad, the controller may decompose it and place new candidates onto the frontier.

Initially, the same foundation model could perform several of these roles using different contexts. Later, the allocator might become a separately trained value model, or parts of the entire loop could be distilled back into the foundation model.

The boxes are not the claim.

Their purpose is to make the underlying capability measurable.

## How could we train problem selection?

The hardest part is obtaining a useful training signal.

A successful trajectory does not prove that every allocation decision inside it was good. A failed trajectory does not prove that its initial direction was wrong. Results may depend on executor quality, random events, delayed effects and interactions between branches.

More importantly, we normally do not observe what would have happened if the agent had worked on something else.

A useful first experiment would therefore use environments where counterfactual comparisons can actually be run.

Start from the same state. Generate several possible allocation targets. Clone the environment, give each target an equal budget, and let the same or comparable executor pursue each branch. Then evaluate the resulting states against the top-level objective.

For example:

```text
Shared initial state
        │
        ├── 20 steps on memory pressure
        ├── 20 steps on queue contention
        ├── 20 steps on network scheduling
        └── 20 steps on workload measurement
```

Suppose workload measurement reveals that all three implementation branches were based on the wrong traffic assumptions.

That gives us a much stronger lesson than simply observing that the measurement branch eventually succeeded:

> When uncertainty about the workload dominates uncertainty about the implementation, resolving that uncertainty should receive the initial budget.

Across many such comparisons, a problem-value model could learn to rank allocations and become better calibrated about how much different branches are likely to matter.

Problem generation needs its own signal as well.

A system should not receive full credit for ranking supplied candidates if the important candidate never enters the frontier. Training environments should therefore contain cases where success depends on finding a better abstraction, combining several symptoms under one cause, or noticing that the supplied metric is a misleading proxy.

The stronger test would be whether this transfers to **unseen causal structures**, not just new descriptions of familiar ones.

Otherwise, we may simply teach another collection of heuristics.

At first, this search could be extremely expensive. That is fine during training.

The broader hypothesis is:

```text
expensive exploration during training
                 ↓
comparative experience about what mattered
                 ↓
learned priors about where to look
                 ↓
less wasted search during deployment
```

The desired outcome is not a model that never iterates. Real engineering cannot avoid iteration because important information genuinely comes from interaction with the world.

The goal is a model that wastes far fewer iterations.

## A benchmark for choosing what to work on

This capability would also need a different style of evaluation.

Many widely used benchmarks provide the problem and primarily measure whether the model eventually solves it. Even where time, tokens or compute are limited, the agent is usually spared an important decision:

**What deserves attention in the first place?**

I would start with something relatively mundane rather than unsolved science: a simulated distributed system.

The agent's objective might be to maximize throughput while satisfying tail-latency, reliability and infrastructure-cost constraints. The environment would contain many components and tunable parameters, but only a few would matter at any particular moment.

The causal structure would be hidden. Some local improvements would be decoys, producing attractive component-level metrics without meaningfully improving the top-level outcome. Measurements would consume budget but reveal structure. The value of an intervention would change after other interventions, causing bottlenecks to move.

Some apparent component problems might share a deeper cause. In other cases, the benchmark workload itself might be a poor proxy for the production objective, forcing the agent to notice the mismatch rather than simply optimize what was easiest to measure.

Most importantly, the benchmark would not provide a complete list of candidate problems.

The agent would have to generate its own problem frontier.

Give it fifty experiments, a fixed reasoning budget and a limited number of production-like evaluations. Then measure not only the final system performance, but how intelligently those resources were used.

Did it identify the active bottleneck early?

How much budget did it spend on irrelevant branches?

Did it recognize diminishing returns?

Did its predicted problem values match the eventual outcomes?

Did it change direction when the bottleneck moved?

Could it discover a useful candidate that was not supplied to it?

Does the behaviour transfer when the underlying causal structure changes?

The distinctive question becomes:

> **Did the agent discover and maintain a useful ordering over where bounded effort was likely to matter, and did that ordering change appropriately as evidence arrived?**

That feels much closer to the capability I am interested in than another benchmark where the problem has already been cleanly isolated for the model.

## The objective still has to stay anchored

There is an obvious danger in giving an agent freedom to reinterpret its subgoals.

The system should be able to discover that a metric is a poor proxy. It should be able to say that optimizing average latency is irrelevant when users actually care about tail latency, or that the supplied benchmark does not represent production behaviour.

But it should not solve a difficult objective by quietly replacing it with an easier one.

Real objectives are also rarely singular or complete. An aircraft is not judged only by operating cost. Safety, certification, reliability, environmental impact, development schedule and passenger experience may matter to different stakeholders, and some of those cannot sensibly be collapsed into one number.

The objective and constraint model should therefore be treated more like a working agreement with humans than a perfect scalar reward.

Its lower-level interpretation should be correctable as evidence changes, but consequential changes to human intent need human involvement.

A useful rule might be:

> **When uncertainty concerns causal facts, the agent may investigate. When uncertainty concerns human values or consequential trade-offs, it should ask.**

"Asking" is therefore not an admission of failure. It is another legitimate allocation of effort.

An explicit representation does not eliminate reward hacking, incorrect assumptions or convenient reinterpretations. What it gives us is an interface where objectives, proxies and justifications can be examined and corrected.

That seems valuable even if much of the underlying judgement eventually becomes internal to the model.

## From search to intuition

I suspect the eventual system will contain both learned and explicit components.

External systems are natural places to keep durable state, measurements, evidence, tools and long-running experiments. Models are natural places to compress enormous amounts of experience into priors and judgement.

Today, a harness may repeatedly tell a model to generate alternatives, reflect, reconsider assumptions and decide whether to continue. With enough comparative training experience, some of those behaviours may become internal habits.

The model could develop something analogous to an experienced engineer's intuition: not a magical ability to know the answer immediately, but a strong prior about what deserves investigation, what probably does not matter, when measurement is more useful than speculation, and how much reasoning a decision warrants.

In that sense, expensive agentic search during training could become cheaper judgement at inference time.

And this returns to the three aspects of capability that started this essay:

```text
Knowledge
What do I know?

Reasoning
What can I work out?

Direction
What is worth working out next?
```

Knowledge and local reasoning are comparatively easy to score inside a bounded task. Direction becomes most visible across time, uncertainty and competing uses of a limited budget.

A system with extraordinary knowledge and reasoning ability can still waste most of its effort on low-value questions. Another system with somewhat weaker local reasoning may outperform it if it consistently directs itself towards high-leverage problems.

This is familiar from human engineering organizations as well. The strongest team is not necessarily the one that completes every assigned task fastest. Often, it is the one that repeatedly figures out which work matters, which work can wait, and which work should never have been assigned in the first place.

That is why I keep coming back to systems engineering.

When viewed from enough distance, software engineering, hardware design, mathematics and scientific research begin to share an underlying structure. Their tools and feedback loops differ, and the amount of uncertainty varies enormously, but progress repeatedly requires building some model of what matters, identifying where that model says progress is constrained, acting or learning at that point, and then revising the model when reality answers back.

We do not need an AI that has already learned how to solve every possible problem. That is neither realistic nor necessary.

A more general capability would be an AI that can enter a problem it has never seen before, build enough understanding to discover what matters, generate plausible places where effort could go, allocate its limited intelligence among them, and keep redirecting itself as its understanding changes.

The question I would want such a system to become exceptionally good at asking is therefore not simply:

> **What should I do next?**

It is:

> **Given everything I currently know, where is the next unit of intelligence most worth spending?**