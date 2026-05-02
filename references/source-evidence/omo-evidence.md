# OMO (oh-my-opencode) 源方案证据

> 来源：`code-yeongyu/oh-my-openagent` GitHub 仓库
> 核查日期：2026-05-02
> 核查方法：clone 仓库 → grep 关键模式 → 逐文件阅读核心模块

---

## 1. Slot-Based 并发模型

**文件**: `src/features/background-agent/concurrency.ts`

```typescript
export class ConcurrencyManager {
  private config?: BackgroundTaskConfig
  private counts: Map<string, number> = new Map()
  private queues: Map<string, QueueEntry[]> = new Map()

  getConcurrencyLimit(model: string): number {
    const modelLimit = this.config?.modelConcurrency?.[model]
    if (modelLimit !== undefined) {
      return modelLimit === 0 ? Infinity : modelLimit
    }
    const provider = model.split('/')[0]
    const providerLimit = this.config?.providerConcurrency?.[provider]
    if (providerLimit !== undefined) {
      return providerLimit === 0 ? Infinity : providerLimit
    }
    const defaultLimit = this.config?.defaultConcurrency
    if (defaultLimit !== undefined) {
      return defaultLimit === 0 ? Infinity : defaultLimit
    }
    return 5  // ← 默认 5 槽位
  }

  async acquire(model: string): Promise<void> {
    // ... FIFO 排队逻辑
  }

  release(model: string): void {
    // ... 释放逻辑，优先唤醒等待者
  }
}
```

**关键发现**:
- 默认 5 槽位（line 39: `return 5`）
- 三级优先级：modelConcurrency > providerConcurrency > defaultConcurrency
- FIFO 队列，settled-flag 防止 double-resolution
- `cancelWaiters()` 支持清理取消

---

## 2. Circuit Breaker 熔断器

**文件**: `src/features/background-agent/constants.ts`

```typescript
export const DEFAULT_MAX_TOOL_CALLS = 4000
export const DEFAULT_CIRCUIT_BREAKER_CONSECUTIVE_THRESHOLD = 20
export const DEFAULT_CIRCUIT_BREAKER_ENABLED = true
```

**文件**: `src/features/background-agent/loop-detector.ts`

```typescript
export interface CircuitBreakerSettings {
  enabled: boolean
  maxToolCalls: number         // 默认 4000
  consecutiveThreshold: number  // 默认 20
}

export function detectRepetitiveToolUse(
  window: ToolCallWindow | undefined
): ToolLoopDetectionResult {
  if (!window || window.consecutiveCount < window.threshold) {
    return { triggered: false }
  }
  return {
    triggered: true,
    toolName: window.lastSignature.split("::")[0],
    repeatedCount: window.consecutiveCount,
  }
}
```

**关键发现**:
- 连续 20 次相同签名调用触发告警（`consecutiveCount >= threshold`）
- 累计 4000 次后强制取消（`maxToolCalls`）
- 签名计算：`toolName::JSON.stringify(sortObject(toolInput))` —— 按键排序的 JSON

---

## 3. Stale Detection 陈旧检测

**文件**: `src/features/background-agent/constants.ts`

```typescript
export const TASK_TTL_MS = 30 * 60 * 1000              // 30 分钟
export const DEFAULT_STALE_TIMEOUT_MS = 2_700_000       // 45 分钟
export const DEFAULT_MESSAGE_STALENESS_TIMEOUT_MS = 3_600_000  // 60 分钟
export const DEFAULT_SESSION_GONE_TIMEOUT_MS = 60_000   // 1 分钟
```

**关键发现**:
- 45 分钟无活动 = stale（`DEFAULT_STALE_TIMEOUT_MS`）
- 60 分钟无消息更新 = message staleness
- 30 分钟 TTL（`TASK_TTL_MS`）
- 独立子进程 idle 检测：`src/cli/run/completion.ts` 递归检查所有子 session

---

## 4. Category Dispatch 分类机制

**文件**: `src/agents/dynamic-agent-category-skills-guide.ts`

```typescript
// OMO 中的 category 名称（原文）：
// - visual-engineering  : UI/UX/CSS/设计/动画/布局
// - ultrabrain          : 重逻辑/架构决策/算法
// - deep                : 目标导向自主工作（非 "deep-work"）
// - quick               : 快速执行（非 "fast-search"）
// - artistry            : 创意设计/视觉艺术
// - writing             : 文档/写作
// - unspecified-low     : 未指定低优先级
// - unspecified-high    : 未指定高优先级
```

**模型决议系统**（4-step，独立于 Category 定义）:
```
override → category-default → provider-fallback → system-default
```
见 `src/agents/AGENTS.md:80`

**关键发现**:
- Category 名称不包括模型名，模型通过独立的 4-step 决议系统匹配
- 之前 skill 中的 `deep-work` / `fast-search` 为错误名称
- Category Dispatch 表中的具体模型名（Gemini 3.1 Pro 等）为推测补充

---

## 5. Intent Gate（意图路由）

**文件**: `src/agents/sisyphus/gpt-5-5.ts:189`

```typescript
// OMO 的 task routing 逻辑：
// "If no specialist matches but a category does 
//  (visual-engineering, artistry, ultrabrain, deep, quick, writing), 
//  delegate via task(category=..., load_skills=[...])"
```

**关键发现**:
- OMO 有 category-based task routing，但无 "Intent Gate" 命名
- "Intent Gate" 的名称 + 4 条 verbalize 规则为本 skill 的设计包装
- 概念本质上存在于 OMO 的 Atlas/Sisyphus agent prompt 中

---

## 6. 工具限制

**关键发现: 不存在**

在 OMO 源码中（`src/` 全部 TypeScript 文件）:
- ❌ 无角色级工具白名单/黑名单
- ❌ 无 "Risk Analyst 禁止 Write" 之类的配置
- OMO 的工具控制通过子代理 session 的权限隔离实现，而非声明式角色限制

---

## 7. 未确认项

以下 orchestra-fusion 中的声明在 OMO 源码中**未找到对应实现**：

| 声明 | 搜索模式 | 结果 |
|------|---------|------|
| Unstable Agent → 升格为监控后台模式 | `unstable`, `background mode`, `monitor mode` | 未找到 |
| Pre-Planning 双审查的具体流程 | `pre.plan`, `risk analyst`, `plan validator` | 概念存在，具体步骤可能为 skill 补充 |
