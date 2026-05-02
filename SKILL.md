---
name: orchestra-fusion
description: "多智能体编排融合方案。融合四个顶尖编排框架的基因：Agent Team Orchestration 的角色生命周期 + Claude DevFleet 的 DAG/auto_dispatch + dmux-workflows 的并行模式库 + oh-my-opencode 的 Slot并发/Intent Gate/熔断器。Use when 用户说\"编排多个agent\"\"并行执行\"\"构建多智能体流水线\"\"设计agent团队\"\"orchestrate agents\"\"multi-agent workflow\"\"agent pipeline\"\"全队出击\"\"ultrawork\"。"
description_zh: "多智能体编排融合方案 — ATO × DevFleet × dmux × OMO 四源基因杂交"
description_en: "Multi-agent orchestration fusion — hybrid of ATO, DevFleet, dmux, and OMO patterns"
version: 1.4.0
agent_created: true
allowed-tools: Read,Write,Edit,Bash,Glob,Grep,TaskCreate,TaskGet,TaskUpdate,TaskList,SendMessage
---

# 🎻 Orchestra Fusion — 多智能体编排融合方案

> ATO（角色+生命周期）× DevFleet（DAG+auto_dispatch）× dmux（并行模式）× OMO（Slot并发+熔断）
>
> 核心理念：**Intent → Plan → Dispatch → Monitor → Report**，每步有质量门禁和人在回路。

---

## ⚡ Quick-Start

| 你说 | 自动发生 |
|------|---------|
| "用agent团队构建博客系统" | Intent→Plan→Dispatch→Monitor→Report |
| "全队出击，重构认证模块" | Pre-Planning→多Builder并行→Review Pipeline |
| "先调研再决定方案" | Interview-Mode→访谈澄清→再Plan |
| "这个任务太大了，拆开并行做" | DAG构建→auto_dispatch→Slot并发 |

**三条黄金法则：**
1. 你只需描述**做什么**，编排策略自动选
2. 每次展示 DAG + 并发方案，确认才执行
3. 执行中看实时看板，异常自动熔断

**最简示例：**
```
👤 "构建一个 REST API + 前端 Dashboard"
🤖 IntentGate: "Implementation, 中等复杂度, 两模块独立可并行"
🤖 Plan: Wave1 Scout×2 → Wave2 Builder×2 → Wave3 Reviewer×2
👤 确认 → Dispatch → Monitor → Done
```

---

## 团队架构

三层层级：

```
L1 编排层: Orchestrator / Planner / Risk Analyst / Plan Validator
L2 专业层: Scout(调研) / Builder(建造) / Reviewer(审查)
L3 执行层: Worker(按Category自动匹配最优模型)
```

| 角色 | 职责 | 工具限制 |
|------|------|---------|
| **Orchestrator** | 路由、追踪、合并、汇报 | 全部（不建造） |
| **Planner** | 访谈消歧义、构建DAG | 无 Write |
| **Risk Analyst** | 识别隐藏风险 | 无 Write/Edit/TaskCreate |
| **Plan Validator** | 验证计划完整性 | 无 Write/Edit/TaskCreate |
| **Scout** | 调研搜索 | 无 Write/Edit/TaskCreate |
| **Builder** | 执行建造 | 全工具（独立分支） |
| **Reviewer** | 独立质量验证 | 无 TaskCreate |
| **Worker** | Category匹配执行 | 无 TaskCreate/Broadcast |

**核心原则：** Orchestrator 不建造 | 每产出物必审 | Reviewer 不审自造 | L1 不互派生（防递归）

**任务生命周期：** Inbox → Assigned → In_Progress → Review → Done/Failed（Blocked 可从任意状态触发）

**Handoff 5要素：** (1)改动摘要 (2)文件路径 (3)验证方式 (4)已知问题 (5)下一步行动

---

## Intent Gate — 意图门禁

分类前强制 verbalize：**任务类型**（Research/Implementation/Mixed）、**复杂度**（Simple/Medium/Complex/Critical）、**风险点**、**是否需要 Pre-Planning**。

---

## DAG 依赖规划

1. 分析依赖 → 独立任务并行，依赖任务排队
2. 分配角色 + auto_dispatch（依赖满足自动触发）
3. 展示 DAG（节点/角色/输入输出/并发数）→ **用户确认后派发**

---

## 并行模式

| # | 模式 | 结构 | 适用场景 |
|---|------|------|---------|
| P0 | **Interview-Mode** | Planner 5步访谈→6段式计划 | 需求模糊（"优化一下""高性能"） |
| P1 | **Research+Build** | Scout×3→Builder×3 并行 | 调研后独立建造 |
| P2 | **Multi-Build** | Builder×N 全并行 | 零依赖多模块 |
| P3 | **Review Pipeline** | Reviewer×N 多角度并行 | 结构/效果/安全审查 |
| P4 | **Fix+Verify** | Builder⇄Reviewer（max3轮） | 反复打磨场景 |
| P5 | **Escalation** | 失败→Orchestrator决策→B/C/重新调研 | 阻塞需人工判断 |

---

## 完整工作流

### Plan → Dispatch → Monitor → Report

- **Plan:** Intent Gate → 判断复杂度 → DAG → 角色/Category分配 → 6段式展示 → 用户确认
- **Dispatch:** TaskCreate 转化 → 派发无依赖任务 → auto_dispatch 链 → 进入 Monitor
- **Monitor:** 30-60s 轮询 TaskList → 完成则 dispatch 下一波 / 阻塞超5min升级 / 审查不通过→Fix+Verify / Stale:45min中断,60min取消 / Breaker:20次重复告警,4000次取消
- **Report:** 成功/失败汇总 + 产出物清单 + diff摘要 + 决策建议 → 用户确认

**异常处理：**

| 异常 | 处理 |
|------|------|
| Agent沉默 | 5min ping→Blocked; 45min→中断; 60min→取消 |
| 产出空洞 | Reviewer拒绝→详细反馈→In_Progress |
| 审查3次不通过 | 升级 Escalation |
| git merge冲突 | 人工解决或选保留版本 |
| 并发饱和 | FIFO排队+告警 |
| 用户中断 | 保存状态，已完成保留 |

---

## 并发控制

**Slot-Based:** 每个 provider/model 组合 = 一个 Key，每 Key 最多 5 槽位，FIFO 排队。同 Key 排队，不同 Key 真并行。

**Circuit Breaker:** 连续20次重复→告警; 4000次→取消; Fix+Verify>3轮→升级。

**Stale Detection:** 无活动45min→中断 | 无更新60min→取消 | 无回复5min→ping。

**Unstable Agent:** 断连模型自动升格后台模式，task_id 跨 session 续接。

**Category Dispatch:**

| 任务域 | 模型 | 场景 |
|--------|------|------|
| `visual-engineering` | Gemini 3.1 Pro | UI/UX/CSS |
| `ultrabrain` | GPT-5.4 / Claude Opus | 重逻辑/架构 |
| `deep-work` | GPT-5.4/Sonnet medium | 自主工作 |
| `fast-search` | Grok/MiniMax | 快速搜索 |
| `general` | 用户选择 | 通用 |

**工作树隔离：** 重叠文件 → `git worktree` 独立分支，完成后 merge。

---

## 质量门禁

| 门禁 | 位置 | 检查内容 | 来源 |
|------|------|---------|------|
| Intent Gate | 请求→Plan | verbalize 判断 | OMO |
| Pre-Plan Gate | Plan前 | Risk+Validator 双审 | OMO |
| Plan Gate | Plan→Dispatch | 用户确认 | DevFleet |
| Handoff Gate | Builder→Reviewer | 5要素齐全 | ATO |
| Review Gate | Review→Done | 独立 Reviewer 通过 | ATO |
| Ship Gate | Report→Done | 用户最终确认 | 独创 |

**审查标准：** 产出符合描述？有验证？边界覆盖？Handoff完整？  
**不通过流程：** Reviewer 指出问题→In_Progress→修复重交→重审（max3轮，超→Escalation）

---

## 约束规则

1. **Orchestrator 不建造** — 只路由追踪
2. **Intent Gate 强制** — 分类前 verbalize
3. **Plan 用户确认** — Complex 需 Pre-Planning 双审
4. **每产出物必审** — Reviewer 独立、不审自造
5. **Agent 沉默即卡住** — 5min→ping; 45min→中断; 60min→取消
6. **熔断生效** — 20次重复/4000次/3轮 Fix+Verify
7. **L1 不互派生** — 防递归规划循环
8. **Tool Restriction** — Risk/Validator/Scout 禁 Write
9. **Ship Gate 确认** — 用户不过不交付

---

## 触发场景

| 用户输入 | 流程 | 模式 |
|---------|------|------|
| "用agent团队重构认证模块" | Intent→DAG→Dispatch→Monitor | P1+P2 |
| "先调研微服务方案再决定" | Interview→访谈→Plan→Dispatch | P0 |
| "全队出击，项目上线" | Pre-Planning→全L1→Multi-Build→Review | 全流程 |
| "改个登录页标题" | 直接执行（不启用编排） | 降级 |
| "评估任务复杂度" | Intent Gate→Plan→展示DAG | 仅评估 |
| "上次任务继续" | task_id续接→Continue | 恢复 |
| "同时写A/B/C单元测试" | DAG→Multi-Build→Slot 3/5 | P2 |
| "看进度" | Dashboard | 看板 |

**复杂度自动路由：** 单一操作→直接执行 | 多模块→DAG+Multi-Build | Critical→Pre-Planning | 模糊→Interview-Mode

---

## 输入异常处理

| 异常 | 处理 |
|------|------|
| 空输入/无请求 | 不触发编排 |
| 乱码/非文本 | Intent Gate 捕捉→要求重述 |
| 超出范围 | 识别不可执行→建议缩小或拒绝 |
| 超大请求 | 自动拆分→触发 P0 访谈 |
| 引用不存在文件 | Scout 检测 404→返回路径→修正 |
| 循环依赖 | DAG 校验拒绝→要求分解 |
| 并发饱和 | FIFO 排队→告警"系统繁忙" |

---

## 与源方案的关系

| 场景 | 推荐 |
|------|------|
| 简单任务 | 直接执行 |
| 明确角色分工 | Agent Team Orchestration |
| DevFleet MCP | Claude DevFleet |
| tmux 终端 | dmux-workflows |
| OpenCode+OMO | oh-my-opencode |
| **大规模编排** | **Orchestra Fusion** |

---

## 基因来源

| 源技能 | 贡献 |
|--------|------|
| Agent Team Orchestration | 角色骨架、生命周期、Handoff、强制审查 |
| Claude DevFleet | DAG 引擎、auto_dispatch、工作树、看板 |
| dmux-workflows | 6 种并行模式、merge 策略 |
| oh-my-opencode | Intent Gate、Slot 并发、熔断、陈旧检测 |
| darwin-skill | 诞生 + 进化方法论 |

> "这不是四个技能的拼接，而是提取各自最优秀的基因，杂交出一个新物种。"
