# skills

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

[English](README.md) | **简体中文**

## 概述

skills 是开箱即用的开源 agent skill 集合。每个 skill 定义一种完整的工作模式，适用于任何具备子代理（SubAgent）派生能力的 agent 环境，与具体工具无关。

## Skills

| Skill | 简介 | 调用 |
|---|---|---|
| [dispatcher](dispatcher/SKILL.md) | 调度模式：提交一份任务清单，调度员自动派 worker 逐个完成 | `/dispatcher` |
| [deepen-review-push](deepen-review-push/SKILL.md) | 确定性流水线：深挖单目录 Top 重构候选（扫描→实施→review→推送）直推 `main` | `/deepen-review-push` |

## dispatcher

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

## deepen-review-push

针对单目录 Top 重构候选的确定性深挖流水线，一路直推 `main`：主会话自行执行流水线，仅在实施/修复步骤派单个 worker。

```
基线（锁定 fixed-point SHA，校验 main）
  → 扫描目录（improve-codebase-architecture → Top 候选）
  → worker 实施 → 按 fixed-point review（code-review）
  → 单轮修复 → 重审 → 推送 main
```

可靠性设计：

- **确定性阶段**——每个步骤自带完成标准与阻断条件；任何阻断即终止并上报实际状态与恢复动作，绝不自动修补
- **一题一代理**——仅实施/修复步骤派 worker，且派工材料只含当步内容，不含流水线后续步骤
- **fixed-point review**——review 范围恒为 `git diff <fixed-point>...HEAD`，判定只覆盖本次运行的改动；Top 卡片作为显式 spec 传入
- **仅一轮修复**——Standards 硬违规与 Spec 缺口原路退回同一 worker，只修一轮；judgement-call 的 smell 只记录、不阻断推送
- **失败即上报**——推送被拒（远端超前、分支保护）原样上报，不重试

### 使用

前提：所处环境具备派生子代理（SubAgent）的能力，且已安装 `improve-codebase-architecture` 与 `code-review` 两个 skill。

```
/deepen-review-push <目标目录；缺失时询问一次>
```

用户显式调用（user-invoked）：模型不会自动触发，仅响应用户指令，启动时机完全由用户控制。

## 安装

```bash
git clone https://github.com/Buktal/skills.git
cp -r skills/dispatcher skills/deepen-review-push <所用 agent 的 skill 目录>/
# 常见位置：~/.agents/skills/（跨工具通用约定），或所用工具自身的 skill 目录
```

## 许可证

本项目基于 [MIT License](LICENSE) 开源。
