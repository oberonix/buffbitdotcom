# AGENTS.md — Claude Code Instructions

## Pre-commit review (`/second-opinion`)

Every logical change gets an independent review via the `/second-opinion` skill before commit. The skill invokes the Codex CLI (GPT-5 family) and falls back to an Opus subagent if Codex is out of quota — either way you get a reviewer that didn't write the code.

**Workflow for each change**:
1. Finish the change. Build + tests must already pass.
2. Stage but don't commit — or commit first and review `HEAD`, either works.
3. Run `/second-opinion` (default scopes the last commit; use `--uncommitted` scope if reviewing staged/unstaged work).
4. Read the findings. For each one:
   - **Real correctness, safety, or regression concern** → fix it in this session. Verify the finding against the actual code before applying — a reviewer can be wrong, and you're the one committing. Amend the commit or make a follow-up commit.
   - **Style / nit** → skip unless the user wants tighter cleanup.
   - **Unclear whether the concern is real, or whether the fix aligns with user intent** → flag it to the user. Don't guess. Ask what they want, then proceed.
5. Commit + push.

**Skip the review only when**:
- The change is pure formatting / comments / docs with zero behavior impact.
- The user has explicitly said to skip it for this change.

Don't skip on "it's just a one-liner" or "tests pass" — one-liners with passing tests have still shipped real regressions.
