# Output Format Chuẩn

---

## Plan File — `.claude/plans/refactor-[name].md` {#plan-file}

File được tạo **tự động cuối Phase 1**, trước khi hỏi confirm. Dùng làm nguồn sự thật duy nhất
cho toàn bộ audit session. Cập nhật status khi Phase 4 hoàn thành.

```markdown
# Refactor Plan — [tên file gốc]
> Generated: [ISO timestamp]
> Status: PENDING | COMPLETED

---

## File Map

- **Hooks:** [list tên hooks]
- **React APIs:** [list]
- **External deps:** [list]
- **Type imports:** [list files]

---

## Any Detection

| # | Hook/Biến | Code gốc | Interface đề xuất | Target file | Action |
|---|-----------|----------|-------------------|-------------|--------|
| 1 | useProgram params | `}: any)` | UseProgramProps | types/program.types.ts | append |
| 2 | stateLocation | `}: any = useLocation()` | LocationState | types/program.types.ts | append |

**Tổng:** N vị trí · N interfaces mới · [list files sẽ thay đổi]

---

## Risk Scan

| # | Tiêu chí | Nhóm | Dự đoán | Lý do |
|---|----------|------|---------|-------|
| 1 | Single responsibility | Critical | ⚠️ WARN | [lý do] |
| 2 | React Query config | Critical | 🔴 FAIL | [lý do] |
...

---

## Summary

| Metric | Value |
|--------|-------|
| `any` cần fix | N |
| Interfaces mới | N |
| PASS / WARN / FAIL | N / N / N |
| Estimated score | **N/100** |
| Files sẽ thay đổi | [list] |

### Top 3 Quick Wins
1. [fix] — +Npts, [độ khó]
2. [fix] — +Npts, [độ khó]
3. [fix] — +Npts, [độ khó]

---

## Phases

- [ ] Phase 2 — Replace `any` → interfaces
- [ ] Phase 3 — Risk audit chi tiết
- [ ] Phase 4 — Refactor + export files

---
> Reply "all" hoặc "phase2" / "phase3" / "phase4" để bắt đầu
```

Khi Phase 4 hoàn thành, **cập nhật file plan** (không tạo file mới):
- `Status: PENDING` → `Status: COMPLETED`
- Check các phase đã xong: `- [x] Phase 2`
- Append section mới:

```markdown
## Kết quả thực tế

| Metric | Trước | Sau |
|--------|-------|-----|
| Score | 54/100 | 78/100 |
| any count | 6 | 0 |
| FAIL | 5 | 1 |
| WARN | 5 | 3 |

### Files đã thay đổi
- `types/program.types.ts` — thêm 5 interfaces
- `hooks/useProgram.ts` — 10 fixes, xem comment ✅ FIX #N
```

---

## Phase 1 Output

### File Map
```
📦 [filename]
├── Hooks: [list tên hooks]
├── React APIs: [list]
├── External deps: [list]
└── Type imports from: [list files]
```

### Any Detection Table
```markdown
| # | Hook/Biến | Code gốc (rút gọn) | Interface đề xuất | Target file | Action |
|---|-----------|---------------------|-------------------|-------------|--------|
| 1 | useProgram params | `}: any)` | UseProgramProps | types/program.types.ts | append |
| 2 | stateLocation | `}: any = useLocation()` | LocationState | types/program.types.ts | append |
```

### Risk Scan Table
```markdown
| # | Tiêu chí | Nhóm | Dự đoán | Lý do |
|---|----------|------|---------|-------|
| 1 | Single responsibility | Critical | ⚠️ warn | useFetchApi mix query+mutation |
| 2 | React Query config | Critical | 🔴 fail | cacheTime:5 = 5ms |
```

### Plan Summary
```
📋 PLAN SUMMARY
─────────────────────────────────
any cần fix:        6 vị trí
Interfaces mới:     5 interfaces
Files sẽ thay đổi: types/program.types.ts (append), hooks/useProgram.ts (refactor)
Risk issues:        5 FAIL · 5 WARN · 5 PASS
Estimated score:    54/100

Phase tiếp theo:
  [ ] Phase 2 — Replace any → interfaces
  [ ] Phase 3 — Risk audit chi tiết
  [ ] Phase 4 — Refactor + export files
─────────────────────────────────
Bắt đầu từ đâu? (all / phase2 / phase3 / phase4)
```

---

## Phase 3 Output — Full Checklist

```markdown
## Audit Results — Vercel Best Practices

| # | Tiêu chí | Nhóm | Kết quả | Chi tiết |
|---|----------|------|---------|----------|
| 1 | Single responsibility | Critical | ⚠️ WARN | useFetchApi có 3 useMutation + 2 useQuery + cleanup |
| 2 | React Query config | Critical | 🔴 FAIL | cacheTime:5(5ms), enabled:true sớm |
...

### Score: 54/100 — Needs work

### Top 3 Quick Wins
1. 🔴→✅ Fix cacheTime: 5 → 300_000 (+10pts, 1 dòng code)
2. 🔴→✅ enabled: !!programDetail (+10pts, 1 dòng code)  
3. 🔴→⚠️ Wrap setCurrentSelectedRef useCallback (+5pts, ~5 dòng)
Potential after quick wins: 74/100
```

---

## Phase 4 Output — Refactored Files

Trong file hook refactored, mỗi fix có comment:
```ts
// ✅ FIX #2 [React Query config]: cacheTime: 5 = 5ms → 300_000ms (5 phút)
cacheTime: 300_000,
staleTime: 30_000,
```

AUDIT_REPORT.md structure:
```markdown
# Audit Report — [filename]
Generated: [date]

## Summary
## Phase 1: Plan
## Phase 2: Any → Interfaces (danh sách thay đổi)
## Phase 3: Risk Checklist (full table)
## Phase 4: Refactor Changes (diff summary)
## Files Changed
```