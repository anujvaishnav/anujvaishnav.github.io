---
title: "State Is Not Context: What Long-Horizon Agents Need to Preserve"
subtitle: "Why remembering more is not the same as knowing where you are"
date: 2026-09-24
tags: [AI, agents, memory]
description: >-
  A long agent run accumulates records, not a current position. The harder
  capability is knowing what the past has made true and what has ceased to be.
---

In [The Geometry of Reachable Futures](/blog/the-geometry-of-reachable-futures/), I wrote that a state should be represented partly by the futures it leaves open. Since then, I have kept getting stuck on the word *state*.

We use it rather loosely when talking about agents. The prompt is state. The conversation is state. The files, tool outputs, notes, retrieved documents, current screen and task list all become parts of state. Put enough of them into a context window and the agent appears to know where it is.

But those things are mostly records and observations. They may contain the current state somewhere inside them. They do not necessarily tell the agent what is true now.

## The problem is not forgetting

Consider a coding agent working through a large repository to remove a deprecated API. Early in the run, it discovers that one caller cannot be migrated until a generated schema has been changed. It writes this down as a blocker. It then updates the schema, regenerates the code, fixes the caller and continues through another twenty files.

Several hours later, the context has been compacted. The agent retrieves the earlier note because it is highly relevant to the migration. The note still says that the caller is blocked on the schema change. That statement was correct when it was written, but the world described by it no longer exists. If the agent treats it as current, it may reopen work that has already been completed, avoid a path that is now available, or try to solve the same blocker in a different way.

This is not ordinary forgetting. The old note, the schema change, the generated files and the passing tests may all still be available. The problem is that the agent has several versions of the task in front of it and no reliable account of which version governs the next decision.

A human reading the sequence would usually understand what happened. The note became stale after the schema changed. We treat one statement as historical and another as current without making the distinction explicit every time. Long agent runs put all of these statements into the same medium, usually text, where an old assumption and a current fact can look equally authoritative.

That is the gap I am interested in.

## History is not the present

A history tells us what happened along the way. The useful state is whatever those events have made true now. It includes the effects of the work, not just a compressed description of the work itself.

In a small task, the difference barely matters. The model can reread the request, inspect the latest file and continue. Over a longer run, more things acquire a history. Requirements are clarified. Hypotheses are weakened. Temporary workarounds appear and disappear. A test failure that once pointed towards the implementation later turns out to have come from the test setup. A safe rollback remains available until one external change quietly removes it.

Simply keeping all of this in context does not resolve it. More context can mean more competing descriptions of the present. Retrieval helps the agent find relevant material, but relevance is not the same as authority. A stale plan can be exactly about the current problem. A summary can mention both an early hypothesis and the evidence that overturned it, while leaving the agent to work out which one it should act on.

The phrase *state* already carries a useful expectation in control and reinforcement learning: history should be reduced to something sufficient for deciding what happens next. Open-ended agent work makes this awkward because the state is rarely handed over cleanly by an environment. The agent has to infer it from code, conversations, tool calls, measurements, documents and changes it made itself.

I am not sure that current systems treat this as a distinct problem. Plans, scratch files, task lists, summaries and retrieval stores all help, and modern harnesses combine several of them. The live environment carries part of the truth as well. The repository says which files actually changed. The test runner says what currently fails. A browser or simulator exposes the result of the latest action.

Yet these pieces do not automatically form one coherent working picture. The plan may still describe the task before the last experiment. The summary may preserve a conclusion after its supporting evidence has been overturned. A task list may say that a migration step is complete while the deployed environment is still on the old version. Each artefact can be reasonable on its own and the collection can still be wrong.

## The difficult operation is supersession

When new evidence arrives, some of it should be appended. Some of it should change the confidence attached to an existing belief. Some should close a question, reopen an earlier decision, or alter the meaning of an observation already made. The old information may still be useful as evidence, but it should no longer occupy the same place in the agent's active understanding.

Human teams do this informally. Someone says, "that was before we changed the schema", and the group adjusts. An engineer stops treating an early benchmark as representative after seeing production traffic. A researcher keeps the record of a failed experiment but changes which explanation seems plausible. The past remains accessible, while the working account of the present moves on.

An agent needs some version of that process. Otherwise, its memory gradually becomes an archive that it mistakes for a world model.

## What should survive compression?

What belongs in the active state is less obvious. Preserving everything defeats the purpose. Discarding too aggressively removes the reason why certain paths are open or closed.

The exact output of a compiler command probably does not need to remain prominent after the failure has been understood. The unsupported dependency revealed by that failure may matter for the rest of the migration. Ten searches that found nothing can usually fade into the background. One failed approach may need to stay visible because it rules out the most tempting alternative. A single configuration detail may matter more than pages of discussion if it determines whether rollback is still possible.

My current intuition is that the state should preserve distinctions that could still change a future decision. That is a different goal from writing a good summary. A summary is usually judged by whether it represents the past faithfully. A state should be judged by whether the agent can continue well from it.

This also means that two very different histories can lead to effectively the same state. One agent may reach a working implementation after three failed attempts and another may find it immediately. If the same code is now deployed, the same uncertainty remains and the same options are available, much of that difference can disappear from the active state.

The reverse is more important. Two nearly identical runs may differ in one quiet detail. Perhaps one has already changed an external interface, or used data that cannot be recovered, or made a promise to a user. The visible work may look the same, while the safe next actions are completely different. That detail has to survive compression.

## Preserving the shape of what remains possible

This is where the idea joins the geometry of reachable futures. The state does not need to preserve every event. It needs to preserve what those events did to the shape of what remains possible. Which routes were closed? Which assumptions still hold the plan together? What became easier to measure? What can still be undone? Which tool or piece of knowledge made a previously unreachable action practical?

Capabilities matter here more than I first appreciated. Suppose the coding agent builds a small migration tool halfway through the task. The repository may be unchanged at that moment, but the agent can now perform an operation that was too expensive or error-prone before. Its position has changed because its ability to act has changed. The same applies when an experiment removes uncertainty or when a new source of evidence becomes available. The external system is only part of the present.

## A working state backed by evidence

There is an uncomfortable edge to this idea. A detail that looks irrelevant now may become important later. A discarded failure might contain the clue needed after the environment changes. No finite state can preserve everything, and no compression process can know with certainty which rare fact will matter several hours later.

That makes me reluctant to imagine one perfect state document that the agent continually rewrites. A more plausible arrangement may have a small working state backed by a larger evidence store. The working state says what the agent currently believes, what remains unresolved and why the active constraints matter. The evidence remains available when a belief is challenged or an old branch becomes relevant again.

Even then, the hard question remains: who decides that a belief has been superseded, or that a detail has become important enough to promote back into the working state? Another model call can perform the update, but that only moves the judgement into a new prompt. A structured schema can force useful fields such as evidence, confidence and status, but the schema cannot decide whether the latest observation changes the problem.

## Judge the state by what happens next

Perhaps this judgement has to be learned from continuations. Give an agent the full trajectory, let it construct a working state, then remove the trajectory and see what happens next. Does it remember that the schema blocker was solved? Does it keep the constraint that still limits the migration? Can it explain why an attractive approach was ruled out and recover the evidence when challenged? If one small change makes rollback impossible, does that change appear in the state even when most of the run is identical?

This would evaluate the state through its consequences rather than through how polished or comprehensive the summary looks. It would also expose a common failure more clearly. A model can reason quite well from a stale premise. The resulting action may be internally sensible and completely wrong for the task that now exists.

Some failures that look like weak planning may therefore begin earlier. The agent may be choosing reasonably from an outdated picture of its options. More search will not help much if the branch it is searching should already have been removed. A larger context window may make the stale evidence easier to find.

The opposite is possible too. Once the active constraint, unresolved uncertainty and remaining commitments are represented clearly, the next action may require less elaborate planning. Better direction can emerge from a better account of the present.

## The capability underneath planning

This connects back to the question from my first article: where is the next unit of intelligence worth spending? The geometry article suggested looking near points where choices reshape the future. I now think both ideas depend on a quieter capability underneath them. The agent has to keep compiling its experience into a current position from which those choices can be understood.

I do not yet know how much of this should live inside the model and how much should be made explicit in the harness. It is probably some combination. The environment is often the best source of truth about what changed. External memory is useful for evidence and durable commitments. The model is still needed to work out what those changes mean for the task.

Long-horizon agents clearly need better memory. I am less convinced that remembering more, by itself, gets us much closer. The harder capability may be learning what the past has made true, what has ceased to be true, and which of those changes can still alter the future.

After a thousand steps, what should the agent believe has become true, and what evidence would still make it change its mind?
