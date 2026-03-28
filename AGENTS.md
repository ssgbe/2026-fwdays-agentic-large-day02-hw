# AGENTS.md

## Project Overview

Excalidraw is an open-source virtual whiteboard for sketching hand-drawn diagrams. It is built as a React component library (`@excalidraw/excalidraw`) published to npm, and a hosted web application at excalidraw.com. The library can be embedded in any React app with a single import. The hosted app adds Firebase-backed real-time collaboration, end-to-end encrypted rooms, and persistent storage.

## Tech Stack

- **React 18** — UI, functional components + hooks throughout
- **TypeScript** — strict mode enabled across all packages
- **Vite** — dev server and production build for `excalidraw-app`
- **esbuild** — package builds for `packages/*`
- **Yarn workspaces** — monorepo package management (not npm, not pnpm)
- **Vitest + jsdom** — unit and integration tests
- **RoughJS** — hand-drawn rendering style on Canvas 2D
- **Jotai** — local UI state atoms (sidebar, language, library)
- **Firebase** — collaboration backend (Firestore + Storage) in `excalidraw-app` only

## Project Structure

Excalidraw is a **monorepo** with a clear separation between the core library and the application:

- **`packages/excalidraw/`** - Main React component library published to npm as `@excalidraw/excalidraw`
- **`excalidraw-app/`** - Full-featured web application (excalidraw.com) that uses the library
- **`packages/`** - Core packages: `@excalidraw/common`, `@excalidraw/element`, `@excalidraw/math`, `@excalidraw/utils`
- **`examples/`** - Integration examples (NextJS, browser script)

## Development Workflow

1. **Package Development**: Work in `packages/*` for editor features
2. **App Development**: Work in `excalidraw-app/` for app-specific features
3. **Testing**: Always run `yarn test:update` before committing
4. **Type Safety**: Use `yarn test:typecheck` to verify TypeScript

## Development Commands

```bash
yarn start           # Start dev server at localhost:3000
yarn test:typecheck  # TypeScript type checking
yarn test:update     # Run all tests (with snapshot updates)
yarn fix             # Auto-fix formatting and linting issues
```

## Conventions

### Naming
- **Components and types/interfaces:** PascalCase — `LayerUI`, `ExcalidrawElement`, `ActionResult`
- **Functions, variables, hooks:** camelCase — `mutateElement`, `appState`, `useExcalidrawAppState`
- **Constants:** ALL_CAPS — `CANVAS_PADDING`, `DEFAULT_FONT_SIZE`
- **Component files:** PascalCase — `ColorPicker.tsx`, `LayerUI.tsx`
- **Utility/module files:** camelCase — `appState.ts`, `clipboard.ts`, `gesture.ts`
- **Test files:** mirror source name — `clipboard.test.ts`, `DefaultSidebar.test.tsx`

### Code Style
- Functional components + hooks only; `App.tsx` is the sole legacy class component
- Props defined as `interface {Name}Props`, never as `type` or inline
- `import type { X }` for all type-only imports
- No `any` or `@ts-ignore` without an explanatory comment
- CSS Modules (`.module.scss`) for styles; no inline `style={{}}` for static layout
- `clsx` for conditional class names, never string concatenation
- Named exports for utilities and types; default exports for components

### PR Workflow
- Branch from `master`; one feature/fix per PR
- Run `yarn test:all` locally before opening a PR
- PR title uses semantic prefix: `feat:`, `fix:`, `docs:`, etc.
- Changesets required for any change to `packages/excalidraw` public API

## Commit Convention

Use semantic prefixes: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`

## Do-Not-Touch / Constraints

These files must not be modified without explicit team approval and a full test run:

- `packages/excalidraw/types.ts` — core element and app state types; changes break all consumers
- `packages/excalidraw/data/restore.ts` — file format compatibility; changes can corrupt saved files
- `packages/excalidraw/actions/manager.tsx` — central action dispatcher; changes affect undo/redo and all actions
- `packages/excalidraw/scene/renderer.ts` — static render pipeline; performance-critical

Additional constraints:
- Do **not** switch the package manager from Yarn — the repo uses Yarn workspaces and `yarn.lock`
- Do **not** add Redux, Zustand, MobX, or any global state library — use Jotai atoms or `AppState`
- Do **not** add react-konva, Fabric.js, Pixi.js — all rendering goes through Canvas 2D API directly
- Do **not** publish `excalidraw-app` to npm — it is the hosted app, not a library
- Do **not** store collaboration encryption keys server-side or in Firebase

## Architecture Notes

### Package System

- Uses Yarn workspaces for monorepo management
- Internal packages use path aliases (see `vitest.config.mts`)
- Build system uses esbuild for packages, Vite for the app
- TypeScript throughout with strict configuration
