# 🎻 Orchestra Fusion

> 多智能体编排融合方案 — ATO × DevFleet × dmux × OMO 四源基因杂交

[![version](https://img.shields.io/badge/version-1.5.4-blue)](SKILL.md)
[![license](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![quality](https://img.shields.io/badge/quality-79%25_Good-brightgreen)](https://github.com/Liber1917/orchestra-fusion)
[![platform](https://img.shields.io/badge/platform-WorkBuddy|ClaudeCode|OpenCode-orange)](SKILL.md)

## 一句话

你说"用 agent 团队构建 X"，Orchestra Fusion 自动完成 **Intent → Plan → Dispatch → Monitor → Report** 全流程多智能体编排。

## 基因来源

| 源技能 | 贡献基因 | 置信度 |
|--------|---------|--------|
| **Agent Team Orchestration** | 角色分工、任务生命周期、Handoff 协议、强制审查 | 源码核实 |
| **Claude DevFleet** | DAG 依赖规划、auto_dispatch、工作树隔离、看板 | 源码核实 |
| **dmux-workflows** | 5 种并行模式、tmux pane 管理 | 源码核实 |
| **oh-my-opencode** | Slot-Based 并发(5槽位)、熔断器(20/4000)、Intent Gate、陈旧检测(45/60min) | 源码核实 |

> 所有声明均经源文件核实，详见 `references/source-evidence/`。

## 30 秒上手

```
👤 "用agent团队构建博客系统"
🤖 Intent Gate → Plan(DAG+并发) → 用户确认 → Dispatch → Monitor(看板) → Done

👤 "改个变量名"
🤖 Intent Gate: Simple → 降级，直接执行
```

## 核心能力

- 🎯 **6 种编排模式**：Research+Build / Multi-Build / Review Pipeline / Fix+Verify / Escalation / Interview-Mode
- 🛡️ **生产级防御**：Circuit Breaker (20次重复/4000次上限) + Stale Detection (45/60min) — OMO 源码确认
- 🚪 **6 道质量门禁**：Intent → Pre-Plan → Plan → Handoff → Review → Ship
- 📊 **Slot-Based 并发**：按 provider/model 分组 FIFO，最大 5/Key — OMO `ConcurrencyManager` 证实
- 🏗️ **3 层层级架构**：Orchestrators → Specialists → Executors
- 🌐 **三平台适配**：WorkBuddy (原生) / Claude Code (降级策略) / OpenCode (降级策略)
- 📋 **证据溯源**：24 条声明按 ✅/⚠️/❌/🆕 四级置信度标注

## 质量评估

| 工具 | 评分 | 日期 |
|------|------|------|
| darwin-skill (自评) | 80.5% | 2026-05-02 |
| agent-skill-infra (外部) | 79% (Good) | 2026-05-02 |

> 自评→外部评 12 分偏差教训：自评有认知偏见，外部工具更诚实。

## 安装

```bash
cp -r orchestra-fusion ~/.workbuddy/skills/
```

## 触发词

- 强: "编排多个agent" / "用agent团队" / "全队出击" / "并行执行"
- 中: "agent分工" / "先调研再实现" / "分头执行"
- 英: "orchestrate agents" / "multi-agent workflow" / "agent pipeline"

## 进化历程

| 版本 | 日期 | 关键变化 |
|------|------|---------|
| v1.0.0 | 2026-05-02 | 三源杂交初始版本 |
| v1.1.0 | 2026-05-02 | OMO 基因杂交 |
| v1.3.0 | 2026-05-02 | 结构增强（触发示例/输出格式/异常处理） |
| v1.4.0 | 2026-05-02 | **证据溯源 + 平台适配**（可信度修补） |
| v1.4.1 | 2026-05-02 | OMO 源码核查（5槽位/20/4000/45/60min 全通过） |
| v1.5.0 | 2026-05-02 | **agent-skill-infra 驱动**：约束规则分类/错误恢复示例/输出格式示例 |
| v1.5.4 | 2026-05-02 | 管线对齐锚点 + 最终去冗余 |

## 许可

MIT
