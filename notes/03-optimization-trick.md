# A Surprise Optimization Trick with Children

This section explains a subtle but powerful React optimization pattern involving **children** and **component re-renders**. The code examples stay exactly the same, but the explanation below is rewritten to be clearer and easier to understand when you revisit it in the future.

## The Question

Why does **example 1** feel faster than **example 2**, even though both render the same `SlowComponent`?

## Example 1

```jsx
function Counter({ children }) {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Increase: {count}</button>
      {children}
    </div>
  );
}

export default function Test() {
  return (
    <div>
      <h1>Slow counter?!?</h1>
      <Counter>
        <SlowComponent />
      </Counter>
    </div>
  );
}
```

## Example 2

```jsx
export default function Test() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <h1>Slow counter?!?</h1>
      <button onClick={() => setCount((c) => c + 1)}>Increase: {count}</button>
      <SlowComponent />
    </div>
  );
}
```

## What’s the Key Difference?

The difference is **where the state lives** and **how React treats children**.

### In Example 1

- The `count` state lives **inside** the `Counter` component.
- `SlowComponent` is passed as `children`.
- When `count` updates, **only `Counter` re-renders**.
- The `children` (`<SlowComponent />`) are **already created by the parent** (`Test`).
- React does **not re-render `SlowComponent`**, because it hasn’t changed.

✅ Result: Clicking the button feels fast.

### In Example 2

- The `count` state lives in the `Test` component.
- `SlowComponent` is rendered **directly inside `Test`**.
- Every time `count` changes, **the entire `Test` component re-renders**.
- That means `SlowComponent` re-renders **every time**, even though it doesn’t depend on `count`.

❌ Result: Clicking the button feels slow.

## Why This Works (Important Concept)

React treats `children` as **opaque values**.

If:

- The parent component does not re-render, and
- The `children` reference does not change

Then React will **reuse the previous children** without re-rendering them.

This means you can sometimes improve performance **without `memo`**, simply by:

- Moving state down
- Lifting expensive components up
- Passing slow components as `children`

## Mental Model to Remember

> "State updates only re-render the component that owns the state and its descendants — not siblings or already-created children."

This trick works best when:

- A component is slow
- It does not depend on state
- You can safely pass it as `children`

## What’s Next?

This optimization has limits. When props change, or when children depend on state, this trick alone is not enough.

➡️ **Next: Understanding `memo`**

[Next: Understanding memo](./04-understanding-memo.md)
