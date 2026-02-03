# useCallback in Practice

## Overview

`useCallback` is a React Hook that caches a function definition between re-renders, preventing unnecessary re-creations of the function.

## Basic Usage

```jsx
// Memoize a function to maintain the same reference across re-renders
const handleAddPost = useCallback(function handleAddPost(post) {
  setPosts((posts) => [post, ...posts]);
}, []);
```

**Key points:**

- The function is only recreated if dependencies in the dependency array change
- Empty dependency array `[]` means the function is created once and reused on every re-render

## Working with State Setters

State setter functions from `useState` are stable references that don't change between re-renders:

```jsx
const [isFakeDark, setIsFakeDark] = useState(false);

<Archive
  archiveFunction={archiveFunction}
  onAddPost={handleAddPost}
  setIsFakeDark={setIsFakeDark}
/>;
```

**Why this matters:**

- `setIsFakeDark` doesn't cause child components to re-render unnecessarily
- State setters from `useState` are guaranteed to be stable, so you don't need to wrap them in `useCallback`

## When to Use useCallback

Use `useCallback` when:

- Passing functions as props to memoized child components
- Functions are dependencies in other hooks (like `useEffect`)
- You want to prevent unnecessary re-renders of child components

---

[Next: Optimizing context](./09-optimizing-context.md)
