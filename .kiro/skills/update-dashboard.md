---
name: update-dashboard
description: Cập nhật index.html dashboard trỏ đến file báo cáo ngày mới nhất cho mỗi module. Bao gồm archive listing và multi-language handling.
---

# Skill: Update Dashboard

Skill này thực thi sau mỗi scan session hoàn thành, cập nhật `index.html` để trỏ đến file mới nhất.

---

## Khi nào chạy

- Tự động: Step 7 của skill `scan-news`.
- Thủ công: khi user yêu cầu update dashboard.

---

## Cách update

### Sidebar links + Module cards
- Tìm tất cả `loadPage('...')` calls trong `index.html` — CẢ SIDEBAR LẪN MODULE CARDS.
- Thay path file thành ngày mới nhất có file `.html` tồn tại cho module đó.
- Cập nhật text ngày hiển thị trên card (`📅 YYYY-MM-DD`).
- **BẮT BUỘC:** Cập nhật ĐỒNG THỜI cả hai nơi:
  1. **Sidebar nav links** (trong `<div class="group-items">`) — link chính + sub-links archive
  2. **Module cards** (trong `<div class="modules">`) — onclick path + `<div class="date">` text
- Nếu chỉ update sidebar mà quên cards (hoặc ngược lại) = BUG. Phải update CẢ HAI.

### Logic xác định file mới nhất
- Scan `{module}/daily/` → tìm file `.html` có ngày lớn nhất (YYYY-MM-DD sort).
- Nếu module có cả file `-vi.html` và `.html` (ví dụ AI News), sidebar trỏ đến file chính (`.html` = English hoặc default), thêm link phụ cho `-vi.html` nếu tồn tại.

### Không hardcode
- KHÔNG hardcode ngày cụ thể vào skill. Luôn scan filesystem để xác định.
- Nếu không tìm thấy file `.html` nào → giữ nguyên link cũ, ghi warning.

---

## Archive listing (tùy chọn, khuyến khích)

Nếu có > 1 file daily cho một module, thêm dropdown hoặc sub-links trong sidebar cho 7 ngày gần nhất. Format:

```
AI News
  ├── 2026-05-13 (latest)
  ├── 2026-05-12
  └── 2026-05-11
```

Nếu chỉ có 1 file → không cần archive list.

---

## HTML integrity

- Giữ nguyên toàn bộ CSS, layout, responsive behavior.
- Chỉ thay đổi: paths trong `loadPage()`, text ngày, archive links.
- KHÔNG refactor HTML/CSS/JS trừ khi user yêu cầu.
- Validate: sau update, mọi link phải trỏ đến file thực sự tồn tại.

---

## Multi-language (AI News)

AI News mặc định output tiếng Việt (giống các module khác):
- `YYYY-MM-DD.md` / `.html` — **Tiếng Việt** (default, dùng cho dashboard)
- `YYYY-MM-DD-en.md` / `-en.html` — English (chỉ khi user yêu cầu)

Dashboard sidebar:
- Link chính trỏ đến file tiếng Việt (`.html`)
- Nếu có bản English → thêm indicator nhỏ `[EN]` link đến bản English

World Conflicts và VN News: tiếng Việt duy nhất → 1 link.
