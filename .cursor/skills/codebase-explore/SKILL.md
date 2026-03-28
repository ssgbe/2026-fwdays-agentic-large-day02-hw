# Skill: Codebase Explorer

## When to use

When you need to understand an unfamiliar area of the codebase.
Triggered by: "explore", "investigate", "how does X work?"

## Inputs

- Area of interest (module, feature, file pattern)

## Monorepo orientation

Package dependency order: `@excalidraw/common` → `@excalidraw/math` → `@excalidraw/element` → `@excalidraw/excalidraw` → `excalidraw-app`

`@excalidraw/*` imports resolve to `packages/*/src/` via tsconfig path aliases — **never to `dist/`**. Always follow imports into `src/`.

Key entry points:
- Library: `packages/excalidraw/index.tsx`
- Web app: `excalidraw-app/index.tsx`
- Architecture docs: `dev-docs/docs/` (the root `README.md` is user-facing, not architectural)

## Steps

1. Identify relevant directory/files using @folder or @codebase
2. Check `dev-docs/docs/` for any documentation on the area; fall back to top-level comments in source
3. Map the key files and their responsibilities
4. Trace data flow: entry point → processing → output
5. Identify cross-package dependencies — follow `@excalidraw/*` imports into `packages/*/src/`
6. Document findings in a summary

## Outputs

- Summary: purpose, key files, data flow, dependencies
- List of related files for deeper investigation

## Safety

- READ-ONLY — do not modify any files during exploration
- Verify findings against actual code, not assumptions
