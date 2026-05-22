# Vercel React Best Practices — 15 Tiêu Chí

Nguồn: https://vercel.com/blog/introducing-react-best-practices

---

## Nhóm A: Critical (phải pass)

### 1. Single Responsibility
Mỗi custom hook làm đúng 1 việc rõ ràng.
- **FAIL**: Hook tên `useFetchApi` nhưng mix cả query lẫn mutation lẫn cleanup
- **PASS**: `useProgramQuery` chỉ query, `useProgramMutation` chỉ mutation
- **Dấu hiệu vi phạm**: hook name cần chữ "and", hook có >3 useQuery/useMutation, return object >10 keys

### 2. React Query config
`staleTime`, `cacheTime`, `enabled`, `retry` phải được config đúng.
- **FAIL**: `cacheTime: 5` (= 5ms), `enabled: true` khi data chưa ready
- **PASS**: `cacheTime: 300_000`, `staleTime: 30_000`, `enabled: !!requiredData`
- **Common bugs**: cacheTime/staleTime = 0 hoặc số quá nhỏ, enabled không gate đúng dependencies

### 3. useCallback justified
Chỉ dùng `useCallback` khi:
  (a) function truyền xuống child có `React.memo()`, HOẶC
  (b) function là dependency của useEffect/useMemo khác
- **FAIL**: wrap useCallback "cho chắc" mà không có lý do trên
- **WARN**: function được truyền xuống nhưng child không memo
- Với React 19+/React Compiler: useCallback sẽ tự động → đừng over-optimize

### 4. useMemo justified
Chỉ dùng `useMemo` khi computation >1ms HOẶC cần referential equality.
- **PASS**: `useMemo` cho array transformation phức tạp dùng `produce()`
- **FAIL**: `useMemo(() => a + b, [a, b])` — quá đơn giản, overhead > benefit

### 5. useRef đúng mục đích
`useRef` cho: DOM refs, interval/timeout IDs, previous values, mutable values không cần re-render.
- **FAIL**: dùng useRef để store state mà lẽ ra nên trigger re-render
- **WARN**: dùng ref.current làm "escape hatch" để tránh re-render khi re-render là cần thiết

### 6. Dependency arrays đầy đủ
Không bao giờ lie về deps trong useEffect/useCallback/useMemo.
- **FAIL**: deps thiếu → stale closure, bug khó tái hiện
- **FAIL**: deps thừa → re-run không cần thiết
- Cài ESLint `react-hooks/exhaustive-deps` và không disable

### 7. Effect cleanup
Mọi useEffect với subscription, interval, event listener, hoặc async call có thể race phải có cleanup.
- **FAIL**: useEffect với async call không có AbortController
- **WARN**: cleanup function có nhưng dùng stale closure (không capture stable refs)
- Pattern đúng: capture ref trước return cleanup

---

## Nhóm B: High (nên pass)

### 8. No duplicate state
Server data chỉ managed bởi React Query. Không copy data từ query vào useState.
- **FAIL**: `const [data, setData] = useState()` rồi `useEffect(() => setData(queryData), [queryData])`
- **FAIL**: cùng 1 field được set ở 2 useEffect độc lập
- **PASS**: React Query là single source of truth, component đọc trực tiếp từ query

### 9. TypeScript strict
Không dùng `any`. Hooks generic-typed. Return types rõ ràng.
- **FAIL**: params hook là `any`, return type không khai báo
- **PASS**: `useProgram({ ... }: UseProgramProps): UseProgramReturn`
- Dùng `unknown` thay `any` khi thực sự không biết type

### 10. Error handling
Errors được catch, typed, và xử lý — không chỉ `console.log`.
- **FAIL**: `catch(e) { console.log(e) }` rồi tiếp tục như không có gì
- **PASS**: return typed error, hiển thị error state cho user, có fallback
- Với async mutation: check `isError`, trả về `undefined` với kiểu rõ ràng

---

## Nhóm C: Medium (nên cân nhắc)

### 11. State colocation
State nằm ở component/hook gần nhất cần nó.
- **FAIL**: lift state lên quá cao, nhiều components không cần nhưng vẫn nhận
- **PASS**: state chỉ lift khi thực sự cần share giữa siblings

### 12. Parallel fetching
Các data independent phải fetch song song, không sequential.
- **FAIL**: fetch A xong rồi mới fetch B dù B không phụ thuộc A
- **PASS**: `Promise.all([fetchA(), fetchB()])` hoặc 2 useQuery độc lập

### 13. Composability
Hooks nhận ít params, không tightly coupled.
- **WARN**: hook nhận >5 params riêng lẻ → nên gom thành object hoặc tách nhỏ hơn
- **PASS**: hook compose được — A gọi B gọi C, mỗi cái độc lập test được

---

## Nhóm D: Low (nice to have)

### 14. Return interface
Hooks trả về named object khi >2 values, không array tuple.
- **PASS**: `return { data, isLoading, error, refetch }`
- **WARN**: `return [data, loading, error, refetch]` — khó destructure đúng thứ tự

### 15. Data validation
API response được validate trước khi dùng.
- **FAIL**: `_merge(first, second)` trực tiếp không check shape
- **PASS**: Zod schema validate response, hoặc ít nhất check required fields
- Ở mức tối thiểu: optional chaining + nullish coalescing thay vì assume shape

---

## Scoring

```
PASS  = 10 điểm
WARN  = 5 điểm  
FAIL  = 0 điểm

Score = sum(điểm) / 150 * 100

90-100: Excellent
70-89:  Good
50-69:  Needs work
<50:    Critical issues
```
