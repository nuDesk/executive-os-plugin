---
name: executive-planning
description: >
  Reference document: Executive-level prioritization framework. This framework is embedded directly
  in /daily-plan, /strategic-review, and /weekly-report commands. Do NOT invoke this skill
  directly — use the appropriate command instead.
version: 2.0.0
---

# Executive Planning

> Note: This skill is a reference document only. It is not invoked directly.
> To plan your day: /daily-plan. For strategic planning: /strategic-review. For weekly reporting: /weekly-report.

Prioritization framework for synthesizing signals from Asana, Calendar, Gmail, Fireflies, and HubSpot into a clear daily action plan. Designed for an executive managing multiple client accounts, internal operations, and business development simultaneously.

## Configuration

This skill reads all user-specific context from two files:
- **`~/.claude/CLAUDE.md`** — User identity, priorities, strategic goals, working memory (people, terms, clients)
- **`~/.claude/memory/asana-config.md`** — Asana workspace, project, and custom field GIDs

No hardcoded user data exists in this skill. All personalization comes from those config files.

## Prioritization Framework

Rank all items using this hierarchy:

### Tier 1: Non-Negotiable (do first)
- Calendar commitments (meetings, calls)
- Client deliverable deadlines
- Commitments made in recent meetings (Fireflies action items)
- Anything blocking someone else's work

### Tier 2: Strategic (do today)
- Items aligned with CLAUDE.md top priorities
- Revenue-impacting activities (pipeline, BD, client relationships)
- Tasks that have been overdue >2 days

### Tier 3: Important (schedule this week)
- Operational tasks (invoicing, compliance, admin)
- Internal process improvements
- Content and marketing activities

### Tier 4: Delegate or Defer
- Tasks assignable to team members (check CLAUDE.md Working Memory for team)
- Low-urgency follow-ups
- Nice-to-have improvements

## Effort Tagging

Assign one tag to each action item:

| Tag | Duration | Type |
|-----|----------|------|
| 15m | Under 15 minutes | Quick reply, approval, review |
| 30m | 15-30 minutes | Short task, email draft, brief call |
| 1hr | 30-60 minutes | Focused task, document review, planning |
| 2hr | 1-2 hours | Deep deliverable, analysis, strategy |
| Deep | 2+ hours | Major project work, complex creation |

## Context Awareness

When synthesizing, account for:
- **Calendar density** — Heavy meeting days mean only quick tasks fit in gaps
- **Energy management** — Front-load strategic/creative work, back-load admin
- **Stakeholder urgency** — Requests from key contacts (listed in CLAUDE.md) get elevated; internal admin gets deferred
- **Momentum** — If a project has been stalled, consider prioritizing to unblock

## Source-Specific Extraction

### Asana Signals
- Overdue tasks = highest urgency
- "Agent Queue" tasks = can be automated via /run-tasks (call this out)
- Tasks in client projects = client-facing urgency

### Calendar Signals
- Meeting prep needed? Flag 30min before each meeting
- Back-to-back meetings = no deep work blocks available
- Open blocks = opportunity for Tier 2 strategic work

### Gmail Signals
- **Default query filter:** `to:<user-email> is:important` — always apply as base filter
- Emails from key contacts (see CLAUDE.md Working Memory People section) = elevated
- Threads with >3 replies = likely needs attention
- Emails >24hrs old without response = flag

### Fireflies Signals
- Action items from past 48hrs = commitments to honor
- Decisions made in meetings = may need follow-through tasks

### HubSpot Signals
- Deals approaching close date = revenue urgency
- Recent activity on dormant deals = opportunity to capitalize
- New contacts added = possible follow-up needed

## Anti-Patterns to Avoid

- **Overloading the plan** — Max 10 priority actions. Anything beyond goes to Watch List.
- **Treating all tasks equally** — Executive time is the scarcest resource. Ruthlessly prioritize.
- **Ignoring energy** — Don't schedule deep work after 4 back-to-back meetings.
- **Missing commitments** — Fireflies action items represent promises. Never bury these.
- **Forgetting delegation** — Flag items that team members should own (check CLAUDE.md for team).

## Output Standards

- Clean, scannable format — no walls of text
- Effort tags on every action item
- Source attribution (Asana, Gmail, Calendar, etc.) so user knows where it came from
- Separate sections for meetings, priority actions, emails, automatable tasks, and watch list
