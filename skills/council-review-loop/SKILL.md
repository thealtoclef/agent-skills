---
name: council-review-loop
description: Iterative council review loop that auto-fixes high/medium/low findings across up to 2 fix rounds, always ending with a final review. Use when user wants a council review, peer review, or says "council review loop".
---

# Council Review Loop

Review-fix loop: review → fix → review → fix → final review. Two fix rounds max, always ends on a review.

## Severity Scale

| Severity | Action |
|----------|--------|
| **high** | Auto-fix. Bugs, incorrect behavior, data loss/corruption risk. |
| **medium** | Auto-fix. Design flaws, missing edge cases, architectural smells, fragile logic. |
| **low** | Auto-fix. Dead code, missing observability, minor robustness gaps. |
| **nit** | Announce only. Naming, style, test coverage, readability suggestions. |

High, medium, and low are **actionable** — they trigger fixes and re-review.
Nit is **advisory** — logged for the user, never fixed in this loop, never triggers another round.

## Workflow

### Round 1: Review

Delegate to `@council`:

```
Peer review this change for correctness, code quality, maintainability,
potential bugs, and architectural concerns.

IMPORTANT: You MUST delegate this review to your councillors. Do NOT
answer yourself. Run multiple councillors in parallel, collect their
individual reviews, then synthesize into a single verdict.

Classify each finding as: high, medium, low, or nit.
Include file, line, and fix suggestion for every finding.

Return CONCUR (zero high/medium/low findings) or REJECT (any high/medium/low findings).
Nit findings alone do not constitute REJECT.
```

If **CONCUR** → report any nit findings as advisory, exit.

If **REJECT** → proceed to Round 1 Fix.

### Round 1: Fix

- Fix every high, medium, and low finding.
- Log nit findings for the user — do not fix them.
- Proceed to Round 2 Review.

### Round 2: Review

Re-submit to `@council`:

```
Previous high/medium/low findings have been fixed.
Re-review for any remaining issues.

IMPORTANT: You MUST delegate this review to your councillors. Do NOT
answer yourself.

Classify each finding as: high, medium, low, or nit.
Return CONCUR or REJECT. Nit findings alone do not constitute REJECT.
```

If **CONCUR** → report any nit findings as advisory, exit.

If **REJECT** → proceed to Round 2 Fix.

### Round 2: Fix

- Fix every remaining high, medium, and low finding.
- Log any new nit findings for the user — do not fix them.
- Proceed to Final Review (no more fixes after this).

### Final Review

Submit to `@council` one last time:

```
All actionable findings from two review rounds have been fixed.
This is the final review — no further fixes will be made.

IMPORTANT: You MUST delegate this review to your councillors. Do NOT
answer yourself.

Classify each finding as: high, medium, low, or nit.
Return CONCUR or REJECT.
```

Report the final verdict:
- If **CONCUR** → clean pass. Report total findings fixed across all rounds, plus any nit findings as advisory.
- If **REJECT** → report remaining high/medium/low findings as **unresolved** for the user to triage.

## Termination

- **2 fix rounds max** — no exceptions.
- **3 reviews total** — the final review is always performed after the last fix round.
- If the council concurs at any review, report and exit (the concur IS the clean final review).
- Nit findings never trigger a fix round.

## Key Rules

- **auto-fix high, medium, and low** — all three severity levels are actionable;
- **nit is advisory only** — announce to user, never fix in this loop;
- **always end on a review** — the loop closes with council verdict, not with a fix;
- **always instruct the council to delegate to councillors** — never let it answer alone;
- **2 fix rounds hard cap** — after that, report whatever remains for the user.
