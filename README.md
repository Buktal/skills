# skills

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

**English** | [简体中文](README.zh-CN.md)

## Overview

skills is an open-source collection of agent skills, ready to use out of the box. Each skill defines a complete working pattern, applicable to any agent environment with subagent-spawning capability — independent of any specific tool.

## Skills

| Skill | Summary | Invocation |
|---|---|---|
| [dispatcher](dispatcher/SKILL.md) | Dispatch mode: submit a task list; the dispatcher assigns workers to complete every task | `/dispatcher` |

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

## Installation

```bash
git clone https://github.com/Buktal/skills.git
cp -r skills/dispatcher <your-agent-skill-directory>/
# Common location: ~/.agents/skills/ (cross-tool convention), or your tool's own skill directory
```

## License

Released under the [MIT License](LICENSE).
