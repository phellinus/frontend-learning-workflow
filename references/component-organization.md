# React and Vue component organization

Use the repository's existing conventions when they differ. These are stable defaults for readable component code, not a formatting contest.

## Shared import order

Group imports with blank lines in this order:

1. Framework and third-party packages.
2. Shared project modules and aliases.
3. Nearby rendering components.
4. Local types.
5. Local context, constants, composables, helpers, and style utilities.

Within a group, keep the project convention (usually alphabetical or source-order). Use `import type` for type-only imports. Avoid mixing unrelated runtime and type imports just to reduce line count.

## React template

Keep module-level values that do not depend on a component render outside the component: namespace helpers, stable constants, static maps, and pure helpers.

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

## Styles and tests

- Put static layout, color, transition, hover, responsive, and state selectors in the component style file/SFC style block.
- Put values that need runtime configuration behind documented props and CSS custom properties, instead of generating large inline style blocks.
- Test observable behavior: rendered state, events, accessibility, controlled/uncontrolled behavior, and regressions. Do not test internal hook order or implementation details.
- Co-locate tests according to the repository convention. In work mode, add/update the smallest focused test that protects the requested behavior before relying only on broad test suites.
