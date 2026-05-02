# 🎻 Orchestra Fusion

> 多智能体编排融合方案 — ATO × DevFleet × dmux × OMO 四源基因杂交

[![version](https://img.shields.io/badge/version-1.3.0-blue)](SKILL.md)
[![license](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![workbuddy](https://img.shields.io/badge/platform-WorkBuddy-orange)](https://www.codebuddy.cn)

## 一句话

你说"用 agent 团队构建 X"，Orchestra Fusion 自动完成 Intent → Plan → DAG → Dispatch → Monitor → Report 全流程多智能体编排。

## 基因来源

| 源技能 | 贡献基因 |
|--------|---------|
| **Agent Team Orchestration** | 角色分工、任务生命周期、Handoff、强制审查 |
| **Claude DevFleet** | DAG 依赖规划、auto_dispatch、工作树隔离、看板 |
| **dmux-workflows** | 6 种并行模式、merge 策略 |
| **oh-my-opencode** | Slot-Based 并发、熔断器、Intent Gate、陈旧检测 |

## 30 秒上手

```
👤 "用agent团队构建博客系统"
🤖 Intent Gate → Plan(DAG+并发) → 用户确认 → Dispatch → Monitor(看板) → Done
```

## 核心能力

- 🎯 **6 种并行模式**：Research+Build / Multi-Build / Review Pipeline / Fix+Verify / Escalation / Interview-Mode
- 🛡️ **生产级防御**：Circuit Breaker + Stale Detection + Unstable Agent 处理
- 🚪 **6 道质量门禁**：Intent → Pre-Plan → Plan → Handoff → Review → Ship
- 📊 **Slot-Based 并发**：按 provider/model 分组 FIFO 排队，最大 5/Key
- 🏗️ **3 层层级架构**：Orchestrators → Specialists → Executors

## 安装

```bash
# 复制到 WorkBuddy 用户技能目录
cp -r orchestra-fusion ~/.workbuddy/skills/
```

## 触发词

- "编排多个agent" / "并行执行" / "构建多智能体流水线"
- "全队出击" / "ultrawork"
- "orchestrate agents" / "multi-agent workflow"

## 进化历程

| 版本 | 日期 | 变化 |
|------|------|------|
| v1.0.0 | 2026-05-02 | 三源杂交初始版本 (ATO + DevFleet + dmux) |
| v1.1.0 | 2026-05-02 | OMO 基因杂交 (10个新基因) |
| v1.2.0 | 2026-05-02 | 自评优化 (Quick-Start + 决策树 + 速查卡) |
| v1.2.1 | 2026-05-02 | 去重精简 (-115行/-15%) |
| v1.2.2 | 2026-05-02 | 降密度 (prose→bullet, -26行) ← 回滚，评分降至70% |
| v1.3.0 | 2026-05-02 | 结构性增强 (+触发示例/输出格式/输入异常处理) |

## 许可

MIT
