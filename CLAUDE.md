# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Development
yarn start                  # Start dev server (excalidraw-app)
yarn start:production       # Build and serve production version

# Build
yarn build                  # Full build (app + version)
yarn build:packages         # Build all packages (must do before building app in isolation)
yarn build:app              # Build web application only

# Testing
yarn test:app               # Run unit tests with Vitest
yarn test:code              # Run ESLint
yarn test:other             # Run Prettier check
yarn test:typecheck         # TypeScript type checking
yarn test:all               # Run all tests
yarn test:coverage          # Generate coverage report

# Single test file
yarn test:app path/to/file.test.ts

# Fix formatting/linting
yarn fix                    # Fix both formatting and linting
yarn fix:code               # ESLint auto-fix only
yarn fix:other              # Prettier auto-format only
```

## Architecture

This is a **Yarn workspaces monorepo** with a layered package dependency structure:

```
@excalidraw/common    (shared constants, utils, event bus, font metadata)
       ↓
@excalidraw/math      (geometric primitives: Point, Vector, Line, Curve, Ellipse, etc.)
       ↓
@excalidraw/element   (element creation, mutation, binding, bounds, rendering logic)
       ↓
@excalidraw/excalidraw  (main React component library, published to npm)
       ↓
excalidraw-app        (excalidraw.com web application, adds Firebase/collab/auth)
```

**`packages/excalidraw`** is the npm package users install. Its core is `components/App.tsx` (~12k lines), which houses the editor state machine. State is managed via Jotai atoms and React context. The `scene/` directory manages the canvas scene graph; `renderer/` handles canvas drawing; `actions/` maps user inputs to mutations.

**`excalidraw-app`** wraps the npm package with Firebase-backed collaboration (`collab/`), authentication, and data persistence for the hosted app.

**`packages/utils`** provides export utilities (PNG, SVG, clipboard) used independently of the main component.

## Key Conventions

- Use `Point` from `packages/math/src/types.ts` instead of `{ x, y }` objects
- React functional components with hooks only; no class components
- CSS Modules for component styles
- PascalCase for components/types, camelCase for functions/variables, ALL_CAPS for constants
- TypeScript strict mode is enabled; path aliases `@excalidraw/*` resolve to package source files (not dist)

## Testing

Tests use **Vitest** with jsdom. Coverage thresholds: 60% lines, 70% branches, 63% functions. Test files are colocated with source (`*.test.ts` / `*.test.tsx`). Run `yarn test:app` after any logic changes.
