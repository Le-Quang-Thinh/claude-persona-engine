# Stack Reference

## Project: vimai-smarttv

### Versions
| Package | Version | Notes |
|---------|---------|-------|
| react | 18.2.0 | uses createRoot |
| @testing-library/react | ^13.4.0 | RTL 13 — compatible with React 18 |
| @testing-library/user-event | ^14.5.2 | async userEvent API |
| @testing-library/jest-dom | ^5.16.5 | custom matchers |
| @types/jest | ^28.1.3 | |
| react-query | 3.16.0 | **NOT** @tanstack/react-query — stay on v3 (Chrome 38 constraint) |
| react-scripts | 5.0.1 | Jest built-in, no jest.config.ts needed |
| craco | ^7.1.0 | |

### Why stay on react-query v3
- v4 dropped old browser support — Chrome 38 (Smart TV) would break
- v5 uses ES private fields (`#field`) — cannot be polyfilled or transpiled for Chrome 38
- v3 is ES5-compatible, safe to keep

### Test runner
- **Jest** — built-in via `react-scripts`, no extra install needed
- **Do NOT use Vitest** — project uses Webpack, not Vite
- Run: `yarn test` or `yarn test [file]`

### Standard imports for test files
```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react'
import { renderHook, act } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import '@testing-library/jest-dom'
```

### RTL 13 notes (vs RTL 14)
- `renderHook` is now exported from `@testing-library/react` directly (no separate package)
- `userEvent` in v14 is async — always `await userEvent.click()`, `await userEvent.type()`
- `act()` wrapping is more automatic — RTL handles most cases internally

```typescript
// userEvent v14 — async API
const user = userEvent.setup()
await user.click(screen.getByRole('button'))
await user.type(screen.getByRole('textbox'), 'hello')
```

### Mock patterns by dependency

**Timer (setTimeout, setInterval):**
```typescript
beforeEach(() => jest.useFakeTimers())
afterEach(() => jest.useRealTimers())

// advance time
jest.advanceTimersByTime(500)
jest.runAllTimers()
```

**React Query v3 (import from 'react-query', NOT '@tanstack/react-query'):**
```typescript
import { QueryClient, QueryClientProvider } from 'react-query'

const createWrapper = () => {
  const queryClient = new QueryClient({
    defaultOptions: { queries: { retry: false } }
  })
  return ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  )
}
// usage: renderHook(() => useHook(), { wrapper: createWrapper() })
```

**Axios (v0.21.1):**
```typescript
import axios from 'axios'
jest.mock('axios')
const mockedAxios = axios as jest.Mocked<typeof axios>
mockedAxios.get.mockResolvedValue({ data: { id: 1, name: 'test' } })
```

**Redux (react-redux 7.2.4 + @reduxjs/toolkit 1.5.1):**
```typescript
import { Provider } from 'react-redux'
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: { featureSlice: reducer } })
const wrapper = ({ children }: { children: React.ReactNode }) => (
  <Provider store={store}>{children}</Provider>
)
```

**React Router v5:**
```typescript
import { MemoryRouter } from 'react-router-dom'
const wrapper = ({ children }: { children: React.ReactNode }) => (
  <MemoryRouter initialEntries={['/some-path']}>{children}</MemoryRouter>
)
```

**localStorage:**
```typescript
const localStorageMock = (() => {
  let store: Record<string, string> = {}
  return {
    getItem: jest.fn((key: string) => store[key] ?? null),
    setItem: jest.fn((key: string, val: string) => { store[key] = val }),
    removeItem: jest.fn((key: string) => { delete store[key] }),
    clear: jest.fn(() => { store = {} }),
  }
})()
Object.defineProperty(window, 'localStorage', { value: localStorageMock })
```

### Alias paths (check craco.config.js)
If the project has aliases like `@hooks`, `@components` — add to `package.json`:
```json
"jest": {
  "moduleNameMapper": {
    "^@hooks/(.*)$": "<rootDir>/src/hooks/$1",
    "^@components/(.*)$": "<rootDir>/src/components/$1"
  }
}
```
