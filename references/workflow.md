# Workflow — plan, debug, verify, review

Distilled from obra/superpowers. These are the disciplines that prevent rework.

## Brainstorm → plan (before non-trivial work)

For anything multi-file, schema-touching, or risky: think first, write the plan, then execute.
- State the goal in one sentence. List the files you expect to touch.
- Identify the riskiest assumption and how you'll validate it early.
- Prefer the smallest change that fully solves the problem. Don't gold-plate.
- For OxedoScans, check `PLAN.md` (priorities) and `CLAUDE.md` (conventions) first.

## Systematic debugging (the four phases)

**Iron law: NO FIXES WITHOUT ROOT-CAUSE INVESTIGATION FIRST.** Symptom-patching is failure.
Apply to every bug, test failure, build error, or unexpected behavior — *especially* under time
pressure or when "one quick fix" looks obvious.

1. **Root-cause investigation** (before any fix):
   - Read the full error message + stack trace. Note exact line/file/code. They often state the fix.
   - Reproduce consistently. What are the exact steps? Every time? If not reproducible, gather more
     data — don't guess.
   - Check recent changes: `git diff`, recent commits, new deps, config/env differences.
2. **Hypothesis**: form a specific theory of the cause. Predict what you'd see if it's true.
3. **Test the hypothesis**: add a log/assertion/breakpoint that confirms or refutes it. Don't fix yet.
4. **Fix the cause, not the symptom**: minimal change addressing the root. Then re-run to confirm,
   and check you didn't break anything adjacent.

If you've already tried multiple fixes and none worked, stop guessing — go back to phase 1.

## Verification gate (before claiming done)

**Iron law: NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE.** Claiming work is complete
without verification is dishonesty, not efficiency.

Before saying "done", "fixed", "passing", or committing:
1. **Identify** the command that proves the claim.
2. **Run** it fresh and in full, this turn.
3. **Read** the entire output, exit code, failure count.
4. **Verify** the output actually confirms the claim.
5. **Only then** state the claim — *with* the evidence.

| Claim | Requires | NOT sufficient |
|---|---|---|
| Tests pass | Test command output, 0 failures, this turn | A previous run, "should pass" |
| Build clean | Build output succeeds | Partial check, extrapolation |
| Types OK | `tsc --noEmit` 0 errors | "looks right" |
| Feature works | Observed behavior (run it / preview) | Code reads correctly |

If a step was skipped or a test failed, **say so plainly** with the output. Don't hedge a failure
into a success.

## Code review

- **Self-review the diff** before declaring done: correctness bugs, reused-vs-duplicated logic,
  security on auth/money/input paths, scope creep.
- Use `/code-review` (low/medium/high/max/ultra) for a structured pass; `/security-review` for
  auth/payment changes.
- When receiving review feedback: fix the root issue, don't just silence the comment.

## Finishing

- Commit with a clear subject + body explaining *why*. Keep commits scoped.
- Push only when asked / per repo policy. For OxedoScans, the user wants a push after each change set.
- Update the roadmap/PLAN/memory when a milestone lands so status stays truthful.
