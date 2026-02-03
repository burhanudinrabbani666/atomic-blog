# Optimizing Context

## Overview

When providing context values to child components, React will re-render all consumers whenever the context value changes. To prevent unnecessary re-renders, we can memoize the context value using `useMemo`.

## Problem

Without memoization, a new object is created on every render, causing all context consumers to re-render even when the actual data hasn't changed.

## Solution

Use `useMemo` to cache the context value object:

```jsx
const value = useMemo(() => {
  return {
    posts: searchedPosts,
    onAddPost: handleAddPost,
    onClearPosts: handleClearPosts,
    searchQuery,
    setSearchQuery,
  };
}, [searchedPosts, searchQuery]);

return (
  // Provide memoized value to child components
  <PostContext.Provider value={value}>{children}</PostContext.Provider>
);
```

## Key Points

- **Dependency array**: Only include values that actually change (`searchedPosts`, `searchQuery`)
- **Stable references**: Functions like `handleAddPost` and state setters like `setSearchQuery` don't need to be in dependencies if they're memoized or from `useState`
- **Result**: Context consumers only re-render when `searchedPosts` or `searchQuery` actually change

## Best Practices

- Always memoize context values that contain objects or arrays
- Include all non-stable values in the dependency array
- Combine with `useCallback` for function props in the context value

---

[Next: Optimizing bundle size](./10-optimizing-bundle-size.md)
