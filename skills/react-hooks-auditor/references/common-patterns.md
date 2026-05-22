# Common React Hook Patterns — Nhận dạng và đánh giá

## Patterns cần NHẬN DẠNG khi scan

### 1. Stale Closure trong cleanup
```ts
// ❌ Bug: removeQuery bị capture theo giá trị lúc mount
useEffect(() => {
  return () => {
    removeQuery(...)  // stale closure
  }
}, [])

// ✅ Fix: capture stable ref HOẶC thêm vào deps
useEffect(() => {
  const _remove = removeQuery
  return () => { _remove(...) }
}, [removeQuery])
```

### 2. _merge mutate object gốc
```ts
// ❌ Bug: mutate object từ React Query cache
const res = _merge(first, { view: second })

// ✅ Fix: luôn merge vào empty object
const res = _merge({}, first, { view: second })
```

### 3. Unstable function reference
```ts
// ❌ Bug: tạo function mới mỗi render, gây re-render loop
const setRef = (key, value) => { ref.current[key] = value }

// ✅ Fix: useCallback với empty deps (ref stable)
const setRef = useCallback((key, value) => {
  ref.current[key] = value
}, [])
```

### 4. React Query enabled quá sớm
```ts
// ❌ Bug: query chạy khi data chưa có
useQuery(['key', data?.id], () => fetch(data.id), { enabled: true })

// ✅ Fix: gate bằng required dependency
useQuery(['key', data?.id], () => fetch(data.id), { enabled: !!data?.id })
```

### 5. Magic string literal lặp lại
```ts
// ❌ Smell: string literal dùng nhiều nơi, typo không bị catch
setRef('recallApiWatchNow', 'callApi')

// ✅ Fix: typed constant
const RECALL_API = { CALL: 'callApi', DEFAULT: 'default' } as const
type RecallApiStatus = typeof RECALL_API[keyof typeof RECALL_API]
```

### 6. Duplicate state source
```ts
// ❌ Bug: 2 nơi set cùng field → race condition
useEffect(() => { setInfo({ title: programDetail.title }) }, [programDetail])
// ... trong hàm khác:
setInfo(prev => ({ ...prev, title: seasonDetail.title }))

// ✅ Fix: tách rõ base info vs override info, hoặc dùng useReducer
```

### 7. Any trong useLocation state
```ts
// ❌ Common mistake
const { state }: any = useLocation()

// ✅ Fix: generic type parameter
interface LocationState { target?: string; directPlayContent?: string }
const { state } = useLocation<LocationState>()
```

### 8. Return any từ async function
```ts
// ❌
const getData = async (id: string): Promise<any> => { ... }

// ✅
type GetDataResult = MovieData | ShowData | undefined
const getData = async (id: string): Promise<GetDataResult> => { ... }
```

---

## Anti-patterns phổ biến trong React Query

| Anti-pattern | Fix |
|---|---|
| `cacheTime: 0` hoặc số nhỏ | `cacheTime: 300_000` (5 phút) |
| `enabled: true` unconditional | `enabled: !!requiredParam` |
| Không có `staleTime` | Thêm `staleTime: 30_000` |
| queryKey flat string | queryKey array có structure: `['resource', id, filters]` |
| Không dùng `select` option | Dùng `select` để transform/filter data |
| Copy query data vào useState | Đọc trực tiếp từ `data` của useQuery |

---

## Nhận dạng hook type

| Pattern trong code | Hook type | Vấn đề thường gặp |
|---|---|---|
| `useQuery + useMutation cùng file` | Data hook | SRP violation |
| `useState + useEffect phức tạp` | State machine | Nên dùng useReducer |
| `useRef + update trong callback` | Mutable ref | Stable reference issues |
| `useCallback wrap toàn bộ` | Over-optimized | Xem xét bỏ bớt |
| `multiple useEffect` | Side-effect heavy | Xem xét tách hook |
