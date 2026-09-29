# skills

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

**English** | [简体中文](README.zh-CN.md)

## Overview

skills is an open-source collection of agent skills, ready to use out of the box. Each skill defines a complete working pattern, applicable to any agent environment with subagent-spawning capability — independent of any specific tool.

## Skills

| Skill | Summary | Invocation |
|---|---|---|
| [dispatcher](dispatcher/SKILL.md) | Dispatch mode: submit a task list; the dispatcher assigns workers to complete every task | `/dispatcher` |
| [deepen-review-push](deepen-review-push/SKILL.md) | Deterministic pipeline: deepen a directory's top refactor candidate (scan → implement → review → push) straight to `main` | `/deepen-review-push` |

## dispatcher

Provide a task list, and the main session takes the role of the **dispatcher**, assigning work to workers (SubAgents) one by one in serial order. The dispatcher handles exactly two responsibilities — **dispatching and acceptance**; all actual work is performed by workers.

```
Loop: order (blockers first) → dispatch → accept
  ├─ Accepted, tasks remain  → next round
  ├─ Accepted, all done      → final report (results + open items)
  ├─ Rejected                → cleanup agent fills the gaps, then re-acceptance
  └─ No dispatchable tasks   → wrap up; blocked tasks recorded as open items
```

Reliability by design:

- **Workers run on fresh context** — every dispatch order must carry six elements (work items, operating procedure, required reading, settled decisions, hard constraints, completion actions) and depends on no session history
- **Acceptance relies on actual output**; summaries serve only as a clue — preventing falsely reported completion
- **Generic slots** — the semantics of tasks, submission, and verification are defined by the invoking instruction; the same process can dispatch any kind of work
- **Interruption recovery** — when a worker is stopped or errors out: unfinished artifacts are taken over by a cleanup agent; with a clean workspace, the remaining tasks are redispatched as-is

### Usage

Prerequisite: the environment must be able to spawn subagents.

```
/dispatcher <task list; optionally specify a procedure and decision authority>
```

User-invoked: the model never triggers it automatically; it runs only on explicit user command.

## deepen-review-push

A deterministic pipeline that deepens the top refactor candidate of a single directory and pushes it straight to `main`: the main session runs the pipeline itself, spawning a single worker only for the implement/fix steps.

```
Baseline (lock fixed-point SHA, verify main)
  → scan directory (improve-codebase-architecture → Top candidate)
  → worker implements → review vs fixed-point (code-review)
  → one fix round → re-review → push to main
```

Reliability by design:

- **Deterministic stages** — every step carries explicit completion criteria and blocking conditions; on any blocker the run terminates and reports the actual state plus recovery actions, never auto-repairs
- **One topic, one agent** — only the implement/fix steps spawn a worker, and the dispatch order carries just that step's materials, never the remaining pipeline
- **Fixed-point review** — review always covers `git diff <fixed-point>...HEAD`, so the verdict judges exactly this run's changes; the Top card is passed in as the explicit spec
- **One fix round only** — hard Standards violations and Spec gaps go back to the same worker exactly once; judgement-call smells are recorded but never block the push
- **Fail over retry** — a rejected push (remote ahead, branch protection) is reported as-is, no retries

### Usage

Prerequisite: the environment must be able to spawn subagents, and the skills `improve-codebase-architecture` and `code-review` must be available.

```
/deepen-review-push <target directory; asked once if missing>
```

User-invoked: the model never triggers it automatically; it runs only on explicit user command.

## Installation

```bash
git clone https://github.com/Buktal/skills.git
cp -r skills/dispatcher skills/deepen-review-push <your-agent-skill-directory>/
# Common location: ~/.agents/skills/ (cross-tool convention), or your tool's own skill directory
```

## License

Released under the [MIT License](LICENSE).
