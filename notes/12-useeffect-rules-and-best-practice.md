# useEffect Rules and Best Practices

## Dependency Array Rules

### What Must Be Included

- **Every state variable and prop** used inside the effect must be included in the dependency array
- **All "reactive values"** must be included—this means any function or variable that references any other reactive value
- **Dependencies choose themselves**: Never ignore the `exhaustive-deps` ESLint rule
- **Do NOT use objects or arrays** as dependencies directly (objects are recreated on each render, and React sees new objects as different: `{} !== {}`)

## Removing Unnecessary Dependencies

### 🤖 Removing Function Dependencies

1. **Move the function into the effect** if it's only used there
2. **Memoize with `useCallback`** if you need the function in multiple places
3. **Move it outside the component** if the function doesn't reference any reactive values

### 📥 Removing Object Dependencies

1. **Include only specific properties** you need (primitive values) instead of the entire object
2. **Apply function strategies** if that doesn't work (moving or memoizing the object)

### 🎯 Other Strategies

- **Use `useReducer`** if you have multiple related reactive values as dependencies
- **No need to include** `setState` (from `useState`) or `dispatch` (from `useReducer`) in dependencies—React guarantees them to be stable across renders

## When NOT to Use an Effect

> Effects should be used as a **last resort**, when no other solution makes sense. React calls them an "escape hatch" to step outside of React's normal flow.

### Three Cases Where Effects Are Overused

1. **Responding to user events**
   - ❌ Don't use `useEffect`
   - ✅ Use an event handler function instead

2. **Fetching data on component mount**
   - ⚠️ Acceptable in small apps or learning projects
   - ✅ In production apps, use a library like **React Query** or **SWR**

3. **Synchronizing state changes with one another**
   - ❌ Don't set state based on another state variable in an effect
   - ✅ Use **derived state** or **event handlers** instead

## Quick Reference

```jsx
// ❌ Bad: Object as dependency
useEffect(() => {
  // ...
}, [config]); // Object recreated every render

// ✅ Good: Primitive values as dependencies
useEffect(() => {
  // ...
}, [config.url, config.method]); // Only specific properties

// ❌ Bad: Responding to user event
useEffect(() => {
  if (clicked) doSomething();
}, [clicked]);

// ✅ Good: Use event handler
function handleClick() {
  doSomething();
}
```

---

[Next: Setting state based on other state](./13-setting-state-based-on-other-state.md)
