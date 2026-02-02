# Understanding Memo

Memoization: optimization technique that executes a pure function once, and saves the reults in memory. if we try to execute the function again with the same arguments as before, the perviously saved result will be retured, without executing the function again.

- Memoize components with memo
- Memoize Objects with memo
- Memoize function with useCallback

## The memo Function

- Used to create a components that will **not re-render when its parent to re-renders**, as long as the **prop stay the same between renders**
- Only effect props!A memorizes components will still re-renderes when its own state changes or when a context that is subcribed to changes
- Only make sense when the components is heavly (slow rendering), re-renders often, and does so with the same props

[Next: Usememo in practice](./07-usememo-in-practice.md)
