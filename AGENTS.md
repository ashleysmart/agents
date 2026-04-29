# Agent Guidelines

Rules and expectations for all AI agents working in this repository tree.
These guidelines apply to every project unless a project-level `Agents.md`
explicitly overrides a section.

## Agent Workflow

When an AI agent picks up a task it **must** follow this order:

1. **Read** the relevant micro spec (or create one if absent).
2. **Read** existing code in the affected area before writing anything.
3. **Write or update** the micro spec if the task is new or scope changes.
4. **Red** — write one test, run the suite, confirm that test fails for the
   right reason. A test that cannot be seen to fail proves nothing.
5. **Green** — write the minimum production code needed to make that test
   pass. No more.
6. **Repeat** steps 4–5 for each acceptance criterion in the micro spec.
7. **Refactor** — with all tests green, clean names, split large functions,
   remove duplication. Run the suite after every refactor step.
8. **Commit** in atomic commits following the git hygiene rules above.
9. **Do not deploy** unless the user explicitly says so.

An agent must stop and surface an open question rather than guess when:
- A spec section is ambiguous.
- An interface from another module is missing or contradicts the spec.
- The 97 % coverage target cannot be reached without unreasonable stubbing.

### Response style

- Keep responses short and to the point.
- Prefer direct answers over long explanations.
- Include only the detail needed for the current task unless the user asks for more.

---

## What Agents Must Never Do

### Scope creep — the hardest rule

An agent works only on what was explicitly asked for. When in doubt, do less
and ask. Violations erode trust and create hidden regressions.

- **Do not add features** that were not in the current task or micro spec,
  even if they seem obviously useful.
- **Do not refactor code** outside the files directly touched by the task.
- **Do not rename** symbols, files, or directories unless renaming is the
  explicit task.
- **Do not add logging, metrics, or instrumentation** beyond what the spec
  requires.
- **Do not add comments or documentation** to code that was not part of the
  change, even to "improve" it.
- **Do not change formatting** in lines that were not otherwise modified.

## Specs and Design

Read the guide at @guidelines/SPECS.md

##  Unit Test Standard

Read the guide at @guidelines/TESTING.md

