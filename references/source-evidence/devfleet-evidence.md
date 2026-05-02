# DevFleet (Claude DevFleet) 源方案证据

> 来源：`~/.workbuddy/skills/claude-devfleet/SKILL.md`
> 核查日期：2026-05-02

---

## 1. DAG 依赖规划 + auto_dispatch

**原文** (§How It Works, line 19-36):

```
User → "Build a REST API with auth and tests"
  ↓
plan_project(prompt) → project_id + mission DAG
  ↓
Show plan to user → get approval
  ↓
dispatch_mission(M1) → Agent 1 spawns in worktree
  ↓
M1 completes → auto-merge → auto-dispatch M2 (depends_on M1)
  ↓
M2 completes → auto-merge
  ↓
get_report(M2) → Report back to user
```

**auto_dispatch 机制** (§Tools, line 44):
> `create_mission(project_id, title, prompt, depends_on?, auto_dispatch?)` — Set `auto_dispatch=true` to auto-start when deps are met.

**关键发现**: DAG + auto_dispatch 确实来自 DevFleet，这是其核心机制。

---

## 2. 工作树隔离

**原文** (§Tools, line 12):
> Each agent runs in an isolated git worktree with full tooling.

DevFleet 自动管理 worktree，无需用户手动 `git worktree add`。

---

## 3. 看板仪表盘

**原文** (§Tools, line 50-51):

| Tool | Purpose |
|------|---------|
| `get_dashboard()` | System overview: running agents, stats, recent activity |
| `get_mission_status(mission_id)` | Check mission progress without blocking |

---

## 4. Plan→Dispatch→Monitor→Report 管线

**原文** (§Workflow, line 56-62):

```
1. Plan: plan_project(prompt=...) → project_id + missions with depends_on
2. Show plan: Present to user
3. Dispatch: dispatch_mission on root mission, rest auto-dispatch
4. Monitor: get_mission_status or get_dashboard
5. Report: get_report for each terminal mission
```

**关键发现**: 这 5 步管线是 orchestra-fusion 工作流的直接来源。我们在其基础上加了 Intent Gate 和更细化的阶段输出格式。

---

## 5. 并发控制

**原文** (§Concurrency, line 66):

> DevFleet runs up to 3 concurrent agents by default (configurable via `DEVFLEET_MAX_AGENTS`). When all slots are full, missions with `auto_dispatch=true` queue and dispatch automatically as slots free up.

**关键发现**: DevFleet 默认 3 并发槽位（非 5），且是全局槽位（非按 provider/model 分组）。我们的 Slot-Based 分组模型来自 OMO。

---

## 6. 用户确认

**原文** (§Guidelines, line 97):
> Always confirm the plan with the user before dispatching, unless they said to go ahead.

---

## 7. 未确认项

在 DevFleet 中不存在的内容：
- Intent Gate（这是 OMO 的概念）
- Circuit Breaker / Stale Detection（来自 OMO）
- Category Dispatch（来自 OMO）
- 6 段式计划模板（来自 OMO）
- 详细 Handoff 协议（来自 ATO）
