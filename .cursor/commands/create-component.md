# /create-component

Scaffold a new React component following Excalidraw's conventions — generates the `.tsx` file, colocated `.scss` file, and a minimal test file.

## When to use

When you need to add a new UI component to `packages/excalidraw/components/`. Describe what the component should do and the command will generate all three files with correct structure, naming, and imports.

## Instructions

Generate a new Excalidraw component based on the user's description. Ask for the following if not provided:

1. **Component name** — PascalCase (e.g., `ColorSwatch`, `LayerPanel`, `ToolbarDivider`)
2. **Props** — what data the component receives and what events it emits
3. **Rough behavior** — what the component renders and how it responds to interaction

Then generate three files:

---

### File 1: `packages/excalidraw/components/{Name}.tsx`

Rules:
- Functional component only — no class components
- Props defined as `interface {Name}Props` (not `type`, not inline)
- Named export (not default export) for components in `packages/excalidraw/components/`
- Import CSS Module via `import "./{Name}.scss"`
- Use `clsx` from `clsx` for conditional class names — never string concatenation
- All className strings must be nested under `.excalidraw {}` in the SCSS (see below) — prefix component root class with the component name (e.g., `.ColorSwatch`)
- Use `React.FC<{Name}Props>` type annotation
- No inline `style={{}}` for static layout — only for dynamic values (e.g., a color derived from props)
- Do not use `document.getElementById` or `querySelector` — use `React.useRef` if DOM access is needed
- Accessible markup: interactive elements must have `role`, `aria-*` attributes, and keyboard handlers where appropriate

Template:
```tsx
import clsx from "clsx";
import React from "react";

import "./{Name}.scss";

interface {Name}Props {
  // define props here
  className?: string;
}

export const {Name}: React.FC<{Name}Props> = ({
  // destructure props
  className,
}) => {
  return (
    <div className={clsx("{Name}", className)}>
      {/* component content */}
    </div>
  );
};
```

---

### File 2: `packages/excalidraw/components/{Name}.scss`

Rules:
- All rules nested inside `.excalidraw { }` wrapper — this scopes styles to the editor and enables dark mode via `[data-theme="dark"]`
- Use `@use "../css/theme" as *` to access theme mixins (`@include outlineButtonStyles`, etc.)
- Use `@use "../css/variables.module" as *` to access design tokens (`$color-blue-4`, `$border-radius-md`, etc.)
- Use `sass:color` for color manipulation — never hardcode derived colors
- BEM-like naming: `.{Name}` for root, `.{Name}-{part}` for children, `.{Name}--{modifier}` for states

Template:
```scss
@use "../css/theme" as *;
@use "../css/variables.module" as *;

.excalidraw {
  .{Name} {
    // layout styles

    &--active {
      // modifier
    }

    .{Name}-label {
      // child element
    }
  }
}
```

---

### File 3: `packages/excalidraw/components/{Name}.test.tsx`

Rules:
- Use `renderApp(<Excalidraw />)` from `packages/excalidraw/tests/test-utils.ts` — not bare `render()`
- Use `UI`, `Keyboard`, `mouse` helpers from `packages/excalidraw/tests/helpers/ui.ts` for interactions
- Do not construct raw element objects — use `API.createElement()` from `packages/excalidraw/tests/helpers/api.ts`
- At minimum: one render test (component appears), one interaction test (props/callbacks work)

Template:
```tsx
import React from "react";
import { describe, it, expect, vi } from "vitest";
import { render, screen, fireEvent } from "@testing-library/react";

import { {Name} } from "./{Name}";

describe("{Name}", () => {
  it("renders without crashing", () => {
    render(<{Name} {/* required props */} />);
    expect(screen.getByRole("...")).toBeInTheDocument();
  });

  it("calls callback on interaction", () => {
    const onSomething = vi.fn();
    render(<{Name} onSomething={onSomething} {/* other required props */} />);
    fireEvent.click(screen.getByRole("..."));
    expect(onSomething).toHaveBeenCalledOnce();
  });
});
```

---

## After generating

Remind the user to:

1. Export the component from `packages/excalidraw/index.tsx` if it should be part of the public API
2. Run `yarn test:app packages/excalidraw/components/{Name}.test.tsx` to verify tests pass
3. Run `yarn test:typecheck` to confirm no type errors
4. Run `yarn fix` to auto-format generated files before committing

## Example usage

```
/create-component
Component name: ColorSwatch
Props: color (string), selected (boolean), onSelect (() => void)
Behavior: Renders a circular swatch; clicking calls onSelect; adds a checkmark when selected
```

Expected output: three files — `ColorSwatch.tsx`, `ColorSwatch.scss`, `ColorSwatch.test.tsx` — ready to use.
