---
name: react-hooks-auditor
description: >
  Audit, review, và refactor React custom hooks viết bằng TypeScript HOẶC JavaScript. Tự động:
  (1) detect ngôn ngữ — nếu là JS thì hỏi option convert sang TS trước khi làm gì khác;
  (2) tạo PLAN trước khi làm bất cứ điều gì — liệt kê toàn bộ any cần fix, risks cần review,
  files sẽ được tạo/sửa; (3) replace tất cả `any` bằng interfaces phù hợp và append vào
  types/[name].ts có sẵn; (4) đánh giá risks theo Vercel React Best Practices checklist
  15 tiêu chí; (5) xuất file refactored + audit report.

  Trigger skill này BẤT CỨ KHI NÀO người dùng:
  - upload file .ts/.tsx/.js/.jsx chứa custom hooks và hỏi "review", "audit", "optimize", "refactor"
  - nói "tìm any", "replace any", "fix type", "kiểm tra hook", "convert sang typescript"
  - nói "review best practices", "check vercel", "tìm risk", "tìm bug trong hook"
  - paste code React hooks dù không nói gì thêm — nếu code có custom hooks thì trigger
---

# React Hooks Auditor

Skill này làm việc theo **các phase bắt buộc theo thứ tự**. Không bao giờ bỏ qua Phase 0 và Phase 1.

---

## Phase 0 — DETECT LANGUAGE (chạy đầu tiên, trước mọi thứ)

Xác định ngôn ngữ của file input dựa trên extension hoặc nội dung:

| Tín hiệu | Ngôn ngữ |
|----------|----------|
| Extension `.ts` / `.tsx` | TypeScript → **bỏ qua Phase 0, đi thẳng Phase 1** |
| Extension `.js` / `.jsx` | JavaScript → **dừng lại, hỏi option** |
| Không có extension, nội dung không có type annotation | JavaScript → **dừng lại, hỏi option** |
| Nội dung có `: string`, `interface`, `<T>`, `as Type` | TypeScript → **đi thẳng Phase 1** |

### Nếu file là JavaScript

Hiển thị thông báo và **hỏi 1 trong 3 option** trước khi làm bất cứ điều gì khác:

```
⚠️  Phát hiện file JavaScript: [tên file]

Skill này hoạt động tốt nhất với TypeScript. Bạn muốn:

  [A] Convert sang TypeScript trước, rồi audit
      → Output: [name].ts + types/[name].types.ts
      → Thời gian: lâu hơn, kết quả đầy đủ nhất

  [B] Audit JS như bình thường, không convert
      → Output: [name].js đã refactor (không có types)
      → Một số tiêu chí TypeScript sẽ bị skip

  [C] Chỉ convert sang TypeScript, không audit
      → Output: [name].ts với types cơ bản
      → Nhanh nhất, có thể audit sau

Reply A / B / C để tiếp tục.
```

Chờ người dùng chọn. **Không tự ý tiến hành** khi chưa có lựa chọn.

### Xử lý theo option

**Option A — Convert rồi audit:**
1. Chạy JS→TS conversion (xem `references/js-to-ts.md`)
2. Ghi file `[name].ts` tạm thời
3. Tiếp tục **Phase 1** với file TS vừa convert
4. Trong file plan ghi rõ: `Language: JS → TS (converted)`
5. Output cuối bao gồm cả file `.ts` đã convert + refactored

**Option B — Audit JS:**
1. Tiếp tục **Phase 1** với file JS gốc
2. Trong bảng Any Detection: bỏ cột "Interface đề xuất", thay bằng "JSDoc đề xuất"
3. Trong Risk Scan: đánh dấu `[TS-only]` cho các tiêu chí không áp dụng được (tiêu chí 9)
4. Output: file JS refactored với JSDoc comments thay vì TypeScript types
5. Trong file plan ghi rõ: `Language: JS (no conversion)`

**Option C — Chỉ convert:**
1. Chạy JS→TS conversion (xem `references/js-to-ts.md`)
2. Ghi file `[name].ts`
3. Tạo `types/[name].types.ts` với interfaces cơ bản
4. Thông báo hoàn thành, **không chạy audit**
5. Gợi ý: "Chạy lại audit trên file `.ts` vừa tạo nếu muốn review đầy đủ"

---

## Phase 1 — BUILD PLAN (bắt buộc, làm trước tiên)

Trước khi viết bất kỳ dòng code hay fix nào, **luôn luôn build plan** và hiển thị cho người dùng xem. Plan gồm 3 phần:

### 1A. File Map
Parse file input, liệt kê:
- Tên các custom hooks tìm thấy
- React APIs được dùng (useState, useEffect, useCallback, useMemo, useRef, useQuery, useMutation...)
- External dependencies (lodash, immer, react-router, react-query...)
- File types đang import từ (để biết sẽ append interface vào đâu)

### 1B. Any Detection Table
Scan toàn bộ `any` trong file, tạo bảng:

| # | Vị trí (hook/biến) | Dòng code gốc | Interface đề xuất | Target file |
|---|---------------------|---------------|-------------------|-------------|
| 1 | useProgram params | `}: any)` | `UseProgramProps` | types/program.types.ts |
| 2 | useLocation state | `}: any = useLocation()` | `LocationState` | types/program.types.ts |
| ... | | | | |

Rules khi điền bảng:
- Nếu file types tương ứng đã có sẵn (detect qua import statements) → ghi "append vào [file]"
- Nếu chưa có → ghi "tạo mới types/[domain].types.ts"
- Đặt tên interface theo PascalCase, phản ánh đúng domain (không dùng tên generic như `Props1`)

### 1C. Risk Scan Table
Chạy 15 tiêu chí Vercel checklist (xem references/vercel-checklist.md), tạo bảng:

| # | Tiêu chí | Mức độ | Dự đoán | Lý do ngắn gọn |
|---|----------|--------|---------|----------------|
| 1 | Single responsibility | critical | ⚠️ warn | useFetchApi mix query+mutation |
| 2 | React Query config | critical | 🔴 fail | cacheTime: 5 = 5ms |
| ... | | | | |

### 1D. Xuất file plan

Sau khi build xong 3 phần trên, **luôn luôn ghi plan ra file** trước khi hỏi confirm:

```
.claude/plans/refactor-[tên-file-không-có-extension].md
```

Ví dụ: file input là `useProgram.ts` → ghi vào `.claude/plans/refactor-useProgram.md`

Nếu thư mục `.claude/plans/` chưa tồn tại → tạo tự động, không hỏi.

Nội dung file plan theo format chuẩn trong `references/output-format.md#plan-file`.

Sau khi ghi file xong, thông báo:
> `✅ Plan đã được lưu tại .claude/plans/refactor-[tên-file].md`

Rồi mới hỏi người dùng:
> "Plan trên đã đúng chưa? Bạn muốn mình bắt đầu từ Phase nào, hay chạy toàn bộ?"

Chỉ tiếp tục Phase 2+ sau khi người dùng confirm (hoặc nói "làm hết đi", "ok", "đúng rồi").

---

## Phase 2 — REPLACE ANY → INTERFACES

Với mỗi row trong bảng Any Detection (Phase 1B):

1. **Generate interface** — suy luận shape từ cách biến được dùng trong code, không đoán mò
2. **Check target file** — nếu file types đã tồn tại, append; nếu chưa, tạo mới
3. **Update import** trong file gốc — thêm import cho interface mới
4. **Replace** `any` bằng interface tương ứng

Một số rules quan trọng:
- Dùng `unknown` thay vì `any` cho fields thực sự không biết shape
- Dùng generic (`T extends object`) thay vì `any` cho utility functions
- Với union types, dùng `TypeA | TypeB` rõ ràng thay vì `any`
- Không tạo interface quá rộng (`[key: string]: unknown`) trừ khi thực sự cần
- Magic strings lặp lại → extract thành `const enum` hoặc `as const` object

Output Phase 2:
- File `types/[name].types.ts` đã được append/tạo mới
- File hook gốc đã replace toàn bộ `any`

---

## Phase 3 — RISK AUDIT (Vercel Best Practices)

Đọc references/vercel-checklist.md để lấy đầy đủ 15 tiêu chí.

Với mỗi tiêu chí, đánh giá:
- **✅ PASS** — code tuân thủ đúng
- **⚠️ WARN** — có vấn đề nhưng không gây bug ngay, nên fix
- **🔴 FAIL** — bug thực sự hoặc pattern nguy hiểm, phải fix

Output Phase 3 là bảng kết quả đầy đủ (xem format trong references/output-format.md).

Sau bảng, tính **Quality Score**:
```
score = (PASS * 10 + WARN * 5) / (total * 10) * 100
```

Và liệt kê **Top 3 quick wins** — những fix dễ nhất mang lại điểm cao nhất.

---

## Phase 4 — REFACTOR & OUTPUT FILES

Chỉ chạy Phase 4 sau khi người dùng confirm muốn refactor.

Tạo các files:
1. `.claude/plans/refactor-[name].md` — plan đã được tạo ở Phase 1, **cập nhật thêm** kết quả audit thực tế
2. `types/[name].types.ts` — interfaces mới (nếu chưa tạo ở Phase 2)
3. `hooks/[name].ts` — file hook đã refactor, comment `// ✅ FIX #N: [mô tả]` tại mỗi chỗ sửa

Trong file refactored, mỗi thay đổi phải có comment giải thích:
```ts
// ✅ FIX #4: cacheTime: 5 = 5ms (bug) → 300_000 (5 phút)
cacheTime: 300_000,
```

---

## Quy tắc chung

- **Không bao giờ bỏ qua Phase 0** — luôn detect ngôn ngữ trước, hỏi option nếu là JS
- **Không bao giờ bỏ qua Phase 1** — dù người dùng nói "làm nhanh thôi" hay "fix hết đi"
- **Không tự ý refactor** mà không có plan được confirm
- **Giữ nguyên logic business** — chỉ fix type safety và React patterns, không rewrite logic
- **Không xóa code** trừ khi code đó là duplicate rõ ràng
- Nếu không chắc về shape của interface → comment `// TODO: verify shape with backend`
- Nếu file types chưa tồn tại trong project → tạo mới, đặt tên theo domain của hook
- Với JS Option B: dùng JSDoc (`/** @param {string} id */`) thay vì TypeScript types

---

## References

Đọc các file sau khi cần:
- `references/vercel-checklist.md` — 15 tiêu chí đánh giá đầy đủ với ví dụ
- `references/output-format.md` — format chuẩn cho bảng plan, checklist, report
- `references/common-patterns.md` — các React hook patterns phổ biến cần nhận dạng
- `references/js-to-ts.md` — hướng dẫn convert JS → TS cho React hooks (đọc khi Option A hoặc C)