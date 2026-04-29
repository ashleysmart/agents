# Agent Guidelines

Rules and expectations for all AI agents working in this repository tree.
These guidelines apply to every project unless a project-level `Agents.md`
explicitly overrides a section.

---

##  Micro Spec Convention

Every non-trivial task begins with a micro spec written **before** any code.

### File location

Short term PR spec
```
<project>/docs/<YYYYMMDD>_<short-task-slug>.md
```

Long term service/task/module spec
```
<project>/docs/<module-slug>.md
```

### Required sections

| Section | Purpose |
|---|---|
| **Goal** | One sentence — what problem this solves and why |
| **Scope** | What is in and explicitly out of scope |
| **Interfaces** | Public API / data shapes / events exposed by this work |
| **Behaviour** | Numbered acceptance criteria, written as observable facts |
| **Error cases** | How each failure mode is detected and surfaced |
| **Test plan** | List of unit tests required; maps 1-to-1 to Behaviour items |
| **Open questions** | Assumptions that need a decision before or during implementation |

### Rules

- No code is written until the micro spec exists and is committed.
- The spec is the source of truth. If code diverges, update the spec first
  and get acknowledgement before continuing.
- Specs must not contain jargon or implementation-specific code labels in
  requirement statements.
- Requirements are written as clear dot points, where each bullet expresses
  exactly one goal or requirement, comnplex/compound requirements are a sub list 
- Acceptance criteria drive the test plan — every criterion maps to at least
  one test. Missing test → missing criterion or vice versa.
- Interfaces in the spec are expressed in language-neutral pseudocode or
  plain English unless the project has an established language convention.

---

## SOLID Design Principles

This project applies all five SOLID principles:

| Abbr | Full name | One-line rule |
|---|---|---|
| **SRP** | Single Responsibility | One reason to change per module, class, or function |
| **OCP** | Open / Closed | Open for extension, closed for modification |
| **LSP** | Liskov Substitution | Subtypes must be drop-in replacements for their base type |
| **ISP** | Interface Segregation | Many narrow interfaces over one wide one |
| **DIP** | Dependency Inversion | Depend on abstractions, not concretions |

Call the principle out by abbreviation in code review comments and micro
specs (e.g. "this violates SRP — split the parse and persist steps").

### SRP — Single Responsibility

A module, class, or function has exactly one reason to change. When a unit
does two things — parsing *and* persisting, validating *and* formatting —
split it. The name of each unit should make its single responsibility
obvious without needing to read the body.

### OCP — Open / Closed

New behaviour is added by extending the system, not by editing existing
code. Achieve this through composition, strategy objects, and well-defined
extension points. A change request that requires editing an existing class
body is a signal the abstraction boundary is in the wrong place.

### LSP — Liskov Substitution

Every subtype must honour the contract of its base type. A caller that holds
a reference to the base type must observe identical behaviour regardless of
which concrete subtype it receives. Violations: a subtype that throws where
the parent does not, returns a narrower type, or silently ignores a method.

### ISP — Interface Segregation

No implementor should be forced to define methods it does not use. Split
wide interfaces into focused ones. Clients depend only on the slice of the
interface they actually call.

### DIP — Dependency Inversion

High-level policy modules do not import low-level detail modules. Both
depend on a shared abstraction. Concrete implementations are injected at
the composition root — never constructed inside business logic.

---

## Engineering Quality Standards

### Code style

- Follow the language's idiomatic style guide.
- Maximum function length: **30 lines** of logic (excluding blank lines and
  comments). If longer, extract a helper with a clear name.
- Maximum file length: **400 lines**. Larger files signal more than one
  concept in the file.
- No commented-out code committed. Use version control instead.

### Naming

- Names are pronounceable, unambiguous, and domain-specific.
- No abbreviations unless universally understood in the domain.
- Boolean names start with `is_`, `has_`, `can_`, or `should_`.

### Error handling

- Never silently swallow exceptions. Log and re-raise, or convert to a
  typed domain error with context.
- Every public function that can fail has a documented failure contract.
- Errors are typed — avoid bare catch-all error types.

### Dependencies

- No new dependency is added without a note in the micro spec justifying it.
- Prefer standard library over third-party where the effort is comparable.
- Pin all dependency versions; no floating version ranges in lock files.

### Git hygiene

- Commits are atomic: one logical change per commit.
- Commit message format:
  ```
  <type>: <imperative short summary>

  <body — why, not what; optional>
  ```
  Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`.
- No merge commits on feature branches; rebase onto main before merging.
- Branch names: `<type>/<short-slug>`.

### Pull / merge requests

- PR description links to the micro spec doc.
- Checklist before requesting review:
  - [ ] All acceptance criteria from the micro spec are met.
  - [ ] Coverage gate passes locally.
  - [ ] No new lint warnings introduced.
  - [ ] Micro spec updated if scope changed during implementation.
  - [ ] CHANGELOG entry added for user-visible changes.

---

