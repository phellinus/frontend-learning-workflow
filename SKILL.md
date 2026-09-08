---
name: frontend-learning-workflow
description: Guide React or Vue work in a learning mode (explain and provide code without editing) or an approval-gated work mode (plan, file list, confirm, implement, and test). Use when the user asks for these learning/work conventions or for frontend code organization templates.
---

# Frontend Learning Workflow

Use this skill to preserve the user's preferred boundary between learning and implementation. It applies to React and Vue source, styles, component tests, and frontend documentation closely related to a requested change.

## Choose the mode

Honor an explicitly named mode first.

- **Learning mode**: phrases such as "学习模式", "给我讲解和代码即可", "不要直接修改源码", or a request to explain/review/teach select this mode.
- **Work mode**: phrases such as "工作模式", "直接改", "帮我实现", or a request to modify files select this mode. Work mode is still approval-gated; a request to implement is not the approval to edit.
- If the mode is not explicit and the user merely wants an explanation or code example, use learning mode. If they want a repository change, use work mode and ask for approval after presenting the plan. Do not silently change files.

## Learning mode

Inspect the relevant code read-only, then teach from the actual implementation.

Present the explanation in the same order a developer would build the feature. Adapt the number of steps to the task, but preserve this progression whenever the steps apply:

1. **Understand the starting point and goal**: explain the observed code, the requested behavior, and the smallest scope of change.
2. **Design the interface and data flow**: identify props, emits/callbacks, state, derived values, and the boundary with parent/shared state before writing implementation code.
3. **Build the component logic**: give incremental snippets in implementation order—imports, types/constants, state or composables, derived values, handlers, then effects only when external synchronization is needed.
4. **Build the view**: add the JSX or template that consumes the logic, keeping conditions and accessibility semantics understandable.
5. **Add styles**: show the relevant CSS or style-block snippet, ordering declarations from position and layout through box model, appearance, text, and interaction.
6. **Check behavior**: cover important edge cases, accessibility, and the smallest useful test or verification command.

For every step, include:

- The purpose of the step and the reason for the design choice.
- A smallest code snippet that builds on the preceding step; include imports or changed signatures when relevant.
- A concrete check the learner can make before moving on.

Label snippets as examples or patch-shaped guidance. Do not present an unrelated final code dump before the steps. Explain ordering, dependencies, types, reactivity, accessibility, and test decisions where they arise rather than mechanically describing every line.

Do not edit source, tests, configuration, dependencies, snapshots, or generated files. Distinguish a fact observed in the repository from a suggested convention, and name the files and commands the learner could use to verify the change themselves.

## Work mode

Before changing any file, inspect the repository and give a concise proposal containing:

1. The diagnosis or desired outcome and the chosen approach, including meaningful alternatives or trade-offs.
2. An exact list of files to modify and files to add, with each file's purpose.
3. The tests to add or update beside the changed component or feature, and the commands to run.

Ask for a clear confirmation after that proposal. Do not edit anything until the user confirms the listed plan. If they alter the scope, refresh the proposal and confirmation.

After confirmation:

1. Make only the approved changes.
2. Add or update focused tests in the affected feature's established test location. If the repository has no applicable test setup, explain that rather than inventing one.
3. Run the focused tests and relevant formatting, lint, type, or build checks in proportion to the change. Avoid commands that modify unrelated snapshots or generated files.
4. Report files changed, tests added, commands run, and any remaining unrelated failures.

## Code organization

When recommending or writing React/Vue component code, read [component-organization.md](references/component-organization.md). Follow its import grouping and CSS property order as defaults, while preserving a repository's established linting, import aliases, naming, or styling conventions when they differ.

## Boundaries

- Keep static visual rules in the component's style system; use props/CSS variables only for values that are genuinely runtime-configurable.
- Do not add `useMemo`, `useCallback`, watchers, or abstraction layers merely to make code look advanced. Add them only for a clear dependency, performance, or reuse need.
- Keep presentation components focused on rendering and local interaction; keep shared state/data orchestration in the appropriate parent, composable, store, or context.
- Follow accessibility semantics in examples: prefer native controls for actions and expose state with appropriate ARIA attributes when it matters.
