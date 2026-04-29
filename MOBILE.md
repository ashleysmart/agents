# Mobile Mode

This file inherits `AGENTS.md`.

If this file conflicts with `AGENTS.md`, this file wins for mobile tasks.

---

## Mobile mode is active

When the user says mobile mode is active, treat the task as mobile-first by default.

---

## Overrides

### 1. Module usage

- Prefer existing mobile-specific modules before creating new ones.
- Keep platform-neutral business logic separate from UI or device integration code.
- Do not put iOS, Android, React Native, Flutter, or device API calls into shared core modules.
- If a feature needs platform-specific behavior, add a narrow adapter module per platform.
- Reuse shared modules only when the interface is already mobile-safe and does not pull in desktop or server assumptions.

### 2. Dependency boundaries

- UI modules may depend on domain modules.
- Domain modules must not depend on UI modules.
- Device/service wrappers must sit at the edge and be injected inward.
- Avoid cross-feature imports between mobile screens unless the dependency is clearly shared infrastructure.

### 3. File and feature structure

- Organize mobile work by feature first, platform second.
- Keep screens, view models/controllers, and platform adapters in separate modules.
- Do not mix navigation, rendering, network calls, and persistence in one file.

### 4. Defaults for implementation

- Optimize for offline tolerance, latency, and small-screen UX constraints.
- Prefer incremental changes over large framework-level refactors.

### 5. Response and execution style

- Keep responses concise and to the point.
- Attempt to run code or commands for the user when practical instead of only describing what to do.
- Assume the user may be voice typing and interpret likely swapped or autocorrected words before asking for clarification.
- Keep commands tight without extra whitespace or decorative formatting.
- Expect commands to run in a local Docker sandbox when available and return the actual results, not just the command text.
