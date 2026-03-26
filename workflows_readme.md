# AI Coding Agent - Recommended Workflow

## Overview: The 7-Stage Pipeline

```
Research → Plan → Implement → Validate → Commit → PR → Handoff (if needed)
```

Each stage has a dedicated slash command. Here's how to use them for different scenarios:

---

## Scenario 1: New Feature

### Stage 1 - Research the codebase
```
/research_codebase
```
Then provide your question, e.g.: *"How does the approval flow work between hlyr and hld?"*

This generates a research document at `thoughts/shared/research/YYYY-MM-DD-description.md` with findings, code references, and architecture documentation.

**Which variant to use:**
| Command | When to use |
|---|---|
| `/research_codebase` | Full research with `thoughts/` history |
| `/research_codebase_nt` | Research without thoughts infrastructure |
| `/research_codebase_generic` | Quick research, allows suggestions too |

### Stage 2 - Create an implementation plan
```
/create_plan
```
Then describe the feature or provide a ticket file, e.g.: *"Add webhook retry logic for failed approvals"*

This is **interactive** - Claude will:
1. Research the codebase automatically
2. Ask clarifying questions
3. Propose design options
4. Get your approval on plan structure
5. Write a detailed phased plan to `thoughts/shared/plans/YYYY-MM-DD-description.md`

**Variants:**
| Command | When to use |
|---|---|
| `/create_plan` | Full planning with thoughts + Linear integration |
| `/create_plan_generic` | Generic planning without thoughts infra |
| `/create_plan_nt` | Planning without thoughts directory |

### Stage 3 - Implement the plan
```
/implement_plan thoughts/shared/plans/2026-03-25-webhook-retry.md
```
Claude will:
1. Read the plan fully
2. Implement phase by phase
3. Run automated verification after each phase
4. **Pause for your manual testing** before moving to next phase
5. Check off items in the plan as completed

### Stage 4 - Validate the implementation
```
/validate_plan thoughts/shared/plans/2026-03-25-webhook-retry.md
```
Generates a validation report comparing what the plan specified vs what was actually implemented, runs all automated checks, and lists remaining manual tests.

### Stage 5 - Commit changes
```
/commit
```
Analyzes your changes, groups them logically, proposes commit messages, and waits for your approval before committing.

### Stage 6 - Create PR description
```
/describe_pr
```
Reads the diff, runs verification checks, and generates a full PR description from the repo's template.

---

## Scenario 2: Bug Fix / Defect

```
/debug                          <- Investigate logs, DB, git state
/research_codebase              <- Understand the affected area
/create_plan                    <- Plan the fix (if non-trivial)
/implement_plan <plan-path>     <- Execute the fix
/validate_plan <plan-path>      <- Verify it's correct
/commit                         <- Commit
/describe_pr                    <- PR
```

For simple bugs, you can skip `/create_plan` and fix directly, then `/commit`.

---

## Scenario 3: Iterating on an existing plan

```
/iterate_plan thoughts/shared/plans/2026-03-25-feature.md
```
Then provide feedback like *"Add a phase for error handling"* or *"Split Phase 2 into backend and frontend"*. Claude will research if needed, confirm changes, and update the plan surgically.

---

## Scenario 4: Handing off / Resuming work across sessions

When you need to stop mid-work:
```
/create_handoff
```
Creates a handoff doc at `thoughts/shared/handoffs/` with tasks, learnings, artifacts, and next steps.

To resume in a new session:
```
/resume_handoff thoughts/shared/handoffs/ENG-1234/2026-03-25_14-30-00_ENG-1234_description.md
```
Or just by ticket number:
```
/resume_handoff ENG-1234
```

---

## Quick Reference Cheat Sheet

| Stage | Command | Purpose |
|---|---|---|
| Investigate | `/debug` | Debug issues via logs, DB, git |
| Research | `/research_codebase` | Understand codebase areas |
| Plan | `/create_plan` | Create phased implementation plan |
| Iterate | `/iterate_plan <path>` | Refine an existing plan |
| Implement | `/implement_plan <path>` | Execute plan phase by phase |
| Validate | `/validate_plan <path>` | Verify implementation vs plan |
| Commit | `/commit` | Create git commits |
| PR | `/describe_pr` | Generate PR description |
| Handoff | `/create_handoff` | Save context for next session |
| Resume | `/resume_handoff <path>` | Pick up where you left off |

---

## Typical Flow Diagram

```
  New Feature              Bug Fix                Resuming Work
  -----------              -------                -------------
  /research_codebase       /debug                 /resume_handoff
       |                      |                        |
  /create_plan             /research_codebase      (reads plan)
       |                      |                        |
  /implement_plan          /create_plan (optional)  /implement_plan
       |                      |                        |
  /validate_plan           /implement_plan          /validate_plan
       |                      |                        |
  /commit                  /commit                  /commit
       |                      |                        |
  /describe_pr             /describe_pr             /describe_pr
       |                      |                        |
  /create_handoff          (done)                   /create_handoff
  (if stopping mid-work)                           (if stopping again)
```
