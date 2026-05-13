---
inclusion: auto
---

# Memory Rules — Persistence & Integrity

Áp dụng cho mọi file trong `{module}/memory/`. Mục tiêu: không mất state, không corrupt, không phình vô hạn.

---

## File schema

### `last_scan.json`
Required fields:
- `last_scan_timestamp` (ISO 8601 với timezone, ví dụ `2026-05-13T15:03:36+07:00`)
- `last_scan_date` (YYYY-MM-DD, local timezone)
- `run_count` (integer, số lần quét trong `last_scan_date`)
- `stories_found` (integer)

Optional:
- `top_story` (string, dùng cho continuity)
- `summary` (string ngắn, ≤ 500 chars)
- `next_scan_priority` (array of string)
- `conflicts_tracked` / `entities_tracked` (module-specific)

### `processed_links.json`
```json
{
  "processed_urls": ["...", "..."]
}
```
URL phải là full URL (protocol + host + path). Không lưu query params trừ khi chúng phân biệt nội dung.

### `processed_links_YYYY-MM.json` (archive, xem rotation)
Same shape. Read-only sau khi tạo.

---

## Atomic write

Mọi write vào memory PHẢI qua pattern tmp → rename:

1. Write content vào `{file}.tmp`.
2. Validate JSON parse được + required fields đủ.
3. Rename `{file}.tmp` → `{file}` (atomic trên cùng filesystem).

Nếu validate fail → xoá `.tmp`, giữ file gốc, log error.
KHÔNG bao giờ write trực tiếp vào `last_scan.json` hoặc `processed_links.json`.

---

## Rotation & retention

### `processed_links.json`
- Cap ở 60 ngày gần nhất hoặc 5,000 URL (whichever larger).
- Cuối mỗi tháng (hoặc khi file > 5,000 URL), tách:
  - URLs từ tháng M-2 trở về trước → ghi vào `processed_links_YYYY-MM.json` (archive).
  - `processed_links.json` chỉ giữ URLs từ tháng M-1 + M hiện tại.
- Khi dedup, check cả file chính + archive gần nhất (1 tháng trước).

### `last_scan.json`
- Không rotate. File này luôn chỉ chứa state của lần quét gần nhất.
- Lịch sử scan được implicit lưu qua các file `daily/YYYY-MM-DD.md`, không cần log riêng.

---

## Recovery

### File missing
- `last_scan.json` missing → treat as cold start:
  - Scan window: 24h gần nhất
  - `run_count`: 1
  - Báo warning ở đầu report: "⚠️ Cold start — không có lịch sử quét trước đó"
- `processed_links.json` missing → không skip URL nào:
  - Báo warning
  - Scan có thể xuất hiện tin trùng với trước đó — OK cho lần cold start, sau đó tự phục hồi

### File corrupt (parse fail)
- KHÔNG auto-fix. KHÔNG ghi đè.
- Rename file corrupt thành `{file}.corrupt-{timestamp}`.
- Treat như file missing (cold start path).
- Ghi warning lớn ở đầu report + yêu cầu user review file `.corrupt-*`.

### Timestamp không monotonic
Nếu `last_scan_timestamp` mới < timestamp cũ (clock skew hay revert):
- Abort write.
- Log error.
- Yêu cầu user confirm overwrite.

---

## Privacy & leakage

- `processed_links.json` là public metadata (chỉ URL) — OK commit vào git.
- KHÔNG lưu: API keys, user tokens, private URLs có auth params, paywall bypass tokens.
- Nếu URL chứa query params có thể là session token → strip trước khi lưu.
