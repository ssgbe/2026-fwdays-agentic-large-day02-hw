# /review-code

Review the currently open file or selected code against Excalidraw project conventions and quality standards.

## When to use

Run this command before opening a PR or after making significant changes to any file in `packages/` or `excalidraw-app/`. It catches issues that ESLint and TypeScript alone won't catch — architectural violations, missing test coverage, security gaps, and convention drift.

## Instructions

Perform a structured code review of the selected code (or the entire file if nothing is selected). Go through each checklist section below and report findings grouped by severity: **❌ blocker**, **⚠️ warning**, **✅ ok**.

### 1. TypeScript & Types
- No `any` or `@ts-ignore` without an explanatory comment
- Type-only imports use `import type { X }` syntax
- Component props are defined as `interface {Name}Props`, not inline or as `type`
- No implicit `any` from missing return types on exported functions

### 2. React & Component Conventions
- Functional components only — no new class components (exception: `App.tsx` is a legacy class component)
- No direct DOM manipulation (`document.getElementById`, `querySelector`) — use React refs
- CSS is via CSS Modules (`.module.scss`) — no inline `style={{}}` for layout, only for dynamic values
- No new global state libraries (Redux, Zustand, MobX) — use Jotai atoms for local UI state or `AppState` for editor state

### 3. Architecture & Package Boundaries
- Imports follow the dependency direction: `common → math → element → excalidraw → excalidraw-app`
- No import of `excalidraw-app/` code from inside `packages/`
- Use `@excalidraw/*` path aliases, not relative `../../../` cross-package imports
- Element mutations go through `mutateElement()` or action `ActionResult` — never mutate element objects directly

### 4. Security
- No `innerHTML` or `dangerouslySetInnerHTML` with user-supplied content
- URLs from element `link` property validated — no `javascript:` or `data:` schemes
- Imported `.excalidraw` file content passed through `restore()` before use
- No encryption keys or room IDs logged or persisted

### 5. Performance
- No DOM layout reads (`getBoundingClientRect`, `offsetWidth`) inside render or pointer-move handlers
- Expensive computations in render paths are memoized (`useMemo`, `useCallback`, or cached outside the component)
- Canvas context transforms wrapped in `context.save()` / `context.restore()`

### 6. Tests
- New utility functions or element mutations have a colocated `*.test.ts` / `*.test.tsx`
- Tests use `API.createElement()`, `UI`, `Keyboard`, `mouse` helpers — no raw element object construction
- No `expect(x).toBeTruthy()` instead of `expect(x).toBe(true)` (or the specific value)

### 7. Naming & Style
- Components and types: `PascalCase`; functions and variables: `camelCase`; constants: `ALL_CAPS`
- No abbreviations in public API (e.g., `el`, `elem` → `element`; `cfg` → `config`)
- Exported functions have explicit return types

## Expected output format

```
## Code Review: <filename>

### ❌ Blockers
- <issue> (line N) — <why it's a blocker>

### ⚠️ Warnings
- <issue> (line N) — <why it matters>

### ✅ Looks good
- <what was done correctly>

### Summary
<1–2 sentence overall assessment and recommended next step>
```

## Example usage

1. Open `packages/excalidraw/actions/actionAddToLibrary.ts`
2. Type `/review-code` in the Cursor chat
3. Review the structured output, fix blockers before pushing
