<%*
const taskId = await tp.system.prompt("Task ID (e.g. TASK-001)")
const taskTitle = await tp.system.prompt("Short title (plain words, no ID)")
const taskUrl = await tp.system.prompt("Tracker URL (empty if none)", "")
await tp.file.rename(taskId + "-index")
%>
---
tags: task
task: <% taskId %>
url: <% taskUrl %>
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% taskId %> — <% taskTitle %>

<2–3 lines: what it does and why.>

<!-- The task's index: the one place that says what happened during the work.
     Link, don't copy. The four bold lines are state (always true now); the sections below are an append-only, dated record. -->

**Status:** <tracker state + where the code is: uncommitted / pushed / in review>

**Next action:** <one verb phrase>

**Blocked on:** <nothing | what, and whose>

**Started:** <% tp.date.now("YYYY-MM-DD") %> · **Shipped:** —

## Sub-notes
-

## Decisions
<!-- One line each: the choice, the rejected alternative, a link to where the why lives. -->
-

## Interactions
<!-- Dated. Who asked or answered what, and what it changed. -->
-

## Review
- **not reviewed**

## QA
- **not run**

## PRs
-

## Attempted & abandoned
-
