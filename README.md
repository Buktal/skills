# skills

个人维护的 agent skill 集合——每个 skill 定义一种工作模式，给 Claude Code（或任何支持 SKILL.md 与 SubAgent 的 agent 环境）使用。

## 目录

| Skill | 一句话 | 调用 |
|---|---|---|
| [dispatcher](dispatcher/SKILL.md) | 调度模式：调度员派工 + 验收，worker 完成全部实际工作，直到任务清单清空 | `/dispatcher` |

## dispatcher

主会话化身**调度员**，把一份任务清单串行派给 worker（SubAgent）完成。调度员只做两件事：**派工、验收**——自己不动手。

```
循环：排序（blocker 在前）→ 派工 → 验收
  ├─ 通过、还有未完成任务 → 下一轮
  ├─ 通过、全部完成       → 收尾汇报（成果 + 遗留事项）
  ├─ 不通过               → 收尾 agent 补完缺漏，重新验收
  └─ 无可派任务           → 收尾，被卡任务写进遗留事项
```

设计要点：

- **worker 是全新上下文**——派工指令必须自带六件事：领取对象、操作规程、必读材料、已定稿决策、硬约束、完成动作
- **验收以实际产出为准**，摘要只是线索——不为齐活硬关单
- **通用槽位**——与代码、issue、具体工具无关：任务、提交、验证的含义由调用指令定义，同一套流程调度任何类型的工作
- **中断兜底**——半成品有收尾 agent 接手，现场干净则原样重派，质量问题修正后提交

### 使用

前提：环境具备 SubAgent 能力（如 Claude Code 的 Agent 工具）。

```
/dispatcher <任务清单；可附规程指名、决策授权>
```

user-invoked：模型不会自动触发，只由人调用。

## 安装

```bash
git clone https://github.com/Buktal/skills.git

# 全局（所有会话可用）
cp -r skills/dispatcher ~/.agents/skills/

# 或项目级
cp -r skills/dispatcher <project>/.claude/skills/
```

## License

[MIT](LICENSE)
