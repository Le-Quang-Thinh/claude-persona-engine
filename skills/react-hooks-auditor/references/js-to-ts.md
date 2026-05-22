# JS → TS Conversion Guide cho React Hooks

Đọc file này khi người dùng chọn **Option A** (convert rồi audit) hoặc **Option C** (chỉ convert).

---

## Chiến lược convert theo từng pattern

### 1. Function params không có type

```js
// JS
const useProgram = ({ setPlayingContent, overWriteDIDPlayer, setIsFavorite }) => { }

// TS — suy luận từ cách dùng trong body
interface UseProgramProps {
  setPlayingContent: (content: PlayingContentObject) => void
  overWriteDIDPlayer: (linkPlay: string) => void
  setIsFavorite: (val: boolean) => void
}
const useProgram = ({ setPlayingContent, overWriteDIDPlayer, setIsFavorite }: UseProgramProps) => { }
```

### 2. useState không có initial value rõ ràng

```js
// JS
const [selectedSeason, setSelectedSeason] = useState(null)

// TS — dùng generic + union với null
const [selectedSeason, setSelectedSeason] = useState<SelectedSeason | null>(null)
```

### 3. useRef không có type

```js
// JS
const currentRef = useRef({ defaultId: null, watchNowId: null })

// TS
interface CurrentRef {
  defaultId: string | null
  watchNowId: string | null
}
const currentRef = useRef<CurrentRef>({ defaultId: null, watchNowId: null })
```

### 4. Async function return type

```js
// JS
const fetchData = async (id) => {
  const res = await api.get(id)
  return res
}

// TS — suy luận từ cách return value được dùng
const fetchData = async (id: string): Promise<DataType | undefined> => {
  const res = await api.get(id)
  return res
}
```

### 5. Event handlers

```js
// JS
const handleClick = (e) => { e.preventDefault() }

// TS
const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => { e.preventDefault() }
```

### 6. Object destructuring phức tạp

```js
// JS
const { data: { seasons, view } = {} } = programDetail

// TS — giữ nguyên, thêm type cho programDetail
const { data: { seasons, view } = {} } = programDetail as ProgramDetail
```

---

## Rules khi convert

1. **Không dùng `any`** — nếu không suy luận được type, dùng `unknown` và ghi `// TODO: type this`
2. **Suy luận từ usage** — xem biến được dùng như thế nào trong code để suy ra shape
3. **Bắt đầu với `strict: false`** — convert xong mới tighten dần, tránh bị block bởi quá nhiều errors
4. **Giữ JSDoc nếu có** — JSDoc comments trong JS thường chứa type hints hữu ích
5. **Không rewrite logic** — chỉ thêm types, không đổi cách hoạt động

---

## Thứ tự convert trong 1 file hook

```
1. Interfaces/types (tạo file types/[name].types.ts)
2. Hook function signature
3. useState generics
4. useRef generics
5. useCallback/useMemo return types (nếu cần)
6. Async function return types
7. Internal variables còn lại
```

---

## Xử lý các trường hợp khó

### Lodash functions

```js
// JS
const index = _findIndex(arr, _ => _.id === id)

// TS — lodash đã có @types/lodash, không cần thêm gì
// Nếu arr chưa có type → type arr trước
const arr: Episode[] = []
const index = _findIndex(arr, (item: Episode) => item.id === id)
```

### Dynamic object keys

```js
// JS
obj[key] = value

// TS
type ObjKey = 'defaultId' | 'watchNowId'
obj[key as ObjKey] = value as string
```

### Third-party hooks không có types

```js
// JS
const { methodPlayer, isInPlayer } = useBackgroundPlayerContext()

// TS — tạo interface tạm, ghi TODO
interface BackgroundPlayerContext {
  methodPlayer: unknown  // TODO: type từ backgroundPlayerProvider
  isInPlayer: boolean
}
const { methodPlayer, isInPlayer } = useBackgroundPlayerContext() as BackgroundPlayerContext
```

---

## Output files khi convert

**Option A (convert + audit):**
```
hooks/[name].ts          ← file convert, sẽ tiếp tục được refactor
types/[name].types.ts    ← interfaces từ conversion
```

**Option C (chỉ convert):**
```
hooks/[name].ts          ← file convert hoàn chỉnh
types/[name].types.ts    ← tất cả interfaces
.claude/plan/refactor-[name].md ← ghi status: "Converted JS→TS, pending audit"
```
