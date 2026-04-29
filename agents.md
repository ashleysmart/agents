# agents.md

## Purpose

This document defines the required engineering behavior for Python coding agents working in this repository.

The goal is disciplined, predictable, testable delivery. Agents must follow explicit requirements, avoid speculative implementation, and produce maintainable Python code with high verification standards.

---

## Core Operating Rules

### 1. No unauthorized merges
- Never merge to any protected or shared branch without explicit human permission.
- Never assume approval from prior discussion, implied intent, or silence.
- If a task reaches merge readiness, stop and request approval.

### 2. No improvisation
- Do not invent requirements, features, behaviors, APIs, workflows, or edge-case rules.
- Work from the microspec only.
- If any requirement is unclear, incomplete, conflicting, or underspecified, ask questions before implementing.
- Do not fill gaps with “reasonable assumptions” unless the microspec explicitly allows assumptions.

### 3. Stay on spec
- Implement only what is requested.
- Do not add bonus features, abstractions, refactors, optimizations, or extensions unless explicitly required.
- Do not widen scope during implementation.
- Treat out-of-spec work as a defect, even if it appears useful.

### 4. Follow solid engineering principles
- Prefer simple, composable, loosely coupled designs.
- Keep responsibilities separated.
- Depend on abstractions where appropriate, but do not over-engineer.
- Favor clarity, determinism, and maintainability over cleverness.
- Minimize shared mutable state.
- Make side effects explicit.
- Design for testability from the start.

---

## Python Code Standards

### 5. Small functions
- Functions must be kept small and focused.
- Each function should do one thing well.
- Split functions that mix parsing, validation, orchestration, I/O, and transformation.
- Prefer early returns and straightforward control flow over deeply nested logic.
- A function that is hard to name clearly is usually doing too much.

### 6. One class per file
- Limit files to one top-level class.
- Supporting helpers may exist in the file if they are small and directly tied to that class.
- If multiple substantial classes are needed, split them into separate files.
- If no class is needed, prefer plain functions over unnecessary class wrappers.

### 7. Style quality
- Follow consistent Python style across the repository.
- Use clear names, explicit types where useful, and readable control flow.
- Prefer standard library solutions unless an external dependency is justified by the spec.
- Avoid hidden behavior, magic constants, and surprising side effects.
- Keep imports clean and ordered.
- Remove dead code, commented-out code, and leftover debug statements.

### 8. Documentation in code
- Public modules, classes, and functions should have concise docstrings.
- Docstrings should describe intent, inputs, outputs, and important constraints.
- Comments should explain why, not restate what the code obviously does.

---

## Testing and Quality Gates

### 9. Red-Green-Refactor TDD
Agents must use red-green-refactor development:

#### Red
- Start by writing or updating a failing test that captures the requirement.
- The test must fail for the right reason.

#### Green
- Implement the minimum code needed to make the test pass.
- Do not overbuild beyond the tested requirement.

#### Refactor
- Improve structure while keeping behavior unchanged.
- Re-run the test suite after each refactor step.

### 10. Testing expectations
- Every behavior introduced or changed must be covered by tests.
- Tests must be deterministic, isolated, and readable.
- Prefer fast unit tests.
- Add integration tests only where boundaries or wiring matter.
- Test both expected behavior and important failure paths.
- Include regression tests for bugs once discovered.

### 11. Coverage requirement
- Maintain **97% minimum code coverage per file**.
- Coverage is enforced per file, not only at project level.
- Uncovered lines must be justified and minimized.
- If a file cannot realistically meet the threshold, raise it explicitly rather than hiding the gap.

### 12. Linting and static quality
- Code must pass repository linting and formatting checks before review.
- Use consistent formatting tools.
- Use static analysis where configured.
- Treat lint failures as real issues, not cosmetic noise.
- Do not suppress warnings without a documented reason.

---

## Requirement Handling

### 13. Microspec-first workflow
Before writing code, the agent must:
1. Read the microspec carefully.
2. Identify exact required behavior.
3. Identify missing information or ambiguities.
4. Ask clarifying questions before implementation if anything is unclear.
5. Confirm boundaries of the task.
6. Implement only after requirements are sufficiently clear.

### 14. Clarification triggers
The agent must stop and ask questions when:
- Inputs or outputs are not precisely defined.
- Error handling requirements are unclear.
- Performance expectations are unspecified but relevant.
- Data model constraints are ambiguous.
- External interfaces are underspecified.
- The requested change conflicts with existing code or prior requirements.

### 15. Change discipline
- Make the smallest correct change that satisfies the microspec.
- Do not mix unrelated cleanup with feature or bug-fix work.
- Separate refactor, behavior change, and formatting changes where possible.
- Preserve backward compatibility unless the microspec explicitly allows breaking changes.

---

## Review Readiness

### 16. Before requesting review
The agent must ensure:
- The implementation matches the microspec.
- No out-of-scope behavior was added.
- Tests were written first or updated in TDD sequence.
- All tests pass.
- Lint and formatting checks pass.
- Coverage is at least 97% for each touched file.
- The diff is focused and understandable.
- Merge has **not** been performed without permission.

### 17. Review notes
When handing off work, include:
- What changed.
- Which microspec requirements were implemented.
- What tests were added or updated.
- Any open questions, constraints, or known follow-ups.
- Any areas where clarification is still required.

---

## Prohibited Behavior

Agents must not:
- Merge without permission.
- Invent requirements.
- Drift from the microspec.
- Add speculative abstractions.
- Make silent breaking changes.
- Hide failing tests, lint errors, or coverage gaps.
- Use broad exception handling without justification.
- Leave partial implementations disguised as complete work.
- Ignore ambiguity and “just code something.”

---

## Default Engineering Preference Order

When tradeoffs appear, prefer:
1. Correctness
2. Spec compliance
3. Testability
4. Simplicity
5. Readability
6. Maintainability
7. Performance optimization only when required by spec

---

## Agent Summary

A Python agent in this repository must behave like a disciplined engineer:
- ask when unclear,
- follow the microspec,
- avoid improvisation,
- write small functions,
- keep one class per file,
- use solid design principles,
- practice red-green-refactor TDD,
- maintain strong linting and testing hygiene,
- achieve 97% coverage per file,
- and never merge without explicit permission.
