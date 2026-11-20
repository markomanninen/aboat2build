# How the work runs

One person builds the boat and three AI agent swarms do the desk work. This page describes the rules that keep that work checkable, and each rule names the fault it exists to catch.

## One work queue

Every task is an item in one shared tree. An item has an owner, a status, the tasks it depends on and a list of messages. An agent takes a task by writing its own name as the owner, and it finishes the task by writing the result into the same item. The tree is kept with [Draversal](https://github.com/markomanninen/draversal), a small open tool for shared task trees.

The queue catches work that two agents would otherwise do twice, and work that nobody owns.

## One decision queue

A choice that belongs to the builder goes to a second list, which he reads on his phone. Each entry is one decision with a recommendation, and approving the entry carries the recommendation out. When an agent makes a choice by itself, it records the choice in the same list with its reasons, so that the builder can reverse it later.

The decision queue catches choices that would otherwise be made silently inside a long agent session.

## Messages between agents

Agents from different tools do not share a memory. An agent that needs another one appends a message to the task in the work queue, with a sender and a receiver. A small watcher program delivers the message to the receiving agent's chat, and the answer comes back the same way.

## The cold reader

A cold reader is a fresh agent that is given one document and nothing else. It reads the document sentence by sentence, asks what each sentence needs the reader to know already, and recomputes every number. One cold read of the build plan found 119 faults, among them one decision that carried two dates.

The writer of a plan fills its gaps from its own knowledge without noticing. The cold reader has only the text, so it stops where a first-time reader would stop.

## The paper build

Before money is spent, an agent builds the boat on paper. It gets the shopping list and the build instructions, follows them step by step and notes every moment when a part, a tool or an instruction is missing. The first paper build found 29 gaps, and four of them would have stopped the build.

## A reviewer from another vendor

Code and plans written by one tool are reviewed by a different tool. A model tends to repeat its own blind spots when it reviews its own work, and a model from another vendor has different ones.

## The plan as data

The build steps, the parts and the dates live in data files. A script computes the schedule from them, and it checks that every sum, date and hour total in the written plan agrees with the data. The same script answers what-if questions, for example how many days the first water test moves when one order arrives late.

Before this rule the same date was written by hand in several sections, and the sections drifted apart from one draft to the next.

## Tests on the builder's own machine

Tests run on the local machine before every push. A push hook exports the pushed commit into a clean folder and runs the tests there inside one Docker image, so that a file lying around in the working folder cannot make a test pass.

## The builder's hours as a fixed limit

The builder has 89 working hours before the competition. Every draft of the plan states how many hours it needs and what is dropped first when the hours run out. A plan that needs more hours than exist is treated as a fault in the plan.
