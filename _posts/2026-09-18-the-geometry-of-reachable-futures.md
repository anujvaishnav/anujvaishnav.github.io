---
title: "The Geometry of Reachable Futures"
subtitle: "Thinking about long-horizon intelligence through what remains possible"
date: 2026-09-18
tags: [AI, agents, representation]
description: >-
  A long-horizon agent needs more than the current state and the goal. It also
  needs to represent how each choice changes the futures that remain reachable.
---

In my previous article, [Teaching AI the Art of Choosing the Right Problem](https://anujvaishnav.com/blog/teaching-ai-the-art-of-choosing-the-right-problem/), I ended with a question:

> Given everything I currently know, where is the next unit of intelligence most worth spending?

I have kept coming back to that question, partly because it hides another one underneath it.

How would a system know?

It is easy to say that an intelligent agent should spend more effort on important decisions, investigate the right uncertainty, or avoid solving the wrong problem. It is much harder to say what the agent would need to represent internally before any of that becomes possible.

A description of the current state is not enough. A description of the goal is not enough either. The agent also needs some sense of how the available choices change what can happen next.

One choice may keep several useful paths open. Another may look efficient but quietly commit the system to a bad direction. A small experiment may appear to delay progress while actually preventing weeks of work on the wrong assumption.

That led me to a simple idea:

> A state should be represented partly by the futures it leaves open.

I have started thinking of this as the **geometry of reachable futures**.

I do not mean a literal geometric space, and I am not suggesting that a model should enumerate every possible future. The phrase is useful because it draws attention to shape. Where do paths split? Which choices close options? Which mistakes are easy to recover from? Which actions look small now but change everything later?

If an AI system could represent that structure, even approximately, it might have a better basis for planning, exploration, recovery, and deciding where more reasoning is actually worth the cost.

## The present is not just what is true now

We usually describe a state by listing facts about the world as it currently is.

For a long-running task, that misses something important. The agent also needs to know what it has learned, what it is still unsure about, which assumptions are holding the plan together, and what it can still safely try.

Imagine an engineer investigating a slow distributed system. After an hour, the system may be no faster than before. No code has been deployed. No latency chart has improved.

But perhaps the engineer has ruled out two likely bottlenecks, added instrumentation that separates queueing time from processing time, and discovered that the benchmark does not resemble production traffic.

Nothing visible has improved, yet the investigation is in a much better place. Several bad paths have been removed. One important uncertainty has become measurable. The next action is easier to choose.

This is a useful distinction. An action can change the world, or it can change the agent's understanding of the world. Both can change what becomes possible next.

That is why I think the relevant state is closer to a belief state than a snapshot. It includes the external situation, but also the agent's uncertainty, evidence, capabilities, and constraints.

Reachability then becomes more subtle than it first appears. A future may be possible in principle but unknown to the agent. It may be understood but too risky to attempt. It may be feasible for a stronger system but outside the current agent's abilities. It may become practical only after one missing fact has been established.

So the representation cannot be only a map of what could happen. It has to include what the agent can recognise, evaluate, and act on from where it currently stands.

## Why I find the geometry metaphor useful

The word *geometry* may sound more formal than the idea really is, but it helps me ask the right questions.

How far apart are two states in a way that matters for decisions? Do different paths lead to the same place? Is there a narrow bottleneck that every successful route must pass through? Can the agent turn back after taking a step, or has something important been lost?

These questions are easy to miss if progress is reduced to distance from a target.

A local improvement can make the real goal harder to reach. A software change might improve a benchmark while deepening dependence on an architecture that will never meet the production requirement. A proof can become longer and more sophisticated while drifting further from anything that can actually be completed. A research project can produce encouraging numbers while relying on a measurement that does not distinguish between the explanations that matter.

The opposite is also true. Some useful actions do not look like progress at all. Instrumentation, refactoring, exploratory experiments, or a carefully chosen counterexample may leave the main metric unchanged. Their value comes from improving the path ahead.

This is why the representation needs more than a sense of closeness. It has to preserve something about branching, reversibility, uncertainty, and path dependence.

It also cannot assume that movement is symmetric. It may be easy to reveal private information and impossible to take that action back. It may be easy to create an external dependency and very expensive to remove it later. Some steps change the state in one direction only.

The scale of the action matters too. A useful next move might be a line of code, an experiment, a system redesign, a new tool, a lemma, or a reformulation of the whole problem. A long-horizon agent has to move between these levels rather than treating every action as the same kind of step.

## Decisions matter when they reshape the future

This view gives a more concrete meaning to a consequential decision.

A choice matters when its alternatives lead to meaningfully different futures.

That difference might come from commitment. One option leaves several routes open, while another forces the system down a single path before enough is known.

It might come from reversibility. A mistake can be tolerable when it is cheap to detect and correct. The same mistake becomes serious when its effects are hidden or compound over time.

It might come from information. An experiment may not improve the system directly, but it can remove uncertainty from every decision that follows.

These are related, but I would be wary of collapsing them into one importance score. A decision can be irreversible without being important to the current goal. A reversible experiment can still be valuable if it separates two explanations that lead to completely different plans.

The richer representation should preserve these differences before the objective decides how to value them.

That matters because real decisions rarely involve only one measure of success. Performance, reliability, safety, time, cost, knowledge, and flexibility can all pull in different directions. Reducing them to one number too early often hides the trade-off rather than resolving it.

The representation should show how the future changes. The objective still has to decide which changes are worth pursuing.

## The same pattern can appear in very different work

The part of this idea that interests me most is the possibility of transfer.

A software migration and a scientific experiment look unrelated on the surface. One is about systems, dependencies, and operational risk. The other is about hypotheses, evidence, and measurement.

But sometimes the underlying decision is almost the same.

In both cases, there may be pressure to commit early. In both cases, the plan may depend on an assumption that has not been tested. In both cases, a small and relatively cheap action could separate the plausible paths before the expensive work begins.

The same pattern appears in mathematics. A proof strategy can make visible progress for several steps before it becomes clear that a necessary lemma is false. A counterexample may look like a detour, but it can save a great deal of wasted effort.

It appears in organisational work as well. A team can commit to a large programme before understanding the real constraint, or run a smaller pilot that reveals which part of the problem is actually difficult.

The details do not transfer. A model cannot learn the value of one software decision and apply the same value directly to a scientific experiment.

The shape of the decision may transfer.

Premature commitment, hidden bottlenecks, one-way doors, recoverable detours, false progress, and useful experiments recur across many kinds of work. The objects change, but the underlying pattern does not always change as much as we assume.

That is why I think of this as a possible organising representation rather than another domain-specific value function. The system would not learn that a particular action is good in general. It would learn recurring patterns in how actions change what remains possible.

## Where should the next unit of intelligence go?

This brings the argument back to the question I started with.

More important decisions do not always need more thought. Some are consequential but obvious. More computation will not improve them.

Uncertain decisions do not always deserve more thought either. If the available choices quickly lead back to the same place, the uncertainty may not matter much.

Additional reasoning becomes most valuable when three things are true at once: the choices lead to different futures, the agent is not sure which path is better, and more thought or evidence could still change the decision.

That is a more useful rule than simply saying that difficult problems deserve more effort.

An agent should not reason equally hard about every step. It should spend more of its budget near the points where the future genuinely branches, especially when recovery will be difficult and the uncertainty can still be reduced.

The same representation could help elsewhere. Planning could search through the paths it exposes. Memory could preserve the assumptions that determine which routes remain viable. Recovery could decide whether a failure can be repaired locally or requires returning to an earlier fork. Oversight could focus on choices that are both hard to reverse and poorly specified by the human objective.

These may not be completely separate capabilities. They may all depend on some common understanding of how actions change the space of future possibilities.

## Perhaps the semantic substrate is already there

Building this kind of representation from scratch would be an enormous task. The system would need to understand software, mathematics, organisations, physical systems, language, and much more before it could reason about the consequences of actions inside them.

Foundation models may already contain part of what is needed.

They learn rich representations of text, code, images, audio, and increasingly the state of tools and environments. They can connect requirements with implementations, symptoms with possible causes, and errors with repairs.

What they may not have is a representation organised strongly enough around intervention and consequence.

Semantic similarity is not the same as similarity in what choices lead to. Two systems can look alike while responding very differently to the same intervention. One may have a reversible configuration problem, while the other is limited by the architecture itself.

The reverse can happen too. Two situations may look very different but share the same decision structure because the same kind of commitment, experiment, or recovery path is involved.

So the opportunity may not be to throw away the representations foundation models already have. It may be to reorganise or extend them around a different set of questions.

What changes if this action is taken? Which possibilities disappear? Which paths remain open? What becomes easier to learn? What can still be recovered later?

The answer does not have to be expressed in human-readable dimensions. The representation could remain high-dimensional and latent. Ideas such as reversibility or optionality may be useful ways for us to inspect it, without being the full story.

## Learning from richer experience

There is also a data problem.

Internet-scale training contains huge amounts of information about plans, decisions, failures, and outcomes. But most of it records the path that happened to be taken. It does not often show several alternatives starting from the same point and followed for long enough to reveal which early decision actually mattered.

A model can learn that certain actions are associated with success. That is not the same as learning how the alternatives would have reshaped the future.

It may need more consequence-rich experience: situations where different actions can be compared from similar starting points and their effects observed over longer horizons.

Counterfactual branching is one possible source of that experience. The system would not need to explore every possible choice. It could compare alternatives around the decisions that appear uncertain or consequential.

Some comparisons would show that the options were effectively interchangeable. Others would reveal that one small assumption changed the whole trajectory.

The expensive exploration could happen during training. What remains later is a cheaper intuition for when a decision deserves attention.

That feels closer to how human expertise works. Experienced people do not enumerate every future before acting. They recognise familiar shapes: a premature commitment, a hidden dependency, a misleading metric, a missing experiment.

The goal is not perfect foresight. It is a better sense of where the future is about to branch and which distinctions are worth caring about.

## An organising idea, not yet a theory

None of the ingredients here is entirely new. There are obvious connections to world models, successor representations, state abstraction, hierarchical reinforcement learning, information value, and rational metareasoning.

What interests me is the possibility that they point towards a common representation.

A foundation model could provide broad semantic understanding. A model of reachable futures could add a sense of how actions change what remains possible across different timescales. Planning, exploration, recovery, and reasoning allocation could then operate over that shared structure.

I am not sure that *geometry* will turn out to be the right mathematical language. A graph, a hierarchy, or a distribution over trajectories may prove more accurate. I am using the word because it helps expose the shape of the problem.

There are also serious limitations. The future is too large to represent in full, so any useful model must compress it. Compression can hide the rare event that later matters most.

Reachability depends on the agent itself. A stronger model, a new tool, or a larger budget changes what can be done. Open-ended intelligence also involves inventing new actions, not only choosing from an existing set.

And the representation can simply be wrong. A decision may look reversible when it contains a hidden one-way effect. Two situations may look structurally similar while differing in the one detail that matters.

None of this removes the need for evidence, monitoring, verification, or human judgement.

The real test is whether the abstraction transfers and whether it improves decisions. Can a system recognise the same underlying structure in engineering, mathematics, research, and planning? Can it tell the difference between real progress and movement along a misleading proxy? Can it use a limited reasoning budget better because it knows where the future genuinely branches?

I do not know yet. But I think the question is worth asking.

The previous article asked where an AI should spend its intelligence. One possible answer is that it first needs to represent not only the current world and the desired destination, but also the futures connecting them.

A capable long-horizon agent should notice one-way doors before walking through them. It should know when two choices are effectively the same and when a small decision changes everything that follows. It should recognise that an experiment can be more valuable than immediate action, and that apparent progress can still lead towards a dead end.

The question then becomes more precise than “What should I do next?”

> Which futures does this choice create or destroy, and is this where another unit of intelligence is worth spending?
