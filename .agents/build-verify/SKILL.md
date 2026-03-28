---
name: build-verify
description: Verifies compilation after code changes, fixes errors without ts-ignore or test hacks, and reports status. Use when the user asks to build, verify, or check compilation, or after edits that might affect compilation.
---

# Skill: Build & Verify

## When to use

After making code changes that might affect compilation.
Triggered by: "build", "verify", "check compilation".

## Inputs

- Changed files (from git diff or conversation context)

## Build commands — choose based on what changed

This is a monorepo. `yarn build` only builds the web app (`excalidraw-app`), not the library packages.

| Changed files | Command |
|---|---|
| `packages/**` only | `yarn build:packages` |
| `excalidraw-app/**` only | `yarn build:app` |
| Both, or unsure | `yarn build:packages && yarn build:app` |

**Package build order is enforced by `build:packages`:** common → math → element → excalidraw. Do not build packages individually out of order.

For type errors specifically, `yarn test:typecheck` (runs `tsc` across the whole monorepo) is faster than a full Vite build and should be tried first.

## Steps

1. Run `yarn test:typecheck` to catch type errors quickly
2. Run the appropriate build command from the table above
3. If a step succeeds → report success, list changed files
4. If a step fails:
   a. Read error output — identify file, line, error type
   b. Open the file at the error line
   c. Fix the issue (type error, missing import, syntax)
   d. Re-run the failing command
   e. Repeat until it passes (max 3 attempts)

## Outputs

- Build status: PASS / FAIL
- Which commands were run
- List of fixes applied (if any)
- Full build output

## Safety

- Do NOT fix errors by adding `@ts-ignore` or `any`
- Do NOT modify test files to fix build errors
- If 3 attempts fail — stop and report to user
