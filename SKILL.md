---
name: orchestra-fusion
description: "多智能体编排融合方案。融合四个顶尖编排框架的基因：Agent Team Orchestration 的角色生命周期 + Claude DevFleet 的 DAG/auto_dispatch + dmux-workflows 的并行模式库 + oh-my-opencode 的 Slot并发/Intent Gate/熔断器。Use when 用户说\"编排多个agent\"\"并行执行\"\"构建多智能体流水线\"\"设计agent团队\"\"orchestrate agents\"\"multi-agent workflow\"\"agent pipeline\"\"全队出击\"\"ultrawork\"。"
description_zh: "多智能体编排融合方案 — ATO × DevFleet × dmux × OMO 四源基因杂交"
description_en: "Multi-agent orchestration fusion — hybrid of ATO, DevFleet, dmux, and OMO patterns"
version: 1.4.5
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
| **dmux-workflows** | 5种并行模式、tmux pane管理、git worktree集成 | 并行策略库 |
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

### 工具限制（安全边界）— 设计提案

> ⚠️ **设计提案，非 OMO 原文。** 以下工具限制矩阵为本 skill 的设计建议。
> OMO 源码中**未找到**角色级工具白名单/黑名单机制。
> 实际执行时，以运行平台的能力为准。

| 角色 | 建议限制 | 原因 |
|------|---------|------|
| **Risk Analyst** | 只读（不 Write/Edit/Create） | 只读分析，不修改代码 |
| **Plan Validator** | 只读（不 Write/Edit/Create） | 只验证不执行 |
| **Scout** | 只读（不 Write/Edit/Create） | 只调研，不修改 |
| **Reviewer** | 不派生新任务 | 只审查，不自行扩编 |
| **Worker** | 不派生新任务/不广播 | 只执行分配任务

**核心原则：**
- Orchestrator 不执行建造工作
- 每个产出物至少经独立眼睛审查
- Reviewer 不能审查自己建造的产出物
- L1 层（除 Orchestrator）不互派生（防递归规划循环）

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

**DAG 输出格式要求：** 计划展示中的「依赖图」必须以可视化形式呈现：

```
✅ 推荐 — 箭头图（ASCII）:
  Scout:调研 ──→ Builder:实现 ──→ Reviewer:审查 ──→ Done

✅ 推荐 — 结构化列表:
  Wave 1: [Scout:调研] [Scout:前端]     ← 并行
  Wave 2: [Builder:实现] (依赖 Wave 1)   ← 等待
  Wave 3: [Reviewer:审查] (依赖 Wave 2)

❌ 不推荐 — 纯文字描述:
  "先调研再实现再审查"
```

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

### 各阶段输出格式

| 阶段 | 输出内容 | 格式 |
|------|---------|------|
| Intent Gate | 任务类型 + 复杂度 + 风险点 + 建议 | 4行 verbalize |
| Pre-Planning | Risk 列表 + 结构检查结果 | 两个独立 report |
| Plan | 6段式计划文档 | Markdown 结构 |
| Dispatch | Task 列表（每个含 role/category/depends_on）| TaskCreate JSON |
| Monitor | Dashboard 看板 + 异常告警 | 表格 + 状态标记 |
| Report | 成功/失败汇总 + 产出物清单 + 决策建议 | Markdown summary |

---

## 输入异常处理

| 异常输入 | 处理方式 |
|---------|---------|
| **空输入 / 无请求** | 不触发编排，正常等待 |
| **格式错误**（乱码/非文本） | Intent Gate 捕捉 → 要求用户重新表述 |
| **超出范围**（如"帮我发射火箭"） | Intent Gate 识别不可执行 → 建议缩小范围或拒绝 |
| **Token 超出**（超大请求） | 自动拆分为子任务 → 触发 Pattern 0 访谈澄清 |
| **引用不存在的文件/模块** | Scout 阶段检测 404 → 返回错误路径 → 要求修正 |
| **循环依赖**（A依赖B，B依赖A） | Plan 阶段 DAG 校验 → 拒绝 circular dependency → 要求用户分解 |
| **并发饱和**（所有 Key 全满） | 自动 FIFO 排队 → 告警用户"系统繁忙，任务已排队" |

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

> ⚠️ **已验证：** Category 名称来自 OMO 源码确认 (`visual-engineering`, `ultrabrain`, `deep`, `quick`, `artistry`, `writing`)。
> 但模型名称 **不在 Category 定义中**——OMO 使用独立的 4-step 模型决议系统。
> 下表中的模型列为本 skill 的建议匹配，非 OMO 原文。

| 任务域（OMO 原名） | 建议模型（本 skill 推测） | 适用场景 |
|--------|---------|---------|
| `visual-engineering` | Gemini 3.1 Pro 等视觉模型 | UI/UX、CSS、设计系统 |
| `ultrabrain` | GPT-5.4 xhigh / Claude Opus max | 重逻辑、架构设计 |
| `deep` | GPT-5.4 medium / Claude Sonnet | 目标导向自主工作 |
| `quick` | Grok-Code-Fast / MiniMax | 快速代码搜索 |
| `writing` | Claude Sonnet / GPT-5.4 | 文档、写作 |
| `artistry` | 创意类模型 | 创意设计、视觉艺术 |
| `general` | 用户当前选择 | 通用任务 |

Orchestrator 在 Dispatch 阶段根据任务特征自动选择 Category。

**深度感知规则：** 同一角色在不同深度的任务中使用不同 Category：

| 角色 | 浅层任务 | Category | 深层任务 | Category |
|------|---------|----------|---------|----------|
| Scout | 搜索已知信息、API文档查询 | `quick` | 代码库深度分析、架构调研 | `deep` |
| Builder | 简单CRUD、配置修改 | `quick` | 核心算法实现、架构重构 | `ultrabrain` |
| Reviewer | 格式检查、lint验证 | `quick` | 架构级审查、安全审计 | `ultrabrain` |

> 判断原则：**涉及跨文件理解、需要上下文推理的任务用 `deep` 或 `ultrabrain`；单文件、确定性任务用 `quick`。**

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

**审查标准：**
- ✅ 产出是否符合任务描述？
- ✅ 是否有测试/验证方法？
- ✅ 是否有边界条件遗漏？
- ✅ Handoff 文档是否完整？

**审查不通过流程：** Reviewer 写具体问题 → Orchestrator 返回 In_Progress → Builder 修复重交 → 重新审查（最多3轮，第4轮升级 Escalation）

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

### 触发场景示例

| 用户输入 | 自动触发的流程 | 预计模式 |
|---------|-------------|---------|
| "帮我用 agent 团队重构认证模块" | Intent→Plan→DAG→Dispatch→Monitor | Pattern 1+2 |
| "先调研一下微服务方案再决定" | Interview-Mode→访谈→Plan→Dispatch | Pattern 0 |
| "全队出击，把这个项目上线" | Pre-Planning→全L1→Multi-Build→Review Pipeline | 全流程 |
| "改个登录页的标题" | 直接执行（不启用编排） | 降级 |
| "评估这个任务的复杂度" | 只分析：Intent Gate→Plan→展示DAG | 仅评估 |
| "上次的任务继续" | task_id 续接→Monitor→Continue | 中断恢复 |
| "同时写 A、B、C模块的单元测试" | DAG→Multi-Build→Slot并发3/5 | Pattern 2 |
| "看下进度" | Dashboard 展示 | 看板 |
| "用 agent 团队构建 X" | 全流程编排 | 大规模 |

---

## 约束规则

1. **Orchestrator 不建造** — 只路由追踪，不亲自执行
2. **Intent Gate 强制** — 分类前必须 verbalize（类型/复杂度/风险）
3. **Plan 用户确认** — 不确认不派发；Complex 任务需 Pre-Planning 双审查
4. **每产出物必审** — 跳过审查 3 次 = 质量漂移；Reviewer 不能审自己造的
5. **Agent 沉默即卡住** — 5min ping → Blocked；45min 无活动 → 中断；60min → 取消
6. **熔断生效** — 20 次重复→告警；4000 次→取消；Fix+Verify 超 3 轮→升级
7. **L1 不互派生** — Planner/Risk/Validator 不能派生其他 L1 角色（防递归循环）
8. **Tool Restriction（设计提案）** — Risk Analyst / Plan Validator / Scout 建议只读；执行时以平台实际能力为准
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

---

## 证据溯源 — Evidence Trace

> **本 skill 中关于四个源方案的声明并非同等可信。**
> 以下按置信度分级标注，区分「原文提取的事实」和「模型推测的设计决策」。
> 未列出的声明（如具体罚值、角色名）视为「设计决策」而非「事实引用」。

### 置信度说明

| 标记 | 含义 | 准则 |
|------|------|------|
| ✅ **高** | 从源 SKILL.md 原文直接提取，可引用段落/行号 | 事实 |
| ⚠️ **中** | 概念在源文件中存在，但具体参数/名称/细节可能是填充的 | 半事实半推测 |
| ❌ **低** | 源文件中未找到对应内容，基于概念名或模型先验知识推测 | 推测 |
| 🆕 **独创** | 本方案的原创设计，不来自任何源方案 | 设计决策 |

### 声明溯源表

| 声明 | 声称来源 | 置信度 | 核查说明 |
|------|---------|--------|---------|
| 角色分工模型（Orchestrator/Scout/Builder/Reviewer） | ATO/🆕 | ⚠️ 中 | ATO 原文仅 4 角色（Orchestrator/Builder/Reviewer/Ops），Scout/Risk Analyst/Plan Validator 为 orchestra-fusion 扩展。详见 `references/source-evidence/ato-evidence.md §1` |
| 任务生命周期（Inbox→Assigned→In_Progress→Review→Done） | ATO | ✅ 高 | ATO 原文: `Inbox→Assigned→In Progress→Review→Done|Failed`。`[Blocked]` 为 orchestra-fusion 扩展。详见 `references/source-evidence/ato-evidence.md §2` |
| Handoff 5要素协议 | ATO | ✅ 高 | ATO 原文逐字对应。详见 `references/source-evidence/ato-evidence.md §3` |
| 强制审查（每产出物必审，Reviewer不能审自己的） | ATO | ✅ 高 | ATO: "every artifact gets at least one set of eyes that didn't produce it." 详见 `references/source-evidence/ato-evidence.md §4` |
| DAG 依赖规划 + auto_dispatch 链 | DevFleet | ✅ 高 | DevFleet MCP tool: `plan_project(prompt) → project_id + mission DAG`。详见 `references/source-evidence/devfleet-evidence.md §1` |
| 工作树隔离 (EnterWorktree) | DevFleet | ✅ 高 | DevFleet: "Each agent runs in an isolated git worktree." 详见 `references/source-evidence/devfleet-evidence.md §2` |
| 看板仪表盘 | DevFleet | ✅ 高 | DevFleet: `get_dashboard()` + `get_mission_status()`。详见 `references/source-evidence/devfleet-evidence.md §3` |
| 5种并行模式（Research+Build / Multi-Build / Review Pipeline / Fix+Verify / Escalation） | dmux/🆕 | ⚠️ 中 | dmux 原文有 5 种模式（Research+Implement/Multi-File/Test+Fix/Cross-Harness/Code Review Pipeline）。Pattern 5 (Escalation) 和 Pattern 0 为 orchestra-fusion 创新。详见 `references/source-evidence/dmux-evidence.md` |
| Interview-Mode 访谈式规划 (Pattern 0) | OMO | ✅ 高 | 5步访谈流程和6段式计划模板来自 OMO 原文 |
| 3-Tier 层级模型 (Orchestrator/Specialist/Executor) | OMO | ✅ 高 | 分层结构来自 OMO 原文 |
| Pre-Planning 双审查（Risk Analyst + Plan Validator） | OMO | ✅ 高 | 双审查机制在 OMO 原文中有描述 |
| Slot-Based 并发模型（每个 Key 最多 5 槽位） | OMO | ✅ 高 | **源码确认：** `ConcurrencyManager.getConcurrencyLimit()` 默认 `return 5`。详见 `references/source-evidence/omo-evidence.md §1` |
| Circuit Breaker 熔断器阈值（20次重复/4000次总调用） | OMO | ✅ 高 | **源码确认：** `DEFAULT_CIRCUIT_BREAKER_CONSECUTIVE_THRESHOLD = 20`，`DEFAULT_MAX_TOOL_CALLS = 4000`。详见 `references/source-evidence/omo-evidence.md §2` |
| Stale Detection 陈旧检测阈值（45min/60min） | OMO | ✅ 高 | **源码确认：** `DEFAULT_STALE_TIMEOUT_MS = 2_700_000` (45min)，`DEFAULT_MESSAGE_STALENESS_TIMEOUT_MS = 3_600_000` (60min)。详见 `references/source-evidence/omo-evidence.md §3` |
| Intent Gate 意图门禁（强制 verbalize 路由判断） | OMO/🆕 | ⚠️ 中 | OMO Atlas Agent 有 task routing 逻辑但无 "Intent Gate" 命名；4条规则的具体形式可能为本 skill 独创 |
| Category Dispatch 分类机制 | OMO | ✅ 高 | **源码确认：** categories 包括 `visual-engineering`, `ultrabrain`, `deep`, `quick`, `artistry`, `writing` 等，见 `dynamic-agent-category-skills-guide.ts` |
| Category Dispatch 中的模型绑定（Gemini 3.1 Pro / GPT-5.4 等） | OMO | ❌ 低 | **需要区分：** Category 定义本身不绑定模型名称。OMO 有独立的 4-step 模型决议系统（override → category-default → provider-fallback → system-default）。本 skill 的 Category Dispatch 表中的模型名称为推测填充 |
| Category 名称 `deep-work` | OMO | ❌ 错 | OMO 实际 category 名为 `deep`，非 `deep-work` |
| Category 名称 `fast-search` | OMO | ❌ 错 | OMO 实际 category 名为 `quick`，非 `fast-search` |
| 工具限制矩阵（Risk Analyst 禁止 Write/Edit 等） | 🆕 设计提案 | ❌ 低 | **已核实：** OMO 源码中**不存在**角色级工具限制。此矩阵为本 skill 的设计建议，非源方案事实。正文已标注"设计提案" |
| Unstable Agent 处理（升格为监控后台模式） | OMO | ⚠️ 中 | OMO 有子进程管理和 TTL 机制（`TASK_TTL_MS = 30min`），但"升格为监控后台模式"的具体策略未确认 |
| Ship Gate（用户最终确认才交付） | 🆕 独创 | 🆕 | 本方案原创的质量门禁节点 |

### 维护规则

1. 每次修改涉及到源方案声明时，必须更新此表
2. 新核验到的事实应标注来源文件路径
3. ❌ 低置信度声明在核实前不得作为编排决策的依据

---

## 平台适配 — Platform Adaptation

> 本 skill 的**编排逻辑**（Intent→Plan→Dispatch→Monitor→Report、DAG、门禁、角色模型）是平台无关的。
> 但**工具调用层**因平台而异。以下为各平台的具体映射。

### 加载方式对比

| 维度 | WorkBuddy | Claude Code | OpenCode |
|------|-----------|-------------|----------|
| Skill 加载 | `Skill("orchestra-fusion")` tool call | `/command` 或 description 语义匹配 → System Prompt 注入 | `opencode skill load` |
| Agent 自主发现 | `<available_skills>` 全局注册表，Agent 启动即知 | 基于 `description` 字段语义匹配 | 需手动指定 |
| 加载后行为 | 上下文注入，Agent Loop 中继续推理 | 上下文注入，Agent 在新指令下继续 | 上下文注入 |

### 工具映射

| 编排步骤 | WorkBuddy（原生） | Claude Code | OpenCode |
|---------|-------------------|-------------|----------|
| **派生子代理** | `Agent()` tool + subagent_type | `claude -p "prompt"` CLI 非交互模式 | `opencode agent spawn` |
| **任务队列** | `TaskCreate/Get/Update/List` | 文件队列（`.tasks/` 目录下 JSON） | 文件队列或 TOML plan |
| **Agent 间通信** | `SendMessage(type="message")` | git notes 或共享文件 | stdout 管道 |
| **任务依赖追踪** | `TaskCreate` 的 `addBlockedBy` 字段 | JSON 任务文件中的 `depends_on` 字段 | TOML plan 中的 `after` 字段 |
| **实时看板** | `TaskList` → 表格渲染 | 读取 `.tasks/` 目录 → Markdown 表格 | `opencode status` |
| **工作树隔离** | `EnterWorktree` | 手动 `git worktree add` | 手动 `git worktree add` |
| **代码审查** | `Read` + `Grep` + 内联审查 | `claude -p "review..."` | `opencode review` |

### Claude Code 降级策略

当在 Claude Code 环境加载本 skill 时：

1. **任务管理降级**：创建 `.tasks/` 目录，每个任务一个 `{task-id}.json` 文件
   ```json
   {"id": "scout-1", "role": "scout", "status": "pending", "depends_on": [], "handoff": null}
   ```
2. **子代理降级**：用 `claude -p` 非交互模式，prompt 中包含 Handoff 协议模板
3. **通信降级**：子代理结果写入 `.tasks/{task-id}.result.md`，主 agent 读取后继续
4. **🛑 不支持的**：Slot-Based 并发控制、Circuit Breaker 熔断器、Stale Detection 陈旧检测
   （这些依赖 WorkBuddy 运行时的监控能力，Claude Code 无等效机制）

### OpenCode 降级策略

当在 OpenCode 环境加载本 skill 时：

1. **任务管理降级**：使用 OpenCode 原生 TOML plan 格式
2. **子代理降级**：`opencode agent spawn --role scout --prompt "..."` 
3. **🛑 不支持的**：同 Claude Code，无 Slot 并发/熔断/陈旧检测

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

