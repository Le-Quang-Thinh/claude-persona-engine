# Common Anti-Patterns by Language

Reference file for `code-reviewer` skill.
Load this when reviewing Python, Go, or other non-React code.

---

## TypeScript / JavaScript (non-React)

### Type Safety
```typescript
// ❌ Suppress errors instead of fixing them
const value = someFunc() as any;
(window as any).myGlobal = true;

// ❌ Non-null assertion without guard
const len = user!.name!.length; // crashes if null

// ✅ Fix: proper narrowing
if (user?.name) {
  const len = user.name.length;
}
```

### Async Pitfalls
```typescript
// ❌ Floating promises (unhandled rejection)
async function save() { await db.write(); }
save(); // no await, no .catch()

// ❌ Sequential awaits that could be parallel
const a = await fetchA();
const b = await fetchB(); // waits for A unnecessarily

// ✅ Fix
const [a, b] = await Promise.all([fetchA(), fetchB()]);
```

### Error Handling
```typescript
// ❌ Swallowing errors silently
try {
  riskyOp();
} catch (_) {}

// ❌ Re-throwing without context
catch (e) { throw e; } // loses stack context

// ✅ Fix
catch (e) {
  logger.error('riskyOp failed', { error: e, context });
  throw new AppError('Operation failed', { cause: e });
}
```

---

## Python

### Mutable Default Arguments
```python
# ❌ Classic Python trap — shared across all calls
def append_item(item, lst=[]):
    lst.append(item)
    return lst

# ✅ Fix
def append_item(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

### Exception Handling
```python
# ❌ Catching too broadly
try:
    process()
except Exception:
    pass  # silent failure

# ❌ Using bare except (catches SystemExit, KeyboardInterrupt)
try:
    process()
except:
    pass

# ✅ Fix
try:
    process()
except ValueError as e:
    logger.warning("Invalid value: %s", e)
except IOError as e:
    raise ProcessingError("IO failed") from e
```

### Resource Management
```python
# ❌ File not closed on exception
f = open('file.txt')
data = f.read()
f.close()

# ✅ Fix
with open('file.txt') as f:
    data = f.read()
```

### Type Hints
```python
# ❌ Missing type hints in function signatures
def process(data, config):
    ...

# ✅ Fix
from typing import Optional
def process(data: list[dict], config: Optional[Config] = None) -> Result:
    ...
```

### Performance
```python
# ❌ String concatenation in loop (O(n²))
result = ""
for item in items:
    result += str(item)

# ✅ Fix
result = "".join(str(item) for item in items)

# ❌ Repeated membership test on list (O(n))
if item in large_list:  # use set instead

# ✅ Fix
large_set = set(large_list)
if item in large_set:  # O(1)
```

---

## Go

### Error Handling
```go
// ❌ Ignoring errors
result, _ := someFunc()

// ❌ Panic in library code
func GetUser(id int) User {
    user, err := db.Find(id)
    if err != nil {
        panic(err) // never panic in libs
    }
    return user
}

// ✅ Fix
func GetUser(id int) (User, error) {
    user, err := db.Find(id)
    if err != nil {
        return User{}, fmt.Errorf("GetUser(%d): %w", id, err)
    }
    return user, nil
}
```

### Goroutine Leaks
```go
// ❌ Goroutine with no exit condition
go func() {
    for {
        process()
    }
}()

// ✅ Fix: use context for cancellation
go func(ctx context.Context) {
    for {
        select {
        case <-ctx.Done():
            return
        default:
            process()
        }
    }
}(ctx)
```

### Interface Design
```go
// ❌ Large interfaces (hard to mock, violates ISP)
type Storage interface {
    Read(key string) ([]byte, error)
    Write(key string, value []byte) error
    Delete(key string) error
    List(prefix string) ([]string, error)
    Stats() StorageStats
    // ... 10 more methods
}

// ✅ Fix: small, focused interfaces
type Reader interface { Read(key string) ([]byte, error) }
type Writer interface { Write(key string, value []byte) error }
type ReadWriter interface { Reader; Writer }
```

---

## SQL (any language)

```sql
-- ❌ SQL injection via string concat (any language)
query = "SELECT * FROM users WHERE name = '" + name + "'"

-- ✅ Fix: parameterized queries
query = "SELECT * FROM users WHERE name = $1"
db.Query(query, name)
```

```sql
-- ❌ SELECT * in production code (fetches unnecessary columns, breaks on schema change)
SELECT * FROM orders WHERE user_id = $1

-- ✅ Fix
SELECT id, status, total, created_at FROM orders WHERE user_id = $1
```

```sql
-- ❌ Missing index on frequently queried column
SELECT * FROM events WHERE user_id = $1 AND created_at > $2
-- If no index on (user_id, created_at) → full table scan

-- ✅ Flag: suggest adding composite index
```

---

## General (any language)

### Magic Numbers
```typescript
// ❌
if (retries > 3) setTimeout(fn, 5000);

// ✅
const MAX_RETRIES = 3;
const RETRY_DELAY_MS = 5_000;
if (retries > MAX_RETRIES) setTimeout(fn, RETRY_DELAY_MS);
```

### Boolean Trap
```typescript
// ❌ Hard to read at call site
createUser(true, false, true);

// ✅ Fix: use options object
createUser({ isAdmin: true, sendEmail: false, isActive: true });
```

### Deep Nesting
```typescript
// ❌ Pyramid of doom
if (user) {
    if (user.profile) {
        if (user.profile.address) {
            // actual logic buried 3 levels deep
        }
    }
}

// ✅ Fix: early returns
if (!user) return;
if (!user.profile) return;
if (!user.profile.address) return;
// actual logic here
```
