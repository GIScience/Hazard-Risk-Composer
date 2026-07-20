# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.

## Project context: deploy model

This repo is a single Vue 3 + Vite app (the Hazard Risk Dashboard, formerly `gaia-viz-vue` inside `humanitarian_pages`), deployed via GitHub Pages using `.github/workflows/deploy.yml`. It is the only thing served by this repo's Pages site — no other pages coexist here, unlike the old `humanitarian_pages` monorepo.

**Release/dev routing:**
- Every push to `main` rebuilds and deploys the current `main` HEAD to the `/dev/` route (`base=/Hazard-Risk-Composer/dev/`).
- The root route (`/`) always serves the **latest git tag** (`base=/Hazard-Risk-Composer/`) — the workflow checks out `git describe --tags $(git rev-list --tags --max-count=1)` mid-build and builds prod from that, not from HEAD.
- So: merging to `main` alone never changes what users see at the root URL. To ship a new release, cut a new tag (e.g. `git tag vX.Y.Z && git push origin vX.Y.Z`) — that alone doesn't trigger a deploy either, since the workflow only runs `on: push branches: [main]`. A tag only takes effect on the *next* push to `main` (or a manual `workflow_dispatch` run). If you need the release live immediately after tagging, trigger the workflow manually (`gh workflow run deploy.yml`).
- The workflow requires **at least one tag to exist** — `git describe --tags` fails on an empty tag list. If this is a fresh checkout with no tags yet, create one (e.g. `v0.1.0`) before the first deploy.

This mirrors the pattern originally used for the GAIA build inside `humanitarian_pages/.github/workflows/deploy-gaia-viz-vue.yml`, adapted so this repo has no other pages to stage alongside it.
