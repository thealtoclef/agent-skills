---
name: council-review-loop
description: Iterative council review loop that fixes all findings until council concurs without complaints. Use when user wants a council review, peer review, or says "council review loop".
---

# Council Review Loop

Iterative review loop: council peer review → fix all findings → repeat until council concurs with zero complaints.

## Workflow

### 1. Council Peer Review

Delegate to `@council` with this prompt structure:

```
Peer review this change for correctness, code quality, maintainability,
naming, style, dead code, potential bugs, and architectural concerns.

Be HARSH. Flag EVERYTHING: errors, warnings, nits, style issues,
naming inconsistencies, dead code, missing edge cases, potential bugs.

Severity levels: error, warning, nit, suggestion.

Return your verdict: CONCUR (zero findings) or REJECT (any findings).

If REJECT: list ALL findings with severity, file, line, and fix suggestion.
```

### 2. Fix All Council Findings

If council returns REJECT or any findings (even nits/suggestions):

- fix EVERY finding, no matter how minor;
- do not dismiss nits or suggestions as "non-blocking";
- apply all fixes before re-submitting.

### 3. Re-submit to Council

After fixing, send back to `@council` with:

```
Previous findings fixed. Re-review for any remaining issues.
Same harshness. Return CONCUR or REJECT with findings.
```

### 4. Repeat Until Concur

Loop steps 1-3 until council returns **CONCUR with zero findings**.

### 5. Final Summary

When council concurs:

- report total iterations and findings fixed.

## Termination

- max 5 iterations; if still rejecting, report blockers to user;
- if council returns CONCUR, exit immediately.

## Key Rules

- never skip findings labeled "nit" or "non-blocking";
- never self-dismiss findings without fixing;
- council's CONCUR is the only exit condition;
- treat all severity levels (error, warning, nit, suggestion) as must-fix.
