# skills

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

开源的 agent skill 集合，开箱即用——每个 skill 定义一种完整的工作模式，适用于 Claude Code 及任何支持 SKILL.md 与 SubAgent 的 agent 环境。

## Skills

| Skill | 简介 | 调用 |
|---|---|---|
| [dispatcher](dispatcher/SKILL.md) | 调度模式：提交一份任务清单，调度员自动派 worker 逐个完成 | `/dispatcher` |

## dispatcher

只需提供一份任务清单，主会话即化身**调度员**，串行派 worker（SubAgent）逐个完成。调度员只负责两件事：**派工、验收**——全部实际工作由 worker 完成。

```
循环：排序（blocker 在前）→ 派工 → 验收
  ├─ 通过、还有未完成任务 → 下一轮
  ├─ 通过、全部完成       → 收尾汇报（成果 + 遗留事项）
  ├─ 不通过               → 收尾 agent 补完缺漏，重新验收
  └─ 无可派任务           → 收尾，被卡任务写进遗留事项
```

可靠性设计：

- **worker 是全新上下文**——派工指令必须自带六件事（领取对象、操作规程、必读材料、已定稿决策、硬约束、完成动作），不依赖任何会话历史
- **验收以实际产出为准**，摘要仅作参考——防止虚报完成
- **通用槽位**——任务、提交、验证的语义由调用指令定义，不绑定任何具体领域或工具，同一套流程可调度任何类型的工作
- **中断恢复**——worker 中断或报错时：半成品由收尾 agent 接手，现场干净则原样重派

### 使用

前提：环境具备 SubAgent 能力（如 Claude Code 的 Agent 工具）。

```
/dispatcher <任务清单；可附规程指名、决策授权>
```

user-invoked：模型不会自动触发，仅由用户显式调用，启动时机完全可控。

## 安装

```bash
git clone https://github.com/Buktal/skills.git

# 全局（所有会话可用）
cp -r skills/dispatcher ~/.agents/skills/

# 或项目级
cp -r skills/dispatcher <project>/.claude/skills/
```

## License

本项目基于 [MIT License](LICENSE) 开源。
