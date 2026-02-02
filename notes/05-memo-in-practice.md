# Memo in Practice

Reference:

- [React: memo](https://react.dev/reference/react/memo)

This section explains how `memo` works in real-world usage, why it helps performance, and what mental model you should keep in mind when using it. The code remains **unchanged**—only the explanation is improved for clarity and future readability.

## What is `memo`?

`memo` is a performance optimization tool in React.

When you wrap a component with `memo`, React will:

- Skip re-rendering that component
- **As long as its props have not changed**

Important reminder:

> Memoization is an optimization, not a guarantee.

React may still re-render a memoized component when it needs to.

## The Code Example

```jsx
const Archive = memo(function Archive({ show }) {
  // Here we don't need the setter function. We're only using state to store these posts because the callback function passed into useState (which generates the posts) is only called once, on the initial render. So we use this trick as an optimization technique, because if we just used a regular variable, these posts would be re-created on every render. We could also move the posts outside the components, but I wanted to show you this trick 😉
  const [posts] = useState(() =>
    // 💥 WARNING: This might make your computer slow! Try a smaller `length` first
    Array.from({ length: 30000 }, () => createRandomPost()),
  );

  const [showArchive, setShowArchive] = useState(show);

  return (
    <aside>
      <h2>Post archive</h2>
      <button onClick={() => setShowArchive((s) => !s)}>
        {showArchive ? "Hide archive posts" : "Show archive posts"}
      </button>

      {showArchive && (
        <ul>
          {posts.map((post, i) => (
            <li key={i}>
              <p>
                <strong>{post.title}:</strong> {post.body}
              </p>
            </li>
          ))}
        </ul>
      )}
    </aside>
  );
});
```

## Why This Component Is Expensive

This component is slow because:

- It creates **30,000 posts**
- Rendering the list is expensive
- Re-rendering it unnecessarily can freeze the UI

Without `memo`, every parent re-render would also re-render this component — even if nothing inside it actually changed.

## How `memo` Helps Here

Wrapping `Archive` with `memo` means:

- If the parent component re-renders
- And the `show` prop stays the same

➡️ React **reuses the previous render result** and skips re-rendering `Archive`.

This prevents:

- Re-running the render logic
- Re-mapping 30,000 items
- Unnecessary performance hits

## The `useState` Initialization Trick

This part is important:

```js
const [posts] = useState(() => Array.from(...))
```

Why not a normal variable?

- A regular variable would be recreated **on every render**
- `useState` with a function initializer runs **only once**, on the first render

This ensures:

- The expensive post generation happens once
- Even if the component re-renders later

This is an optimization technique you’ll see in real-world apps.

## Mental Model to Remember

> `memo` prevents re-renders caused by **unchanged props**, not by state updates inside the component.

That means:

- State changes **inside** `Archive` will still cause re-renders
- Only **parent-driven** re-renders are skipped

## When Should You Use `memo`?

Use `memo` when:

- A component is expensive to render
- It re-renders often because its parent re-renders
- Its props usually stay the same

Avoid using `memo` when:

- The component is cheap to render
- Props change frequently
- You haven’t measured performance issues yet

[Next: Understanding memo and useCallback](./06-understanding-memo-and.md)
