# frontend-learning-workflow

A reusable Codex skill for React and Vue learning, code organization, and approval-gated implementation.

这是一个面向 React 与 Vue 开发的 Codex Skill。它将“先讲解、再动手”的协作习惯固化为两种清晰的工作模式：学习时不修改源码；实施时先给出方案与文件清单，获得确认后再修改和验证。

## What it provides

- **学习模式**：基于实际代码讲解，并提供最小完整示例；不会修改源码、测试、配置或依赖。
- **工作模式**：先说明方案、影响范围与测试计划；只有在用户明确确认后才改动文件。
- **React 组织模板**：明确的导入分组、组件内 Hooks 的推荐排列、派生状态与副作用的边界。
- **Vue 组织模板**：`<script setup>` 的代码顺序、`computed` 与 `watch` 的使用边界，以及样式和测试原则。
- **CSS 组织规范**：从元素外部到内部排列样式属性：定位、布局、盒模型、外观、文本、交互与动画。

## Project structure

```text
frontend-learning-workflow/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   └── component-organization.md
├── LICENSE
└── README.md
```

## Installation

Copy this directory into your local Codex skills directory:

```text
~/.codex/skills/frontend-learning-workflow/
```

Keep the directory structure intact so that `SKILL.md` can load the component-organization reference.

## Usage

Use the skill name together with the mode you want.

### Learning mode

```text
$frontend-learning-workflow 学习模式：解释这个 React 组件，并给我代码，不要修改源码。
```

The agent inspects the relevant code read-only, explains the approach, gives a smallest complete example, and names the files and commands you could use to verify it yourself.

### Work mode

```text
$frontend-learning-workflow 工作模式：为这个 Vue 菜单增加多级展开能力。
```

The agent first provides the chosen approach, an exact list of files to add or modify, and the planned tests and commands. It waits for your explicit confirmation before editing. After confirmation, it makes only the approved changes and reports verification results.

## Principles

- Prefer the repository's existing conventions over generic templates.
- Keep React Hooks unconditional and before `return`.
- Group React imports by framework, shared modules, nearby components, local types, then local helpers and styles.
- Use `useEffect` and Vue `watch` for external synchronization or real side effects, not values that can be derived during rendering.
- Order CSS declarations from outside to inside: positioning, layout, box model, appearance, text, then interaction and motion.
- Avoid premature abstractions, unnecessary memoization, and implementation-detail tests.
- Prefer native semantic controls and accessible state where interaction requires it.

For the full React and Vue reference, see [component-organization.md](references/component-organization.md).

## License

Released under the [MIT License](LICENSE).
