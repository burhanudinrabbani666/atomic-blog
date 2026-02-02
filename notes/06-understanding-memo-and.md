# Understanding useMemo and useCallback

## The Problem with memo

While `memo` prevents unnecessary re-renders, there's a critical issue that can break its optimization:

### Why memo Sometimes Fails

**1. Everything Re-creates on Every Render**

- In React, all objects and functions are **recreated on every render**
- This is fundamental to how React works

**2. JavaScript Object Comparison**

- In JavaScript, two objects or functions that look identical are actually **different**
- Example: `{} !== {}` and `function() {} !== function() {}`
- Each render creates a **new reference** in memory

**3. Impact on Child Components**

- When objects or functions are passed as props, child components see them as **new props on each re-render**
- Even if the values inside are the same, the reference is different

**4. memo Becomes Ineffective**

- `memo` compares props by reference (shallow comparison)
- If props have different references, `memo` thinks they've changed
- Result: **The child component re-renders anyway** ❌

### Visual Example

```
Parent renders → creates new object {} → passes to child
Child sees: newObject !== previousObject → re-renders (even with memo)

Parent renders again → creates another new object {} → passes to child
Child sees: newObject !== previousObject → re-renders (even with memo)
```

## The Solution: Memoization

To make `memo` work effectively, we need to **preserve the same reference** between renders:

**Before memoization:**

```javascript
{} !== {}  // Different references
```

**After memoization:**

```javascript
memoized {} === memoized {}  // Same reference! ✅
```

This is where `useMemo` and `useCallback` come in.

---

## useMemo and useCallback

Two hooks that preserve values and functions between renders:

| Hook          | Purpose                                                 | Returns               |
| ------------- | ------------------------------------------------------- | --------------------- |
| `useMemo`     | Memoizes **values** (objects, arrays, computed results) | The memoized value    |
| `useCallback` | Memoizes **functions**                                  | The memoized function |

### How They Work

**Core Concept:**

- Values/functions are stored in memory ("cached")
- React returns the **same cached value** in subsequent re-renders
- The cache only updates when **dependencies change**

**Syntax:**

```javascript
// Memoize a value
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// Memoize a function
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

### Dependency Array

Both hooks use a **dependency array** (similar to `useEffect`):

- **Dependencies stay the same** → Returns the cached value/function ✅
- **Any dependency changes** → Recalculates and caches the new value/function 🔄

```javascript
useMemo(() => expensiveCalculation(a, b), [a, b]);
//                                          ^^^^^^
//                                    Dependency array
```

---

## Three Primary Use Cases

### 1. Preventing Wasted Renders (with memo)

**Problem:** Props cause unnecessary re-renders

```javascript
function Parent() {
  const handleClick = () => {}; // ❌ New function every render
  return <Child onClick={handleClick} />;
}
```

**Solution:** Memoize the prop

```javascript
function Parent() {
  const handleClick = useCallback(() => {}, []); // ✅ Same function reference
  return <Child onClick={handleClick} />;
}

const Child = memo(function Child({ onClick }) {
  // Now memo works! Won't re-render if onClick stays the same
});
```

### 2. Avoiding Expensive Recalculations

**Problem:** Heavy computation runs on every render

```javascript
function Component({ items }) {
  const sortedItems = items.sort().filter(...);  // ❌ Runs every render
  // ...
}
```

**Solution:** Memoize the expensive calculation

```javascript
function Component({ items }) {
  const sortedItems = useMemo(() => {
    return items.sort().filter(...);  // ✅ Only recalculates when items change
  }, [items]);
  // ...
}
```

### 3. Stabilizing Dependencies for Other Hooks

**Problem:** Objects/functions in dependency arrays cause infinite loops

```javascript
function Component() {
  const options = { sort: "asc" }; // ❌ New object every render

  useEffect(() => {
    fetchData(options);
  }, [options]); // ❌ Runs every render! Infinite loop risk
}
```

**Solution:** Memoize the dependency

```javascript
function Component() {
  const options = useMemo(() => ({ sort: "asc" }), []); // ✅ Stable reference

  useEffect(() => {
    fetchData(options);
  }, [options]); // ✅ Only runs when options actually change
}
```

---

## Key Takeaways

✅ **When to use:**

- Passing objects/functions as props to memoized components
- Expensive calculations that don't need to run every render
- Values used in dependency arrays of other hooks

⚠️ **Remember:**

- `useMemo` and `useCallback` themselves have a cost
- Only use when you have a measurable performance problem
- Not every function or value needs memoization

---

## Next Steps

See these concepts in action:

[Next: useMemo in Practice](./07-usememo-in-practice.md)
