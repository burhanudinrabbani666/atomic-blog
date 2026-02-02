# useMemo in Practice

## Overview

This guide demonstrates practical usage of `useMemo` to memoize objects and prevent unnecessary re-renders in child components.

## The Problem

Without `useMemo`, objects are recreated on every render:

```jsx
function Parent() {
  const [posts, setPosts] = useState([...]);

  // ❌ Problem: New object created on every render
  const archiveOptions = {
    show: false,
    title: `Post archive in addition to ${posts.length} main posts`,
  };

  return <Archive options={archiveOptions} />;
}

const Archive = memo(function Archive({ options }) {
  // This will re-render on every parent render
  // because 'options' is always a new object reference
});
```

**Why this fails:**

- Every render creates a new `archiveOptions` object
- Even though the content might be identical, the reference is different
- `memo` sees different references and triggers a re-render

---

## The Solution: useMemo

```jsx
const archiveOptions = useMemo(() => {
  return {
    show: false,
    title: `Post archive in addition to ${posts.length} main posts`,
  };
}, [posts.length]);
```

### How It Works

**Syntax Breakdown:**

```jsx
const memoizedValue = useMemo(() => {
  // Return the value you want to memoize
  return computedValue;
}, [dependencies]);
```

**In this example:**

1. **Factory Function:**

   ```jsx
   () => {
     return {
       show: false,
       title: `Post archive in addition to ${posts.length} main posts`,
     };
   };
   ```

   - This function creates and returns the object
   - Only runs when dependencies change

2. **Dependency Array:**

   ```jsx
   [posts.length];
   ```

   - React watches `posts.length` for changes
   - If `posts.length` changes → recalculate the object
   - If `posts.length` stays the same → return cached object

3. **Result:**
   - Same object reference is returned between re-renders
   - `memo` in child components now works correctly ✅

---

## Dependency Array Behavior

### When to Recalculate

The memoized value is recalculated only when dependencies change:

```jsx
// Recalculates only when posts.length changes
useMemo(() => ({ ... }), [posts.length])

// Recalculates when either value changes
useMemo(() => ({ ... }), [posts.length, filter])

// Never recalculates (created only once on mount)
useMemo(() => ({ ... }), [])

// ❌ Bad: Recalculates on every render (defeats the purpose)
useMemo(() => ({ ... }))  // Missing dependency array
```

### Empty Dependency Array

```jsx
const archiveOptions = useMemo(() => {
  return {
    show: false,
    title: "Fixed title",
  };
}, []); // Empty array
```

**Empty array `[]` means:**

- The factory function runs **only once** when the component mounts
- The same object reference is returned on every subsequent render
- Use this when the value doesn't depend on any props or state

---

## Complete Example

```jsx
function App() {
  const [posts, setPosts] = useState([...]);
  const [filter, setFilter] = useState('all');

  // Memoize the archive options object
  const archiveOptions = useMemo(() => {
    return {
      show: false,
      title: `Post archive in addition to ${posts.length} main posts`,
    };
  }, [posts.length]);

  return (
    <div>
      <PostList posts={posts} filter={filter} />
      <Archive options={archiveOptions} />
    </div>
  );
}

// Archive component wrapped with memo
const Archive = memo(function Archive({ options }) {
  console.log('Archive rendered');
  // This will only log when options actually changes

  return (
    <aside>
      <h2>{options.title}</h2>
      {/* ... */}
    </aside>
  );
});
```

### What Happens

| Scenario                    | posts.length | archiveOptions Reference        | Archive Re-renders? |
| --------------------------- | ------------ | ------------------------------- | ------------------- |
| Initial render              | 10           | `{ show: false, title: "..." }` | Yes (first render)  |
| User types (filter changes) | 10           | Same reference ✅               | No                  |
| User types again            | 10           | Same reference ✅               | No                  |
| Add new post                | 11           | New reference 🔄                | Yes                 |
| User types                  | 11           | Same reference ✅               | No                  |

---

## Best Practices

### ✅ Good Use Cases

```jsx
// 1. Objects passed as props to memoized components
const config = useMemo(
  () => ({
    theme: "dark",
    count: items.length,
  }),
  [items.length],
);

// 2. Expensive calculations
const sortedAndFiltered = useMemo(() => {
  return items.filter((item) => item.active).sort((a, b) => b.score - a.score);
}, [items]);

// 3. Dependencies for other hooks
const searchParams = useMemo(
  () => ({
    query: searchTerm,
    limit: 10,
  }),
  [searchTerm],
);

useEffect(() => {
  fetchResults(searchParams);
}, [searchParams]);
```

### ❌ When NOT to Use

```jsx
// Don't memoize primitive values
const count = useMemo(() => posts.length, [posts.length]); // ❌ Unnecessary

// Don't memoize if not used as a dependency or prop
const greeting = useMemo(() => `Hello ${name}`, [name]); // ❌ Just use a variable

// Don't memoize cheap calculations
const doubled = useMemo(() => count * 2, [count]); // ❌ Multiplication is cheap
```

---

## Common Pitfalls

### Pitfall 1: Forgetting Dependencies

```jsx
// ❌ Bug: title won't update when posts.length changes
const options = useMemo(
  () => ({
    title: `${posts.length} posts`,
  }),
  [],
); // Missing posts.length dependency
```

### Pitfall 2: Over-Memoization

```jsx
// ❌ Unnecessary: String concatenation is cheap
const title = useMemo(() => `Hello ${name}`, [name]);

// ✅ Better: Just use a regular variable
const title = `Hello ${name}`;
```

### Pitfall 3: Memoizing Everything

Not every value needs memoization. Only memoize when:

- Passing objects/arrays as props to memoized components
- Performing expensive calculations
- Using values in dependency arrays

---

## Key Takeaways

- `useMemo` preserves object/array references between renders
- Essential for making `memo` work with object/array props
- Dependency array controls when to recalculate
- Empty array `[]` means calculate only once
- Don't over-optimize—use only when there's a measurable benefit

---

## Next Steps

Learn how to memoize functions instead of values:

[Next: useCallback in Practice](./08-usecallback-in-practice.md)
