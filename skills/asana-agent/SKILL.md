---
name: asana-agent
description: >
  Execute daily Asana tasks assigned to the user. Use when asked to "run my tasks",
  "check my Asana queue", "process today's tasks", "work through my agent queue", or
  any request to execute tasks from Asana. Retrieves tasks where due date = today AND
  Task Progress = "Agent Queue", processes each sequentially by matching to relevant
  skills, completes deliverables, and closes out tasks with summary comments.
version: 1.0.0
---

# Asana Agent — Daily Task Executor

Automated workflow for processing Asana tasks assigned to the user. This agent retrieves queued tasks, processes them with relevant skills, and closes them out.

## Core Principles

1. **Understand before acting** — Never rush into execution. Tasks in the queue may be incomplete or require interpretation.
2. **Collaborate with the user** — This is an assisted workflow, not a fully autonomous one. The user remains in the loop.
3. **Use available skills** — Match tasks to the right skill for best results.

---

## Workflow

### 1. Retrieve Tasks

Load Asana GIDs from `~/.claude/memory/asana-config.md` for workspace, user, project, and custom field GIDs.

Search Asana for tasks matching ALL criteria:
- Assigned to: me
- Due date: today
- Custom field "Task Progress" = "Agent Queue"

Use `asana_search_tasks` with the workspace GID from config. If no tasks found, inform user and stop.

Present the task queue to the user with a brief summary of each task before proceeding.

---

### 2. Process Each Task Sequentially

For each task:

#### a) Comprehensive Task Review

Before doing anything else, gather ALL available context:

- Fetch full task with `asana_get_task` including custom fields
- Read: name, description (notes/html_notes)
- Retrieve comments/history via `asana_get_stories_for_task`
- Check for attachments via `asana_get_attachments_for_object`
- If attachments exist, review their contents
- Look for related context: parent tasks, subtasks, project context

**Important:** Do not limit analysis to just the task title and description. Many tasks are created as quick captures and may lack full context. Attachments, comments, and related items often contain the actual requirements.

#### b) Handle Attachments

- If attachments exist, prompt user to provide files if they are not accessible
- Review attachment contents as part of understanding the task scope
- Wait for user confirmation before continuing

#### c) Mandatory Clarification Phase

**Before beginning any execution work, ask 2-3 clarifying questions** to ensure full understanding of:

- The desired outcome and success criteria
- Any preferences for approach, format, or style
- Dependencies or constraints not explicitly stated
- Who the audience or stakeholder is (if relevant)

Even if the task seems straightforward, this step catches assumptions. Proceed only after user confirms.

---

### 3. Identify Relevant Skills

Dynamically evaluate available capabilities:

1. **Scan available skills** — Check `~/.claude/skills/` and any plugin skills for relevant matches
2. **Review skill descriptions** to match task requirements to the most appropriate skill(s)
3. **Consider combining skills** when a task spans multiple domains

If a relevant skill is identified:
- Read the SKILL.md file to understand its workflow and requirements
- Follow the skill's prescribed process

If no specific skill matches:
- Complete the task using general capabilities based on task description and clarified requirements

---

### 4. Execute Task

Complete the deliverable based on the task requirements and chosen approach. Save any output files as appropriate for the project context.

---

### 5. Close Out Task

After completing each task:

#### a) Add Comment
Use `asana_create_task_story` to add a plain-text summary:
- What was completed
- Files/deliverables created (with names)
- Any notes, caveats, or recommended follow-ups

#### b) Update Status
Use `asana_update_task` to set custom field "Task Progress" to "Pending Review"

#### c) Obtain User Sign-Off

**Before moving to the next task, explicitly confirm with the user:**
- Present a summary of what was completed
- Ask if the deliverable meets expectations
- Ask if any revisions are needed before proceeding

**Do not proceed to the next task until the user explicitly approves the current task.**

---

### 6. Repeat Until Queue Complete

Continue to next task only after sign-off. Repeat the full workflow for each task.

---

### 7. Session Wrap-Up

After all tasks are processed:

1. **Final Summary** — Consolidated summary of all tasks completed and any outstanding follow-ups
2. **Suggest follow-up tasks** — If execution surfaced new action items, suggest creating them in Asana
3. **Update memory** — If new context emerged (contacts, terms, project updates), suggest memory updates
