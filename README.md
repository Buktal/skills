# skills

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

**English** | [简体中文](#简体中文)

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

---

## 简体中文

skills 是开箱即用的开源 agent skill 集合。每个 skill 定义一种完整的工作模式，适用于任何具备子代理（SubAgent）派生能力的 agent 环境，与具体工具无关。

### Skills

| Skill | 简介 | 调用 |
|---|---|---|
| [dispatcher](dispatcher/SKILL.md) | 调度模式：提交一份任务清单，调度员自动派 worker 逐个完成 | `/dispatcher` |

### dispatcher

只需提供一份任务清单，主会话即担任**调度员**，串行派 worker（SubAgent）逐个完成。调度员仅承担两项职责：**派工、验收**——全部实际工作由 worker 完成。

```
循环：排序（blocker 优先）→ 派工 → 验收
  ├─ 通过，存在未完成任务 → 下一轮
  ├─ 通过，全部完成       → 收尾报告（成果与遗留事项）
  ├─ 未通过               → 收尾 agent 补正缺漏，重新验收
  └─ 无可派发任务         → 收尾，被阻塞任务写入遗留事项
```

可靠性设计：

- **worker 是全新上下文**——派工指令必须自带六项要素（领取对象、操作规程、必读材料、已定稿决策、硬约束、完成动作），不依赖任何会话历史
- **验收以实际产出为准**，摘要仅作参考——防止虚报完成
- **通用槽位**——任务、提交、验证的语义由调用指令定义，不绑定任何领域或工具，同一套流程可调度任何类型的工作
- **中断恢复**——worker 中断或报错时：未完成产物由收尾 agent 接手，现场无遗留则原样重新派发

### 使用

前提：所处环境具备派生子代理（SubAgent）的能力。

```
/dispatcher <任务清单；可附规程指名、决策授权>
```

用户显式调用（user-invoked）：模型不会自动触发，仅响应用户指令，启动时机完全由用户控制。

### 安装

```bash
git clone https://github.com/Buktal/skills.git
cp -r skills/dispatcher <所用 agent 的 skill 目录>/
# 常见位置：~/.agents/skills/（跨工具通用约定），或所用工具自身的 skill 目录
```

### 许可证

本项目基于 [MIT License](LICENSE) 开源。
