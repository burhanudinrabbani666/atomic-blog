# THE PROFILER DEVELOPER TOOLS

React Developer Tools Profiler is a tool in React DevTools that helps developers measure and analyze the performance of React components. It shows which components render, how often they render, and how long each render takes, so you can find performance bottlenecks.

**Main tools inside the Profiler (brief & clear):**

- **Record (Profiling)**
  Starts and stops performance recording of component renders.

- **Flamegraph**
  Visualizes component render times in a tree structure. Bigger blocks = slower components.

- **Ranked Chart**
  Lists components sorted by render duration (slowest first).

- **Interactions**
  Shows what user actions (clicks, navigation, etc.) triggered renders.

- **Commit Information**
  Displays why a component re-rendered (props change, state change, parent re-render).

**In short:**

The React Profiler helps you identify slow components and unnecessary re-renders so you can optimize your app’s performance.

[Next: Optimization trick](./03-optimization-trick.md)
