# dmux (dmux-workflows) 源方案证据

> 来源：`~/.workbuddy/skills/dmux-workflows/SKILL.md`
> 核查日期：2026-05-02

---

## 1. 并行模式

dmux 定义了 **5 种**并行模式（非 6 种）：

**Pattern 1: Research + Implement** (line 44-57)
```
Pane 1 (Research): research best practices → /tmp/research.md
Pane 2 (Implement): start implementation, refine after research
```

**Pattern 2: Multi-File Feature** (line 59-69)
```
Pane 1: Database schema + migrations
Pane 2: API endpoints
Pane 3: Dashboard UI components
→ Merge all, integration in main pane
```

**Pattern 3: Test + Fix Loop** (line 71-80)
```
Pane 1 (Watcher): Run test suite, summarize failures
Pane 2 (Fixer): Fix failing tests
```

**Pattern 4: Cross-Harness** (line 82-90)
```
Pane 1 (Claude Code): Security review
Pane 2 (Codex): Performance refactor
Pane 3 (Claude Code): E2E tests
```

**Pattern 5: Code Review Pipeline** (line 92-102)
```
Pane 1: Security vulnerabilities review
Pane 2: Performance issues review
Pane 3: Test coverage gaps review
→ Merge all into single report
```

**⚠️ 发现**: orchestra-fusion 的设计哲学表声称 `dmux-workflows = 6种并行模式`，但 dmux 实际只有 5 种。我们的 Pattern 5 (Escalation) 是创新扩展。Pattern 0 (Interview-Mode) 来自 OMO。

---

## 2. Merge 策略

dmux 的 merge 是 UX 操作而非策略框架：

**原文** (§What is dmux, line 21):
> Press 'm' to merge pane output back to the main session

**原文** (§Best Practices, line 108):
> Merge strategically. Review pane output before merging to avoid conflicts.

**关键发现**: dmux 没有定义"merge 策略"——只是提供了 `m` 键合并功能。我们的 orchestra-fusion 将 merge 概念化为了 report 阶段的"汇总产出"步骤。

---

## 3. Git Worktree 集成

**原文** (§Git Worktree Integration, line 114-128):

```bash
git worktree add -b feat/auth ../feature-auth HEAD
git worktree add -b feat/billing ../feature-billing HEAD
# Pane 1: cd ../feature-auth && claude
# Pane 2: cd ../feature-billing && claude
git merge feat/auth
git merge feat/billing
```

dmux 也有 `scripts/orchestrate-worktrees.js` 自动化此流程。

---

## 4. 资源建议

**原文** (§Best Practices, line 110):
> Resource awareness. Each pane uses API tokens — keep total panes under 5-6.

**关键发现**: dmux 建议最多 5-6 个并发 pane（基于 token 消耗）。这与 OMO 的 5 槽位巧合一致。

---

## 5. 未确认项 / 差异

| orchestra-fusion 声称 | dmux 实际 | 说明 |
|---------------------|-----------|------|
| 6 种并行模式 | 5 种 | Pattern 5 (Escalation) 是 orchestra-fusion 创新 |
| merge 策略 | 仅是 `m` 键操作 | 非策略框架，是 UX 功能 |
| Pattern 0 (Interview-Mode) | 不存在 | 来自 OMO |
