# Test Rules

## Writing test cases

### 1. Test case naming
- Use technical English, describe behavior — NOT implementation details
- Format: `[subject] [action] [expected result]`
- Good: `"setRefTimeout calls callback after specified delay"`
- Bad: `"test setTimeout works"`, `"test 1"`

### 2. Test structure — AAA pattern
```typescript
it('description', () => {
  // Arrange — prepare data and mocks
  const callback = jest.fn()

  // Act — perform the action
  act(() => result.current.setRefTimeout(callback, 500))
  act(() => jest.advanceTimersByTime(500))

  // Assert — verify the result
  expect(callback).toHaveBeenCalledTimes(1)
})
```

### 3. Hook testing
- Always use `renderHook` + `act`
- Never test `ref.current` directly — test through the return value
- Wrap state changes inside `act()`

```typescript
const { result } = renderHook(() => useHook(props))
act(() => result.current.someAction())
expect(result.current.someValue).toBe(expected)
```

### 4. Component testing
- Test what the USER sees — not internal implementation
- Prefer `getByRole`, `getByText`, `getByLabelText` — avoid `getByTestId`
- Use `userEvent` over `fireEvent` for interactions (more realistic)

```typescript
render(<Component />)
expect(screen.getByRole('button', { name: /submit/i })).toBeInTheDocument()

const user = userEvent.setup()
await user.click(screen.getByRole('button'))
expect(screen.getByText('Success')).toBeVisible()
```

### 5. Async testing
```typescript
// waitFor — wait until condition is true
await waitFor(() => expect(screen.getByText('Loaded')).toBeInTheDocument())

// findBy — shorthand for waitFor + getBy
const el = await screen.findByText('Loaded')
```

### 6. What to mock
| Should mock | Reason |
|-------------|--------|
| API calls (axios, fetch) | Remove network dependency |
| Timers (setTimeout, setInterval, Date) | Don't wait for real time |
| localStorage, sessionStorage | Prevent state sharing between tests |
| Random values (Math.random, uuid) | Make results predictable |
| console.error | Avoid noise in test output |
| External modules (react-query, redux) | Isolate the unit under test |

**Do NOT mock:**
- The pure logic of the hook/func being tested
- React core APIs (useState, useEffect, useCallback)
- Pure utility functions (format, validate, calculate)

### 7. Cleanup
```typescript
afterEach(() => {
  jest.clearAllMocks()   // reset call counts between tests
  jest.useRealTimers()   // restore real timers if fake timers were used
})
```

### 8. Suppressing expected errors
```typescript
// Suppress console.error for expected throws
const consoleSpy = jest.spyOn(console, 'error').mockImplementation(() => {})
// ... code that throws
consoleSpy.mockRestore()
```

### 9. React 18 + RTL 13 specifics
- `renderHook` is exported directly from `@testing-library/react` — no separate package
- `userEvent` v14 is fully async — always `await user.click()`, `await user.type()`
- Automatic batching: multiple `setState` calls in one event = one re-render
- RTL 13 handles `act()` wrapping automatically for most async cases

### 10. Coverage targets
| Type | Target |
|------|--------|
| Pure utility functions | 100% |
| Custom hooks | >= 80% |
| UI components | >= 70% (happy path + error state) |
| Skip testing | constants, TypeScript types/interfaces, `.scss` files |
