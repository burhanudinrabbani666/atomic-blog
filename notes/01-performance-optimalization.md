# PERFORMANCE OPTIMALIZATION AND WESTED RENDERS

## PERFORMANCE OPTIMIZATION TOOLS

### Prevent wated renders

- Memo
- UseMemo
- UseCallback
- Passing element as children or reguler prop

### Improve apps speed/ responsiveness

- UseMemo
- UseCallback
- UseTransition

### Reduce bundle size

- Using fewwer 3rd-party packages
- Code splitting and lzay loading

## WHEN DOES A COMPONENTS INSTANCE RE-RENDERS

**A componets insteance oly gets rerenderes in 3 different situations:**

- 🧠 State change
- 🌐 Context Change
- 👪 Parent Re-renders

**Remember:** a render does **not mean that the DOM actually gets updated**. ist just means the components function gets calles. but this can be an expensive operation

- **Wasted render:** a render that didnt produce any change in the DOM
- Only a problem when they happen **too frequenly** ot when the **components is very slow**

[Next: The profiler developer tools](./02-the-profiler-developer-tools.md)
