---
name: orchestra-fusion
description: "多智能体编排融合方案。融合四个顶尖编排框架的基因：Agent Team Orchestration 的角色生命周期 + Claude DevFleet 的 DAG/auto_dispatch + dmux-workflows 的并行模式库 + oh-my-opencode 的 Slot并发/Intent Gate/熔断器。Use when 用户说\"编排多个agent\"\"并行执行\"\"构建多智能体流水线\"\"设计agent团队\"\"orchestrate agents\"\"multi-agent workflow\"\"agent pipeline\"\"全队出击\"\"ultrawork\"。"
description_zh: "多智能体编排融合方案 — ATO × DevFleet × dmux × OMO 四源基因杂交"
description_en: "Multi-agent orchestration fusion — hybrid of ATO, DevFleet, dmux, and OMO patterns"
version: 1.2.2
agent_created: true
allowed-tools: Read,Write,Edit,Bash,Glob,Grep,TaskCreate,TaskGet,TaskUpdate,TaskList,SendMessage
---

# 🎻 Orchestra Fusion — 多智能体编排融合方案

> **基因来源：** Agent Team Orchestration（角色+生命周期） × Claude DevFleet（DAG+auto_dispatch） × dmux-workflows（并行模式库） × **oh-my-opencode（Slot并发+Intent Gate+熔断器）**
>
> 核心理念：**Intent → Plan → Dispatch → Monitor → Report**，每一步都有质量门禁和人在回路。

---

## ⚡ Quick-Start — 30秒上手

```
用户说一句                     →   自动发生
─────────────────────────────────────────────────────
"用agent团队构建博客系统"       →   Intent→Plan→Dispatch→Monitor→Report
"全队出击，重构认证模块"        →   Pre-Planning → 多Builder并行 → Review Pipeline
"先调研再决定方案"              →   Interview-Mode → 访谈澄清 → 再Plan
"这个任务太大了，拆开并行做"    →   DAG构建 → auto_dispatch → Slot并发
```

**三条黄金法则：**
1. 你只需要描述**做什么**，编排策略自动选择
2. 每次计划展示后你会看到 **DAG 图 + 并发方案**，确认才执行
3. 执行中你会看到**实时看板**，异常自动熔断

**最简示例：**

```
👤 用户："构建一个 REST API + 前端 Dashboard"

🤖 Intent Gate："Implementation任务，中等复杂度，两个模块独立可并行"
🤖 Plan：
   Wave 1: Scout:API调研 + Scout:前端调研           ← 并行
   Wave 2: Builder:API实现 + Builder:前端实现       ← 并行
   Wave 3: Reviewer:API审查 + Reviewer:前端审查     ← 并行
   
👤 用户确认 → Dispatch → Monitor(看板) → Report → Done
```

> 以下为完整参考手册，首次使用只需看 Quick-Start 即可。

---

## 设计哲学

本方案提取四个顶尖编排框架的最强基因，有机杂交而非生硬拼接：

| 来源技能 | 贡献基因 | 在方案中的角色 |
|---------|---------|---------------|
| **Agent Team Orchestration** | 角色分工、任务生命周期、Handoff、强制审查 | 团队骨架 |
| **Claude DevFleet** | DAG依赖规划、auto_dispatch、工作树隔离、看板 | 执行引擎 |
| **dmux-workflows** | 6种并行模式、merge策略 | 并行策略库 |
| **oh-my-opencode** | Intent Gate、Slot并发+熔断、Pre-Planning、陈旧检测 | 防御+规划增强 |

> "这不是四个技能的拼接，而是提取各自最优秀的基因，杂交出一个新物种。" — Orchestra Fusion

四个源方案均可作为降级路径——当复杂度不需要时，退化到更适合的方案。

---

## 团队架构

借鉴 OMO 的三层层级设计，融合 ATO 的角色分工。

### 三层层级模型

```
Layer 1: Orchestrators（编排层）
  ├─ 🎯 Orchestrator（主编排者）— 路由、追踪、汇报
  ├─ 📋 Planner（战略规划者）— 访谈式消歧义、构建DAG
  ├─ ⚠️ Risk Analyst（风险分析师）— 识别隐藏风险
  └─ ✅ Plan Validator（计划验证者）— 审查计划结构完整性
           │ 协调
Layer 2: Specialists（专业层）
  ├─ 🔍 Scout（侦察兵）— 调研、搜索、收集信息
  ├─ 🔨 Builder（建造者）— 执行代码/文档/配置
  └─ 👀 Reviewer（审查者）— 独立质量验证
           │ 按需派生
Layer 3: Executors（执行层）
  └─ 🏃 Worker（执行分身）— 按任务域（Category）自动匹配最优模型
```

### 详细角色定义

| 层级 | 角色 | 职责 | 模型建议 | 数量 | 模式 |
|------|------|------|---------|------|------|
| L1 | **Orchestrator** | 路由、追踪状态、合并结果、用户汇报 | 高推理（主agent） | 1 | primary |
| L1 | **Planner** | 访谈式消歧义、构建完整DAG、生成6段式计划 | 高推理 | 1 | subagent |
| L1 | **Risk Analyst** | 审查计划识别隐藏风险（Pre-Planning） | 高推理 | 1 | subagent |
| L1 | **Plan Validator** | 验证计划结构完整性（Pre-Planning） | 高推理 | 1 | subagent |
| L2 | **Scout** | 调研、搜索代码库、收集外部信息 | 性价比/快速模型 | 1-N | subagent |
| L2 | **Builder** | 执行建造（代码、文档、配置） | 任务匹配模型 | 1-N | subagent |
| L2 | **Reviewer** | 独立质量验证、交叉审查 | 高推理 | 1-N | subagent |
| L3 | **Worker** | 按Category自动匹配最优模型执行 | Category特定 | 0-N | subagent |

### 工具限制（安全边界）— 来自 OMO 设计

| 角色 | 禁止工具 | 原因 |
|------|---------|------|
| **Risk Analyst** | Write, Edit, TaskCreate | 只读分析，不修改代码 |
| **Plan Validator** | Write, Edit, TaskCreate | 只验证不执行 |
| **Scout** | Write, Edit, TaskCreate | 只调研，不修改 |
| **Reviewer** | TaskCreate | 只审查，不派生新任务 |
| **Worker** | TaskCreate, SendMessage(broadcast) | 只执行分配任务，不自行扩编 |

**核心原则：** Orchestrator 不建造 | 每产出物必审 | Reviewer 不能审自己造的 | L1（除Orchestrator外）不互派生（防递归循环）

### 任务生命周期

```
[Inbox] ──→ [Assigned] ──→ [In_Progress] ──→ [Review] ──→ [Done]
                │                │                │            │
                └──→ [Blocked] ←─┘                └──→ [Failed]
```

**状态转换规则：**
- Inbox → Assigned：Orchestrator 分配（记录分配时间和负责人）
- Assigned → In_Progress：Agent 开始工作
- In_Progress → Review：Agent 完成 + 提交 Handoff
- Review → Done：审查通过
- Review → In_Progress：审查不通过，返回修改
- 任意状态 → Blocked：遇到外部阻塞
- Failed：合法终态，记录原因后结束

### Handoff 协议

每次任务交接必须包含：

```
1. What was done    — 改动/产出摘要
2. Artifact paths   — 精确的文件路径
3. How to verify    — 验证命令或验收标准
4. Known issues     — 已知不足或风险
5. Next action      — 接收方的明确下一步
```

**反例：** "搞定了，看文件吧"
**正例：** "在 `/artifacts/auth/` 完成认证模块。运行 `npm test auth` 验证。已知问题：限流未实现。下一步：审查者检查错误处理边界。"

---

## DAG 依赖规划

借鉴 DevFleet 的依赖图引擎，融合 ATO 的任务生命周期。

### 步骤1：分析请求，构建 DAG

对用户请求做依赖分析：

```
用户："构建 REST API + 前端 Dashboard + E2E 测试"
     │
     ▼
依赖分析：
  REST API ─────────────┐
                         ├──→ E2E 测试（依赖两者）
  Frontend Dashboard ────┘

独立任务（可并行）：REST API, Frontend Dashboard
依赖任务（等待）：E2E 测试
```

### 步骤2：分配角色 + 设置 auto_dispatch

```
DAG:
  Scout:API调研 ──→ Builder:API实现 ──┐
                                      ├──→ Reviewer:E2E测试 ──→ Done
  Scout:前端调研 ──→ Builder:前端实现 ─┘

auto_dispatch 设置：
  - Scout 任务：立即派发（无依赖）
  - Builder 任务：依赖 Scout 完成 → auto_dispatch=true
  - Reviewer 任务：依赖 Builder 完成 → auto_dispatch=true
```

### 步骤3：展示计划，等用户确认

展示 DAG（节点/角色/输入输出/依赖/并发数），**用户确认后才派发。**

---

## Intent Gate — 意图门禁

借鉴 OMO 的 Intent Gate 设计：在分类和派发之前，Orchestrator **必须先 verbalize 路由判断**。

```
用户请求："构建一个高性能的博客系统，需要支持百万级并发"
              │
              ▼
         Intent Gate（强制步骤）
              │
   "这是一个 Implementation 任务，规模较大。
    需要先做架构调研（Scout），然后并行构建后端和前端。
    风险点：百万级并发可能需要特殊架构选型。
    建议：先启动 Risk Analyst 做预检查。"
              │
              ▼
         分类路由 → Plan 阶段
```

**Intent Gate 规则：**
1. 必须 verbalize：任务类型（Research / Implementation / Mixed）
2. 必须识别：复杂度（Simple / Medium / Complex / Critical）
3. 必须预警：潜在风险点
4. 必须建议：是否需要 Pre-Planning 阶段

---

## 并行模式库

借鉴 dmux 的 5 种并行模式 + OMO 的访谈式规划。

### Pattern 0: Interview-Mode Planning（访谈式规划）— 来自 OMO

在构建 DAG 之前，当用户请求模糊或复杂时，启动 Planner 进行结构化访谈：

```
Planner 访谈流程：
  1. 要求澄清 → "你提到的'高性能'具体指QPS还是延迟？"
  2. 约束确认 → "技术栈有限制吗？数据库偏好？"
  3. 范围界定 → "MVP 包含哪些功能？哪些可以后续？"
  4. 成功标准 → "怎么判断这个任务完成了？"
  5. 输出 6段式计划 →
     [1] 上下文收集（Scout+Librarian的调研结果）
     [2] 需求澄清摘要
     [3] 任务依赖图（阻塞点和关键路径）
     [4] 并行执行波次（Wave 1/2/3）
     [5] 每个子任务的Category + 推荐Skills
     [6] 响应格式规范
```

**触发条件：** 用户请求包含模糊词（"好的""高性能""优化一下"）且无明确技术约束时。
**必须征得用户同意后才进入正式Plan阶段。**

以下 5 种并行模式借鉴 dmux，适配到本方案的角色模型：

### Pattern 1: Research + Build 流水线

```
Scout:调研A ──→ Builder:构建A    }
Scout:调研B ──→ Builder:构建B    } 全部并行
Scout:调研C ──→ Builder:构建C    }

适用：调研和构建有明确因果关系，但不同模块独立
示例：调研三个API方案，各自实现POC
```

### Pattern 2: Multi-Build 并行

```
Builder:模块A    }
Builder:模块B    } 全部并行，互不依赖
Builder:模块C    }

适用：任务间零依赖，文件不重叠
示例：同时构建认证模块、支付模块、通知模块
```

### Pattern 3: Review Pipeline 审查流水线

```
Reviewer:结构审查  }
Reviewer:效果审查  } 全部并行，角度不同
Reviewer:安全审查  }

适用：同一产出物需要多角度验证
示例：代码同时接受结构审查、性能审查、安全审查
```

### Pattern 4: Fix + Verify 循环

```
Builder:修复 ──→ Reviewer:验证
    ↑                │
    └── 不通过 ──────┘    (最多3轮)

适用：需要反复打磨的产出物
示例：论文定稿的反复修改
```

### Pattern 5: Escalation 升级

```
Builder:尝试方案A ──→ 失败 ──→ Orchestrator 判断
                                    │
                          ┌─────────┼─────────┐
                          ▼         ▼         ▼
                     Builder:   Builder:   Scout:
                     方案B      方案C      重新调研

适用：遇到阻塞需要人工判断方向
Orchestrator 必须介入做决策
```

---

## 完整工作流

### Plan 阶段

```
1. 接收用户请求
2. 🚪 Intent Gate — verbalize 路由判断（强制步骤）
3. 判断复杂度：
   - Complex/Critical → 启动 Pre-Planning（Risk Analyst + Plan Validator）
   - Simple/Medium → 跳过 Pre-Planning
4. 分析任务依赖 → 构建 DAG
5. 为每个节点分配角色和 Category
6. 设置 auto_dispatch 链
7. 展示计划（6段式：调研摘要 + 需求澄清 + 依赖图 + 并行波次 + Category推荐 + 格式规范）
8. 🚪 Plan Gate — 用户确认

产出：Task 列表（TaskCreate），每个 task 标记角色、Category 和依赖
```

### Dispatch 阶段

```
1. 将 Plan 阶段的每个节点转化为 TaskCreate
2. 计算初始可派发任务（depends_on 为空的节点）
3. 判断并发模式 → 选择对应的并行模式（Pattern 1-5）
4. 一次性派发所有可并行任务
5. 设置 auto_dispatch：依赖满足时自动触发
6. Orchestrator 进入 Monitor 状态
```

### Monitor 阶段

```
1. 周期性检查 TaskList（间隔 30-60s）
2. 监控指标：
   - 每个任务的 status（pending/progress/review/blocked）
   - Slot-Based 并发槽位使用率
   - 排队任务数
3. 触发事件：
   - 任务完成 → 检查 auto_dispatch，派发后续
   - 任务阻塞 → 5分钟内无进展则升级到 Orchestrator
   - 审查不通过 → 返回 Builder，触发 Fix+Verify 循环
   - Stale Detection: 45min无活动→中断 | 60min无更新→取消
   - Circuit Breaker: 连续20次重复调用→告警 | 4000次总调用→强制取消
4. 向用户汇报进展（Dashboard 形式）

关键：Orchestrator 在 Monitor 阶段不执行建造工作
```

**常见异常处理：**

| 异常 | 处理方式 |
|------|---------|
| Agent 沉默无进展 | 5min ping → 无响应标记 Blocked；45min无活动→中断；60min→取消 |
| Builder 产出物空洞 | Reviewer 拒绝 → 详细反馈 → In_Progress |
| 审查连续 3 次不通过 | 升级为 Escalation → Orchestrator 介入决策 |
| git merge 冲突 | 人工解决或选择保留版本 |
| 并发超出上限 | 自动 FIFO 排队 |
| 用户中断 | 保存当前状态，已完成任务保留 |
```

### Report 阶段

```
所有任务到达 Done/Failed 后：

1. 汇总产出：
   - 成功任务数 / 总任务数
   - 产出物清单（文件路径）
   - 已知问题列表

2. 展示变更：
   - 代码 diff 摘要
   - 新增/修改文件列表
   - 测试结果

3. 决策建议：
   - 是否可交付？还是需要下一轮？
   - 哪些失败需要人工介入？

4. 用户最终确认 → Ship 或 Rework
```

---

## 并发控制

借鉴 OMO 的 **Slot-Based 并发管理器 + Circuit Breaker 熔断器**，替换 v1.0 的简单自适应策略。

### Slot-Based 并发模型

```
每个 provider/model 组合 = 一个并发Key
每个 Key 最多 5 个并发槽位
任务按 Key 分组排队（FIFO）

示例：
  Key: anthropic/claude-opus    → 槽位: 3/5 used
  Key: openai/gpt-5.4          → 槽位: 2/5 used
  Key: grok-code-fast-1        → 槽位: 1/5 used

总并发上限 = Σ(各Key槽位) = 6/15 active
```

**并发Key分组规则：**

| Key 格式 | 示例 | 何时使用 |
|----------|------|---------|
| `{provider}/{model}` | `anthropic/claude-opus-4-7` | 明确模型时 |
| `{agent_role}` | `scout`, `builder`, `reviewer` | 降级（模型未指定时） |

**关键机制：** 同 Key 排队（FIFO），不同 Key 真并行；获取槽位后执行，完成释放；排队任务 Promise waiter 自动唤醒。

### Circuit Breaker 熔断器 — 来自 OMO

防止单个失控Agent耗尽资源：

| 熔断条件 | 阈值 | 行为 |
|---------|------|------|
| **重复工具调用** | 连续20次相同参数调用 | ⚠️ 标记告警 |
| **绝对调用上限** | 累计4000次工具调用 | 🛑 强制取消 |
| **审查循环** | 同一任务Fix+Verify超过3轮 | 🛑 升级为Escalation |

### Stale Detection 陈旧检测 — 来自 OMO

| 条件 | 阈值 | 行为 |
|------|------|------|
| **无活动** | 45分钟无任何工具调用 | ⏸️ 中断（interrupt） |
| **无进展** | 60分钟无状态更新 | 🛑 取消（cancelled） |
| **Agent沉默** | 5分钟无回复 | 🔔 Orchestrator发ping → 仍无响应则Blocked |

### Unstable Agent 处理 — 来自 OMO

- 不稳定模型（频繁断连）→ 自动升格为**监控后台模式**
- 任务不因父 session 中断而丢失
- 通过 `task_id` 支持跨 session 续接

### 工作树隔离

当任务触及重叠文件时，使用 `EnterWorktree` 隔离：

```
git worktree add -b feat/module-A ../module-A HEAD
git worktree add -b feat/module-B ../module-B HEAD

# Agent A → cd ../module-A
# Agent B → cd ../module-B
# 完成后 merge
```

### Category Dispatch — 按任务域匹配模型（来自 OMO）

| 任务域 | 推荐模型 | 适用场景 |
|--------|---------|---------|
| `visual-engineering` | Gemini 3.1 Pro | UI/UX、CSS、设计系统 |
| `ultrabrain` | GPT-5.4 xhigh / Claude Opus max | 重逻辑、架构设计 |
| `deep-work` | GPT-5.4 medium / Claude Sonnet | 目标导向自主工作 |
| `fast-search` | Grok-Code-Fast / MiniMax | 快速代码搜索 |
| `general` | 用户当前选择 | 通用任务 |

Orchestrator 在 Dispatch 阶段根据任务特征自动选择 Category，Worker 使用 Category 匹配的最优模型执行。

---

## 质量门禁

每个关键阶段都有质量门禁，防止漂移。

| 门禁 | 位置 | 检查内容 | 来源 |
|------|------|---------|------|
| 🚪 Intent Gate | 请求→Plan | 强制verbalize路由判断（类型/复杂度/风险） | OMO |
| 🚪 Pre-Plan Gate | Plan前 | Risk Analyst + Plan Validator 双审查（Complex任务） | OMO |
| 🚪 Plan Gate | Plan→Dispatch | 用户确认6段式计划 | DevFleet |
| 🚪 Handoff Gate | Builder→Reviewer | Handoff 5要素是否齐全 | ATO |
| 🚪 Review Gate | Review→Done | 至少1个独立Reviewer通过 | ATO |
| 🚪 Ship Gate | Report→Done | 用户最终确认 | 独创 |

**审查标准 + 不通过流程：** ✅ 产出符合描述？测试/验证？边界覆盖？Handoff 完整？ → 不通过则 Reviewer 指出问题 → In_Progress → Builder 修复 → 重新审查（最多3轮，第4轮升级 Escalation）

---

## 看板与监控

实时进度展示：

```
═══ Orchestra Fusion Dashboard ═══
Phase: Execute  |  Uptime: 12m  |  Tasks: 5/8 done

┌──────────────┬──────────┬──────────┬─────────────┐
│ Task         │ Role     │ Status   │ Depends On  │
├──────────────┼──────────┼──────────┼─────────────┤
│ 🔍 API调研   │ Scout-1  │ ✅ Done  │ -           │
│ 🔍 前端调研  │ Scout-2  │ ✅ Done  │ -           │
│ 🔨 API实现   │ Builder-1│ ✅ Done  │ API调研     │
│ 🔨 前端实现  │ Builder-2│ 🔄 InPrg │ 前端调研    │
│ 🔨 数据库    │ Builder-3│ ✅ Done  │ -           │
│ 👀 API审查   │ Review-1 │ ⏳ Review│ API实现     │
│ 👀 前端审查  │ Review-2 │ ⏸ Wait  │ 前端实现    │
│ 👀 E2E测试   │ Review-3 │ ⏸ Wait  │ API+前端    │
└──────────────┴──────────┴──────────┴─────────────┘

Concurrency: 3/5 active | Next: Review-2 (waiting on Builder-2)
Alerts: None
═══ ═══ ═══ ═══ ═══ ═══ ═══ ═══ ═══ ═══
```

使用 `TaskList` 获取实时任务状态，定期刷新看板。

---

## 触发词

中英文触发：
- "编排多个agent" / "并行执行" / "构建多智能体流水线"
- "设计agent团队" / "多agent协作" / "agent分工"
- "orchestrate agents" / "multi-agent workflow"
- "agent pipeline" / "parallel agents"
- "全队出击" / "ultrawork" / "所有agent一起上"
- "启动熔断" / "slot并发" / "意图门禁" / "interview plan"

---

## 约束规则

1. **Orchestrator 不建造** — 只路由追踪，不亲自执行
2. **Intent Gate 强制** — 分类前必须 verbalize（类型/复杂度/风险）
3. **Plan 用户确认** — 不确认不派发；Complex 任务需 Pre-Planning 双审查
4. **每产出物必审** — 跳过审查 3 次 = 质量漂移；Reviewer 不能审自己造的
5. **Agent 沉默即卡住** — 5min ping → Blocked；45min 无活动 → 中断；60min → 取消
6. **熔断生效** — 20 次重复→告警；4000 次→取消；Fix+Verify 超 3 轮→升级
7. **L1 不互派生** — Planner/Risk/Validator 不能派生其他 L1 角色（防递归循环）
8. **Tool Restriction** — Risk Analyst / Plan Validator / Scout 禁止 Write 操作
9. **Ship Gate 最终确认** — 用户不过不交付

---

## 使用方式

### 🧭 场景决策树

```
你的任务适合什么模式？
         │
         ▼
  ┌─ 任务简单，单一Agent够用？ ──→ ✅ 直接执行（不启用编排）
  │
  ├─ 需要调研+建造分开？ ──→ ✅ Pattern 1: Research+Build 流水线
  │
  ├─ 多个独立模块并行建造？ ──→ ✅ Pattern 2: Multi-Build 并行
  │
  ├─ 产出物需要多角度验证？ ──→ ✅ Pattern 3: Review Pipeline
  │
  ├─ 需要反复打磨？ ──→ ✅ Pattern 4: Fix+Verify 循环
  │
  ├─ 遇到阻塞需要决策？ ──→ ✅ Pattern 5: Escalation 升级
  │
  ├─ 需求模糊需要澄清？ ──→ ✅ Pattern 0: Interview-Mode 访谈
  │
  └─ 全面大规模任务？ ──→ ✅ 全流程：Intent→Pre-Plan→Plan→Dispatch→Monitor→Report
```

**复杂度自动路由：**

| 用户请求特征 | 自动触发 | 示例关键词 |
|-------------|---------|-----------|
| 单一明确操作 | 直接执行 | "改个bug""加个注释" |
| 多模块但独立 | DAG + Multi-Build | "同时构建A和B" |
| 大规模/Critical | Pre-Planning双审查 | "百万并发""生产环境""重构" |
| 模糊/开放式 | Interview-Mode | "优化一下""做得更好""帮我设计" |

### 基础用法
```
用户："用agent团队构建一个博客系统"
→ 自动进入 Intent→Plan→Dispatch→Monitor→Report 流程
```

### 指定角色
```
用户："派 2 个 Scout 调研数据库方案，然后 3 个 Builder 实现"
→ 按照指定数量分配角色 + Category自动匹配模型
```

### 全队出击（Ultrawork）
```
用户："全队出击，把这个项目从零搭建到上线"
→ 启用 L1 全角色 + L2 多Scout/Builder/Reviewer + Slot并发最大化
→ Pre-Planning → DAG → 所有独立Wave并行
```

### 仅评估不执行
```
用户："分析这个任务适合什么编排模式"
→ 只做 Intent Gate + Plan 阶段，展示 DAG 但不派发
```

### 降级到简单模式
```
用户："只用一个agent做这个"
→ 退化到单Agent执行，不需要团队编排
```

### 📋 速查卡片

| 想要什么 | 说这句话 |
|---------|---------|
| 大规模多Agent编排 | "用 agent 团队构建 X" |
| 全并行最大火力 | "全队出击" / "ultrawork" |
| 模糊需求先访谈 | "先聊聊方案再决定" |
| 只分析不执行 | "分析任务适合什么模式" |
| 看当前进度 | "看板" / "dashboard" |
| 中断恢复 | "继续上次的任务" |

---

## 与源方案的关系

本方案不是替代，而是互补：

| 场景 | 推荐方案 |
|------|---------|
| 简单任务（单Agent） | 直接执行 |
| 需要明确角色分工 | Agent Team Orchestration |
| 已有 DevFleet MCP 服务 | Claude DevFleet |
| 喜欢 tmux 终端操作 | dmux-workflows |
| 已有 OpenCode + OMO 环境 | oh-my-opencode |
| **大规模多Agent编排** | **Orchestra Fusion** ← 本方案 |

