---
name: diligence
description: Verification checklist for code changes — always run before claiming a task is complete.
---

# Diligence

Before reporting any task as complete, verify:

1. **Doc coverage** — run `python3 tests/_check.py` to confirm zero undocumented functions.
2. **Compilation** — `cargo check --workspace` must pass with no errors.
3. **Git status** — no uncommitted changes unless intentionally deferred.
4. **Remote parity** — `git log --oneline -1` matches `origin/master`.
5. **Claim specificity** — report exactly what was done and what was verified, not what was assumed.

Do not skip the check script. Do not assume prior passes still hold.
