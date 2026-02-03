# Dont optimize prematury

**Do**

- Find performance bottlenecks using the Profiler and visual insepection(laggy UI)
- Fix those real performce issues
- Memoize expensive calculations
- Memoize expensive re-renders
- Optimize context if its has many consumers and changes often
- Memoize context value + child components
- implement code spliting + Lazy Loading for SPA routes

**Dont**

- Dont optimize prematurely!
- Dont optimize anything if there is nothing to optimize
- Dont warp all components in memo()
- Dont warp all function in useCallback()
- Dont optimize context if its not slow and doesnt have many consumers

[Next: Useeffect rules and best practice](./12-useeffect-rules-and-best-practice.md)
