# ATO (Agent Team Orchestration) 源方案证据

> 来源：`~/.workbuddy/skills/Agent Team Orchestration/SKILL.md`
> 核查日期：2026-05-02

---

## 1. 角色分工

**原文** (SKILL.md §Roles, line 49-57):

| Role | Purpose | Model guidance |
|------|---------|---------------|
| **Orchestrator** | Route work, track state, make priority calls | High-reasoning model |
| **Builder** | Produce artifacts — code, docs, configs | Cost-effective models |
| **Reviewer** | Verify quality, push back on gaps | High-reasoning model |
| **Ops** | Cron jobs, standups, health checks, dispatching | Cheapest reliable model |

**关键发现**: ATO 只定义了4个角色（Orchestrator/Builder/Reviewer/Ops），无 Scout/Risk Analyst/Plan Validator。我们的角色扩展是 orchestra-fusion 的设计决策。

---

## 2. 任务生命周期

**原文** (SKILL.md §Task States, line 64-66):

```
Inbox → Assigned → In Progress → Review → Done | Failed
```

**规则** (line 68-71):
> - Orchestrator owns state transitions
> - Every transition gets a comment (who, what, why)
> - Failed is a valid end state — capture why and move on

**关键发现**: 我们的 `[Blocked]` 状态是 ATO 原文不具备的扩展。

---

## 3. Handoff 5 要素协议

**原文** (SKILL.md §Handoffs, line 79-86):

```
1. What was done    — summary of changes/output
2. Where artifacts are — exact file paths
3. How to verify    — test commands or acceptance criteria
4. Known issues     — anything incomplete or risky
5. What's next      — clear next action for the receiving agent
```

**正反例** (line 85-86):
> Bad: "Done, check the files."
> Good: "Built auth module at `/shared/artifacts/auth/`. Run `npm test auth` to verify. Known issue: rate limiting not implemented yet. Next: reviewer checks error handling edge cases."

**关键发现**: 这 5 要素在 orchestra-fusion 中被完整保留，一字不差。

---

## 4. 强制审查

**原文** (SKILL.md §Reviews, line 90-96):

> - Builders review specs — "Is this feasible? What's missing?"
> - Reviewers check builds — "Does this match the spec? Edge cases?"
> - Orchestrator reviews priorities — "Is this the right work right now?"
> 
> Skip the review step and quality degrades within 3-5 tasks. Every time.

**原文** (§Common Pitfalls, line 116-117):
> Every artifact gets at least one set of eyes that didn't produce it.

**关键发现**: "at least one set of eyes that didn't produce it" = Reviewer 不能审自己的 → 我们的规则正确。

---

## 5. Orchestrator 不建造

**原文** (§Common Pitfalls, line 124-125):
> The orchestrator routes and tracks — it doesn't build. The moment you start "just quickly doing this one thing," you've lost oversight of the rest of the team.

---

## 6. 未确认项

ATO 中不存在的内容（orchestra-fusion 的扩展）：
- `[Blocked]` 状态（原文只有 Done|Failed）
- Scout / Risk Analyst / Plan Validator 角色
- Pre-Planning 双审查流程
- 6 道质量门禁（原文只有 review 步骤的隐式门禁）
