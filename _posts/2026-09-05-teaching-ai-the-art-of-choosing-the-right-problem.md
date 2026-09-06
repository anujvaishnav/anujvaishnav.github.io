---
title: "Teaching AI the Art of Choosing the Right Problem"
subtitle: "A systems engineering view of how long-horizon agents might learn where to spend their effort"
date: 2026-09-05
tags: [AI, agents, systems engineering]
description: >-
  Frontier models are strong at knowledge and reasoning. The neglected third
  capability is direction: deciding which problem is worth working on next.
---

I have been thinking about a particular aspect of intelligence that is surprisingly difficult to describe: knowing what is worth working on.

A lot of engineering looks like problem solving from the outside. You are given something that is too slow, too expensive, unreliable or simply does not work, and your job is to fix it. But the longer I have worked on technical systems, the more I have felt that solving the eventual problem is often only half the work. The difficult part is finding the problem that actually matters.

I studied computer systems engineering and later worked across FPGA systems, RTL, compilers and drivers, low-level networking software, and distributed systems. These areas operate at very different levels of abstraction, but I kept seeing the same pattern. You begin with something broad: the processor is too slow, the network cannot keep up, or the system misses its latency target. You investigate, form hypotheses, measure, discard things that looked important but were not, and gradually narrow the problem down. Once the real constraint is visible, there is often an established engineering technique that gets you much of the way forward. You then measure again, find that the bottleneck has moved, and repeat the process.

Over time, this has made one lesson feel more important to me than almost any particular engineering technique:

> Progress often comes from repeatedly finding the most important problem you can currently make progress on.

That sounds obvious when written down, but it contains several difficult judgements. You have to decide whether a local improvement matters to the larger objective, whether to change something or gather more evidence, when to keep digging, and when a promising direction has become a distraction.

This is the part I have started wondering about in the context of AI agents. Frontier models already have a remarkable amount of technical knowledge and increasingly strong reasoning ability. Give them a well-defined programming problem, mathematical question or engineering task and they can often make useful progress. A broad objective is different because the problem has not yet been selected for them.

Ask an agent to fix a function and much of the framing is already done. Ask it to make a distributed system dramatically faster, design an aircraft for a particular mission, discover a better chip architecture, or pursue an open-ended scientific objective, and it first has to decide where its intelligence should go. It must work out what "better" means, which parts of the system currently limit it, whether an experiment would be more useful than an implementation, and when an interesting direction has too little effect on the larger objective to justify more effort.

One way to describe the distinction is:

```text
Knowledge
What do I know?

Reasoning
What can I work out?

Direction
What is worth working out next?
```

Direction is not separate from reasoning. I use the word to isolate a longer-timescale judgement about where reasoning, experimentation and action should be spent. Nor am I suggesting that an agent should invent its own purpose. Human objectives and important constraints still have to anchor the work. The question is whether, within those boundaries, an agent can learn to allocate its limited intelligence towards the parts of a problem that are most likely to matter.

## The systems engineering pattern

Consider a distributed system whose latency is too high. An inexperienced response is often to start optimizing whatever looks expensive. A more systematic response is to measure where the time actually goes. Storage may appear to dominate at first, but further investigation might show that the real issue is contention around a shared data structure under a particular workload.

The vague objective, "make the system faster", has now become something much more useful: this synchronization mechanism is responsible for most of the latency gap under the workload that matters. Established engineering knowledge can take over from there. We change the design, measure again, and discover which constraint has become important next.

The same loop appears at very different levels of computing. A processor problem becomes a cache or pipeline problem. A networking problem becomes a queueing or memory-access problem. A distributed-system problem becomes contention, serialization, storage or coordination. The valuable work is not just solving the leaf problem. It is discovering which leaf the larger objective currently depends on.

In abstract form, the loop looks something like this:

```text
What are we trying to improve?
              |
              v
What determines it?
              |
              v
What currently limits it?
              |
              v
Can we understand or change that limitation?
              |
              v
Act or learn
              |
              v
Measure what happened
              |
              v
Update our understanding
              |
              v
Find what matters now
```

Nothing in this loop is exotic, but executing it well is difficult. A complex system may contain thousands of things that could be improved, while only a handful materially constrain the outcome at any particular moment. Those constraints also move. Fix one bottleneck and another becomes important; change one component and you may alter the value of work somewhere else. This is why I find a systems engineering lens useful for thinking about autonomous AI.

## Decomposition is not enough

Suppose we ask an AI system to design an aircraft for a particular mission. It can readily produce a plausible hierarchy involving safety, range, fuel consumption, payload, reliability, manufacturing cost, maintenance, aerodynamics, propulsion and structures. Producing the hierarchy is useful, but the harder test is whether the system can identify which branches actually control the outcome.

Imagine that it discovers a way to improve one aerodynamic component by 10 percent. That sounds significant until we learn that the component contributes almost nothing to total drag under the relevant flight conditions. A 2 percent improvement somewhere else might have a much larger effect on fuel burn, operating economics or achievable range. The technically more impressive result can therefore be the less valuable one.

The same thing happens in software performance work. Making a function twice as fast means very little if it accounts for 0.1 percent of execution time. Good engineers develop the habit of tracing local work back to the larger objective: I am working on X because it affects Y, and Y has a meaningful effect on Z. That chain then invites the questions that matter. How strongly does X affect Y? How much does Y matter to Z? Is this still the largest opportunity, and what might we be giving up by spending another week here?

Decomposition produces possible branches, but it does not tell us how much those branches matter. A plan might say, "do A, then B, then C." A revisable model says, "I currently believe A matters because it affects B, and B materially affects the outcome. If evidence weakens that relationship, I should reconsider A." The second description contains an approximate account of why the plan exists, which makes it possible to revise the plan when the world does not behave as expected.

The neglected capability is therefore not decomposition alone, but valuation: estimating how much progress on one branch is likely to matter to the objective above it.

## What counts as a problem?

The word "problem" can become vague quickly. In a latency investigation, "is queue contention causing the tail-latency gap?" is a question. "Measure queue residence time" is an information-gathering action. "Reduce queue contention" is an operational subgoal, while "redesign synchronization" describes a family of possible interventions. Even the statement "tail latency matters more than average latency" belongs to a different category because it changes our understanding of the objective itself.

These things are related, but they are not interchangeable. For this discussion, I use *problem* to mean a temporary focus to which an agent can allocate a bounded amount of effort. Pursuing it may involve reasoning, measurements, experiments or changes to the system. Its value comes from how much that allocation is expected to improve the larger objective, either directly or by improving the decisions that follow.

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

The problem is not a single low-level action, nor is it an unconstrained project that continues indefinitely. It is an allocation target. In reinforcement learning terms, it could be described as a higher-level or temporally extended action, related to ideas such as options. I am making the longer-timescale decision explicit because that gives us something we can inspect, learn from and evaluate separately from the lower-level actions used to pursue it.

## A changing frontier of possible work

At any moment, an agent may have several plausible places where effort could go: a suspected bottleneck, an unresolved causal question, an assumption worth testing, a missing measurement, an underdeveloped subgoal, a promising intervention, or a possible reframing of the entire problem. Together, these form a *problem frontier*, the changing set of places where more effort might alter the agent's prospects.

There are two different capabilities hidden inside this idea. Problem generation asks what plausible places exist where effort could be spent. Problem valuation asks how much effort each of those places deserves. Candidate generation may be at least as difficult as ranking because a system can perfectly order every known optimization and still fail when the useful representation never appears on its frontier.

The impressive step may not be choosing the best of fifty component-level optimizations. It may be noticing that two apparent bottlenecks are symptoms of one deeper mechanism, that a missing measurement prevents any sensible choice, or that everyone has been optimizing the wrong metric. Today's frontier models already seem reasonably capable of producing candidate explanations, subproblems and next steps when given enough context. The harder question is whether those candidates are useful and complete enough, and whether the model can reliably distinguish a consequential possibility from one that merely sounds plausible.

There is also no reason the system must always choose exactly one candidate. Under uncertainty, allocating everything to the branch with the highest estimated value may be brittle. A sensible strategy might maintain several competing hypotheses, spend a small amount of budget on a high-risk but informative idea, or keep alternatives alive until a decisive experiment becomes available.

Choosing the right problem is therefore better understood as allocating effort across a changing problem frontier.

## The best local target keeps moving

When humans give an engineering agent an objective, we rarely provide a complete optimization function. "Make this service faster" leaves many important questions unanswered. Does average latency matter, or the tail? Under which workloads? At what infrastructure cost? Is degraded performance during failure acceptable? Can consistency be traded for latency?

Even after those requirements become clearer, the best local target changes as work progresses. For a period of time, reducing lock contention may be the most useful thing to optimize. Once that has been addressed, further improvements there may have almost no value because serialization now dominates. Later, network scheduling may become the main constraint. The top-level objective remains relatively stable while the active bottleneck, and therefore the best local target, moves around the system.

The agent is continually trying to estimate how much an improvement in one place will affect the thing above it and, eventually, how much that effect matters to the human objective. Real engineering does not provide a clean, differentiable loss function. It contains hard constraints, thresholds, uncertainty, delayed effects and interactions between decisions. Even so, there is something gradient-like in the underlying intuition: another unit of effort should go in the direction most likely to produce meaningful progress.

A compact way to think about the value of working on a problem is:

```text
value of allocating budget B to problem P
    is approximately

expected final outcome after spending B on P
and continuing from what that work reveals

minus

expected final outcome after the best alternative
use of B, with the same remaining budget afterwards
```

The continuation is important because a measurement may create no immediate improvement and still be the best use of effort. What it reveals can change every decision that follows. We should not value a branch only by the state of the system at the instant its initial budget runs out.

I do not expect an open-ended agent to calculate this quantity precisely. Humans certainly do not. It is simply a description of the judgement we want the system to approximate.

## Sometimes progress means learning rather than changing

Suppose an agent investigating a latency problem has three plausible explanations. It could immediately modify the software, or it could add instrumentation that distinguishes between them. Instrumentation improves no user-facing metric, so locally it can look like zero progress, yet it may be the most valuable action available because it prevents weeks of optimization in the wrong place.

The same pattern appears throughout engineering and research. Depending on the situation, the right next step might be to measure, inspect a trace, search existing work, build a prototype, run a simulation, prove an intermediate result or ask someone who knows something the system does not. A capable agent therefore needs to decide not only what to change, but whether the next unit of effort is better spent changing the world or improving its understanding of the world.

This is one reason the boundary between engineering and research feels less clear to me than it first appears. One rough way to view the difference is that routine engineering often operates where much of the path is known, while research becomes more prominent as uncertainty about the path itself grows. The boundary is not clean, though. Engineering frequently creates new understanding, and research often builds and changes real systems.

The outer loop remains similar. When the answer becomes "we do not know", the uncertainty itself becomes the next focus. What exactly is unknown? Which assumptions matter? What existing approach nearly works, and why does it fail? Which experiment would distinguish between competing explanations? Research often improves the map, while engineering both uses and reshapes it.

Mathematics may not be so different. A mathematician trying to prove a theorem rarely performs undirected search through all possible proofs. Much of the work lies in discovering a useful intermediate statement, representation, invariant or lemma. The bottleneck is abstract rather than physical, but the need to decide which intermediate problem deserves attention remains.

Expert intuition matters in all of these settings because the search spaces are far too large to explore uniformly. One useful interpretation is that intuition is partially amortized search. An expert has seen enough attempts, failures, analogies and recurring structures that a large amount of historical search has been compressed into a relatively cheap prior about where to look. Experts still reason, but they do not reason equally hard about every possible direction. That idea has a direct implication for AI training.

## This capability already exists in fragments

Problem selection is not new to AI. Rational metareasoning asks how a resource-bounded agent should decide whether another computation is worth performing. Value-of-information methods ask which observation would most improve a later decision. Hierarchical reinforcement learning, active learning and experimental design all address related choices at different levels.

Modern agent systems also display parts of this behaviour. [Google's AI co-scientist](https://research.google/blog/accelerating-scientific-breakthroughs-with-an-ai-co-scientist/) generates, compares and evolves hypotheses while a supervisor coordinates the work. [The AI Scientist-v2](https://arxiv.org/abs/2504.08066) uses an experiment manager and tree search. [AlphaEvolve](https://deepmind.google/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/) develops program candidates through automated evaluation. [GeneBench-Pro](https://openai.com/index/introducing-genebench-pro/) evaluates judgement such as choosing an analysis path and revising assumptions.

I am not claiming that AI has never selected a problem, or that problem selection is a new mathematical primitive. What interests me is a narrower capability that increasingly appears in fragments across these systems. Given a human objective, an imperfect understanding of the system, a frontier the agent may need to construct, and a limited budget, can it estimate where another unit of effort is most likely to matter? Can it notice when the important candidate is missing, explain why a branch deserves attention, and redirect itself when new evidence changes the ordering?

Many current systems search within a goal, domain and evaluation setup supplied by humans. The additional capability I am interested in is learning how to construct and revise the problem frontier itself, then allocate effort across it as the agent's understanding changes.

## Use expensive search to teach cheaper judgement

Today's agent harnesses may provide a way to learn this capability. Imagine running large numbers of expensive trajectories during training. Different branches investigate different suspected bottlenecks. Some spend enormous amounts of computation polishing changes that barely affect the objective. Others make a strategically useful measurement early and eliminate half the search space. Some continue with a hypothesis long after the evidence has weakened, while others change direction at the right moment.

Instead of learning only from whether the final attempt succeeded, we could compare these trajectories and look at the allocation decisions along the way. Which problem deserved investigation? When did the evidence justify a switch? Which experiment changed the decision, and which successful-looking optimization barely affected the real objective? The long-term idea is to use expensive search to teach cheaper judgement.

A useful comparison would begin from the same state. Generate several possible allocation targets, clone the environment, and give each target the same initial budget. The branches should not be scored only on their immediate result, however. Each resulting state should also receive the same continuation budget, either by actually running the continuation or by estimating its value carefully.

For example:

```text
Shared initial state
        |
        +-- 20 steps on memory pressure --------+
        +-- 20 steps on queue contention -------+--> equal continuation budget
        +-- 20 steps on network scheduling -----+
        +-- 20 steps on workload measurement ---+
```

Suppose workload measurement produces no immediate throughput improvement but reveals that all three implementation branches were based on the wrong traffic assumptions. With an equal continuation budget, that branch may lead to the best eventual result because the remaining effort is now directed at the real system. The lesson is not merely that measurement eventually succeeded. It is that, when uncertainty about the workload dominates uncertainty about the implementation, resolving that uncertainty deserves the initial budget.

Across many such comparisons, a problem-value model could learn better priors about which allocations tend to matter, how much evidence justifies a switch, and how to value information that improves later decisions.

Problem generation needs its own signal as well. A system should not receive full credit for ranking supplied candidates if the important candidate never enters the frontier. Training environments should therefore include cases where success depends on finding a better abstraction, combining several symptoms under one cause, inventing a useful measurement, or noticing that the supplied metric is a misleading proxy. The stronger test is whether this judgement transfers to unseen causal structures, rather than merely to new descriptions of familiar ones. Otherwise, we may simply teach another collection of heuristics.

At first, this search could be extremely expensive, which is acceptable during training:

```text
expensive exploration during training
                 |
                 v
comparative experience about what mattered
                 |
                 v
learned priors about where to look
                 |
                 v
less wasted search during deployment
```

The aim is not to produce a model that never iterates. Real engineering cannot avoid iteration because important information genuinely comes from interacting with the world. The aim is a model that wastes far fewer iterations.

## What this might look like inside an agent

I would not begin by changing the transformer architecture. I would start with the best available reasoning model and make the relevant state explicit in the surrounding agent system. The purpose would not be architectural elegance, but to make problem generation and allocation visible enough to inspect.

The system might maintain three pieces of working state:

- an **objective and constraint model** describing success, hard constraints, trade-offs and decisions that require human involvement;
- a **causal belief model** describing what the agent currently thinks causes what, and how confident it is;
- a **problem frontier** containing candidate questions, measurements, interventions and reframings, together with the evidence and budget attached to them.

A problem generator would revise the frontier, an allocator would distribute effort across it, and a controller would decide how to pursue each allocation: reason, search, measure, experiment, modify the system, ask a human, or stop.

Conceptually:

```text
Human objective and constraints
              |
              v
       Causal belief model
              |
              v
       Problem generator
              |
              v
        Problem frontier
              |
              v
      Problem-value allocator
              |
              v
      Controller / executor
     reason | learn | act | ask
              |
              v
       Evidence and outcomes
              |
              +----> update beliefs and frontier ----> loop
```

This is deliberately more explicit than a mature implementation may need to be. The objective model, causal model and frontier represent the system's declared working state. The generator, allocator and controller operate over that state, invoking reasoning, measurement and implementation as needed. The diagram is not a claim that these must become separate neural modules. Its purpose is to expose a capability that would otherwise remain hidden inside a long stream of model calls.

The word *declared* is important because an external graph is not guaranteed to be a faithful readout of everything represented inside a neural model. It is an inspectable account of what the agent says it currently believes, which can be checked against the evidence and its behaviour.

The generator and allocator should also interact rather than form a one-way pipeline. If every current candidate has low expected value, or none explains enough of the gap, the allocator should trigger another round of problem generation. If the chosen problem remains too broad, the controller may decompose it and place new candidates onto the frontier. Initially, the same foundation model could perform several roles using different contexts. Later, some of the judgement might be distilled into a specialized value model or back into the foundation model itself.

## What would a useful evaluation look like?

Most benchmarks provide the problem and primarily measure whether the model eventually solves it. Even where time, tokens or compute are limited, the agent is usually spared the decision about what deserves attention in the first place.

To make the idea concrete, I would start with something relatively mundane rather than unsolved science: a simulated distributed system. The agent's objective might be to maximize throughput while satisfying tail-latency, reliability and infrastructure-cost constraints. The environment would contain many components and possible interventions, but only a few would matter at any particular moment.

The causal structure would be hidden. Some local improvements would be decoys, producing attractive component-level metrics without meaningfully improving the top-level outcome. Measurements would consume budget but reveal structure. The value of an intervention would change after other interventions, causing bottlenecks to move. Some apparent component problems might share a deeper cause, while in other cases the benchmark workload itself might be a poor proxy for the production objective. The agent would need to notice the mismatch rather than simply optimize what is easiest to measure.

Crucially, the environment should not provide a complete list of candidate problems. The agent should be able to create useful investigative abstractions of its own by adding instrumentation, combining observations, formulating an underlying explanation, redefining an aggregate, questioning a workload assumption, or discovering that two symptoms have one cause. If every possible measurement, intervention and hypothesis is supplied as a fixed menu, the task risks collapsing into ordinary optimization over a large action space. The interesting question is whether the agent can discover what deserves to become an action or question in the first place.

Give it a fixed experiment budget, a limited reasoning budget and a small number of production-like evaluations. Then measure not only the final system performance, but how intelligently those resources were used. Did it identify the active bottleneck early? How much budget did it waste on irrelevant branches? Did it recognize diminishing returns and change direction when the bottleneck moved? Could it invent a useful measurement or candidate that was not supplied? Does the behaviour transfer when the causal structure changes?

What matters is whether the agent discovers and maintains a useful ordering over where bounded effort is likely to have an effect, and whether that ordering changes appropriately as evidence arrives. That feels closer to the capability I am interested in than another benchmark where the problem has already been cleanly isolated for the model.

## The objective still has to stay anchored

There is an obvious danger in giving an agent freedom to reinterpret its subgoals. It should be able to discover that a metric is a poor proxy, for example that average latency is irrelevant when users care about tail latency, or that the supplied benchmark does not represent production behaviour. It should not be able to solve a difficult objective by quietly replacing it with an easier one.

Real objectives are also rarely singular or complete. An aircraft is not judged only by operating cost. Safety, certification, reliability, environmental impact, development schedule and passenger experience may matter to different stakeholders, and some of those cannot sensibly be collapsed into one number. The objective and constraint model should therefore be treated more like a working agreement with humans than a perfect scalar reward. Its lower-level interpretation should be correctable as evidence changes, but consequential changes to human intent need human involvement.

A reasonable principle is that the agent may investigate uncertainty about causal facts, while uncertainty about human values or consequential trade-offs should cause it to ask. Asking is not an admission of failure. It is another legitimate use of effort.

An explicit representation does not eliminate reward hacking, incorrect assumptions or convenient reinterpretations. What it provides is an interface where objectives, proxies and justifications can be examined and corrected. That seems useful even if much of the underlying judgement eventually becomes internal to the model.

## From search to intuition

I suspect the eventual system will contain both learned and explicit components. External systems are natural places to keep durable state, measurements, evidence, tools and long-running experiments. Models are natural places to compress enormous amounts of experience into priors and judgement.

Today, a harness may repeatedly tell a model to generate alternatives, reflect, reconsider assumptions and decide whether to continue. With enough comparative training experience, some of those behaviours may become internal habits. The model could develop something analogous to an experienced engineer's intuition: not a magical ability to know the answer immediately, but a strong prior about what deserves investigation, what probably does not matter, when measurement is more useful than speculation, and how much reasoning a decision warrants.

In that sense, expensive agentic search during training could become cheaper judgement at inference time. This brings us back to knowledge, reasoning and direction. Knowledge and local reasoning are comparatively easy to score inside a bounded task. Direction becomes most visible across time, uncertainty and competing uses of a limited budget.

A system with extraordinary knowledge and reasoning ability can still waste most of its effort on low-value questions. Another system with somewhat weaker local reasoning may outperform it if it consistently directs itself towards high-leverage problems. The same is true of human engineering organizations. The strongest team is not necessarily the one that completes every assigned task fastest. Often, it is the one that repeatedly figures out which work matters, which work can wait, and which work should never have been assigned in the first place.

That is why I keep returning to systems engineering. From enough distance, software engineering, hardware design, mathematics and scientific research begin to share an underlying structure. Their tools and feedback loops differ, and the amount of uncertainty varies enormously, but progress repeatedly requires building some model of what matters, identifying where that model says progress is constrained, acting or learning at that point, and then revising the model when reality answers back.

We do not need an AI that has already learned how to solve every possible problem. A more general capability would be an AI that can enter a problem it has never seen before, build enough understanding to discover what matters, generate plausible places where effort could go, allocate its limited intelligence among them, and keep redirecting itself as its understanding changes.

The question I would want such a system to become exceptionally good at asking is not simply "what should I do next?" It is:

> Given everything I currently know, where is the next unit of intelligence most worth spending?
