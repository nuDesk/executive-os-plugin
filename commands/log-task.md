---
description: Create an Asana task with smart project routing
argument-hint: [task description]
allowed-tools: Read, Grep
---

Create an Asana task from a quick description. Smart-route it to the right project based on context.

## Config

Load Asana GIDs from `~/.claude/memory/asana-config.md` before making any API calls. This file contains workspace, user, project, and custom field GIDs — use them directly instead of searching.

## Input

The user provides a task description as $ARGUMENTS. Examples:
- `/log-task Follow up with client on check-in`
- `/log-task Draft monthly report for January`
- `/log-task Review candidate interview notes`

## Step 1: Parse Intent

Extract from the description:
- **Task name** — clean, action-oriented title
- **Due date** — if mentioned or implied; otherwise default to tomorrow
- **Project** — infer from context using the routing table in asana-config.md
- **Type** — match to the Type custom field enum (e.g., Account Mgmt., Admin, Business Dev)
- **Priority** — default Medium unless urgency is implied
- **Notes** — any additional context from the description

## Step 2: Smart Project Routing

Match keywords in the task description against the routing table in `~/.claude/memory/asana-config.md`. Use the project GID directly — no typeahead search needed.

**If confidence is low** (no clear context clues), ask: "Which project should this go in?" and present the likely options.

## Step 3: Confirm Before Creating

Present the task details:
```
Task: [Name]
Project: [Project Name]
Due: [Date]
Type: [Type]
Priority: [Priority]
Notes: [Any additional context]
```

Ask: "Create this task?" — wait for confirmation.

## Step 4: Create in Asana

Use `asana_create_task` with:
- `project_id`: from routing table (no workspace needed when project_id is set)
- `assignee`: `me`
- `name`, `due_on`, `notes`: from parsed intent
- `custom_fields`: set Task Progress (Not Started), Type, and Priority using GIDs from config

Confirm completion with the task link.
