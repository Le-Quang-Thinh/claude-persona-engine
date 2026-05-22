# React Hooks Rules & Common Anti-Patterns

## Core Rules (must never be violated)

1. **Only call hooks at the top level** — never inside loops, conditions, or nested functions
2. **Only call hooks from React functions** — not regular JS functions or class components
3. **useEffect dependencies must be complete** — all values used inside effect must be in deps array
4. **Never mutate state directly** — always use setter functions

## Common Hook Anti-Patterns to Flag

### Stale Closures
```typescript
// ❌ count captured at render time, never updates
useEffect(() => {
  const interval = setInterval(() => console.log(count), 1000);
  return () => clearInterval(interval);
}, []); // count missing from deps

// ✅ Fix: add count to deps, or use functional update
```

### Missing Cleanup
```typescript
// ❌ Memory leak — subscription never cleaned
useEffect(() => {
  const sub = someObservable.subscribe(handler);
}, []);

// ✅ Fix
useEffect(() => {
  const sub = someObservable.subscribe(handler);
  return () => sub.unsubscribe();
}, []);
```

### Unstable Object/Array in Deps
```typescript
// ❌ new object on every render → infinite loop
useEffect(() => {
  fetch(url, options);
}, [{ headers: { auth: token } }]); // object literal

// ✅ Fix: memoize or destructure primitives
const headers = useMemo(() => ({ auth: token }), [token]);
useEffect(() => { fetch(url, { headers }); }, [url, headers]);
```

### Unnecessary useCallback / useMemo
```typescript
// 🔵 Premature optimization — not passed as prop or used in deps
const handleClick = useCallback(() => {
  setCount(c => c + 1);
}, []); // no benefit if not in deps or passed to memo'd child
```

### useState vs useRef confusion
```typescript
// ❌ Using ref to track value that should trigger re-render
const count = useRef(0);
count.current++; // UI won't update

// ❌ Using state for value that doesn't affect UI
const [timerId, setTimerId] = useState(null); // use useRef instead
```

## React Query Specific

- `cacheTime` (v3) / `gcTime` (v4+) must be in milliseconds — `cacheTime: 5` = 5ms (likely bug, should be `5 * 60 * 1000`)
- `staleTime: 0` means always refetch on mount (often unintended)
- Never mutate query cache data directly — use `setQueryData` with immutable update
- `enabled` flag should depend on stable values, not object references

## Performance Checklist for Hooks

- [ ] `useCallback` wrapping functions passed to memoized children
- [ ] `useMemo` for expensive derivations
- [ ] `useRef` for values that don't need re-renders (timers, DOM refs, previous values)
- [ ] Effect deps are minimal and stable
- [ ] No derived state that could be computed from existing state
