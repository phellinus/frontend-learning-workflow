# React and Vue component organization

Use the repository's existing conventions when they differ. These are stable defaults for readable component code, not a formatting contest.

## Import order

Group imports with blank lines in this order:

1. Framework and third-party packages.
2. Shared project modules and aliases.
3. Nearby rendering components.
4. Local types.
5. Local context, constants, composables, helpers, and style utilities.

Within a group, keep the project convention (usually alphabetical or source-order). Use `import type` for type-only imports. Avoid mixing unrelated runtime and type imports just to reduce line count.

## React template

Keep module-level values that do not depend on a component render outside the component: namespace helpers, stable constants, static maps, and pure helpers.

Use the following import grouping at the top of a React component. Omit groups that have no imports; do not leave placeholder comments.

```tsx
// 1. Framework and third-party packages
import { useEffect, useRef, useState } from 'react';
import classNames from 'classnames';

// 2. Shared project modules and aliases
import { useTheme } from '@/theme/useTheme';

// 3. Nearby rendering components
import { MenuItem } from './MenuItem';

// 4. Local types
import type { ExampleProps } from './types';

// 5. Local context, constants, hooks, helpers, and styles
import { DEFAULT_KEY } from './constants';
import { useMenuKeyboard } from './useMenuKeyboard';
import styles from './Example.module.css';
```

Keep type-only imports in the local-types group with `import type`. If a project convention combines runtime and type imports from the same local module, retain that convention rather than splitting an existing import unnecessarily.

Inside a component, use this order:

```tsx
export function Example(props: ExampleProps) {
    // 1. Props: destructure and apply defaults
    const { disabled = false, items, onSelect } = props;

    // 2. State hooks: useState / useReducer
    const [selectedKey, setSelectedKey] = useState<string | null>(null);

    // 3. Shared hooks: context, refs, and custom hooks
    const menuRef = useRef<HTMLUListElement>(null);
    const { theme } = useTheme();

    // 4. Derived values; useMemo only for a demonstrated expensive computation
    const selectedItem = items.find((item) => item.key === selectedKey);

    // 5. Named event handlers and local actions
    const handleSelect = (key: string) => {
        if (disabled) return;
        setSelectedKey(key);
        onSelect?.(key);
    };

    // 6. Effects: synchronization with systems outside React
    useEffect(() => {
        // DOM subscription, timer, request, or external state synchronization
    }, []);

    // 7. View-only derivations kept close to JSX
    const classes = classNames('example', { 'is-disabled': disabled });

    // 8. JSX: describe structure; extract substantial recursion or branches
    return <ul ref={menuRef} className={classes}>{/* ... */}</ul>;
}
```

All hooks must be unconditional and appear before the return. A small component may omit sections; do not add placeholders. `useEffect` is for external synchronization, not ordinary values that can be derived during render. Keep class names and inline/CSS-variable style objects close to the JSX they serve.

## Vue 3 template (`<script setup lang="ts">`)

Place the SFC sections in this order unless the repository defines another convention: `<script setup>`, `<template>`, then `<style>`. Choose scoped/module/global styling based on the existing component system—do not add `scoped` by habit to a library that uses BEM/global classes.

Inside `<script setup>`, use this order:

```vue
<script setup lang="ts">
// 1. Imports: use the shared order above

// 2. Static constants and pure local helpers

// 3. Props, emits, models, slots, and injections
const props = withDefaults(defineProps<Props>(), { disabled: false });
const emit = defineEmits<{ select: [key: string] }>();

// 4. Composables and reactive state
const menuRef = ref<HTMLUListElement>();
const selectedKey = ref<string | null>(null);

// 5. Computed state
const selectedItem = computed(() =>
    props.items.find((item) => item.key === selectedKey.value),
);

// 6. Watches and lifecycle side effects
watch(selectedKey, (key) => {
    // synchronize only when needed
});

// 7. Named event handlers
function handleSelect(key: string) {
    if (props.disabled) return;
    selectedKey.value = key;
    emit('select', key);
}
</script>
```

Avoid `watch` when a `computed` value, template expression, or direct event handler is sufficient. Prefer `computed` for derived state and `watch`/lifecycle hooks for side effects. Keep template expressions small; name a computed property or handler when an expression begins to encode business logic.

## CSS property order: outside to inside

Order declarations by the part of the element they affect, from its placement in the page to its contents and behavior. This makes a selector easy to scan and keeps React CSS files, CSS Modules, and Vue `<style>` blocks consistent.

1. **Position and stacking**: `position`, `inset`/`top`/`right`/`bottom`/`left`, `z-index`.
2. **Layout**: `display`, `flex-*`, `grid-*`, alignment, `gap`, `order`.
3. **Box model**: `box-sizing`, width/height constraints, `margin`, `padding`, `overflow`.
4. **Appearance**: `visibility`, `opacity`, `background`, `border`, `border-radius`, `outline`, `box-shadow`, `filter`.
5. **Text and content**: `color`, font properties, `line-height`, text alignment/decoration, `white-space`, text overflow.
6. **Interaction and motion**: `cursor`, `user-select`, `pointer-events`, `transition`, `transform`, `animation`.

```css
.menu-item {
    /* 1. Position and stacking */
    position: relative;
    z-index: 0;

    /* 2. Layout */
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 8px;

    /* 3. Box model */
    box-sizing: border-box;
    width: 100%;
    min-height: 40px;
    margin: 0;
    padding: 0 12px;
    overflow: hidden;

    /* 4. Appearance */
    background: var(--menu-item-background);
    border: 1px solid transparent;
    border-radius: 6px;

    /* 5. Text and content */
    color: var(--menu-item-color);
    font-size: 14px;
    font-weight: 500;
    line-height: 1.4;
    text-align: left;
    white-space: nowrap;
    text-overflow: ellipsis;

    /* 6. Interaction and motion */
    cursor: pointer;
    transition: background-color 160ms ease, color 160ms ease;
}

.menu-item:hover {
    background: var(--menu-item-hover-background);
}

.menu-item:focus-visible {
    outline: 2px solid var(--focus-ring-color);
    outline-offset: 2px;
}
```

Keep each selector in this property order, including modifier classes, pseudo-classes, and responsive overrides. Group CSS custom properties with the declaration group they configure; put an element's `color` with text properties, not beside `background` merely because both are colors.

## Styles and tests

- Put static layout, color, transition, hover, responsive, and state selectors in the component style file/SFC style block, using the CSS property order above.
- Put values that need runtime configuration behind documented props and CSS custom properties, instead of generating large inline style blocks.
- Test observable behavior: rendered state, events, accessibility, controlled/uncontrolled behavior, and regressions. Do not test internal hook order or implementation details.
- Co-locate tests according to the repository convention. In work mode, add/update the smallest focused test that protects the requested behavior before relying only on broad test suites.
