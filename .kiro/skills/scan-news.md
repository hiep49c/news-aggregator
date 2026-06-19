---
name: scan-news
description: Quy trình quét và tổng hợp tin tức mới cho các module (ai-news, world-conflicts, vn-news). Tuân thủ 7-step workflow với atomic write, same-day re-scan, failure handling và cross-module linkage.
---

# Skill: Scan News

Skill này định nghĩa quy trình thực thi khi quét tin mới. Áp dụng cho mọi module (ai-news, world-conflicts, vn-news).

---

## Execution order (BẮT BUỘC)

Mỗi scan session PHẢI chạy đúng thứ tự sau. Không bỏ bước, không đổi thứ tự.

### Step 1 — Load state
- Đọc `{module}/memory/last_scan.json`.
- Đọc `{module}/memory/processed_links.json` (nếu còn tồn tại sau rotation — xem `memory.rules.md`).
- Nếu file không tồn tại / parse fail → fallback như `memory.rules.md` quy định. Không tự tạo state mới im lặng.

### Step 2 — Determine scan window
- Mặc định: từ `last_scan_timestamp` đến hiện tại.
- Nếu `last_scan_date` == hôm nay → đây là **same-day re-scan** (áp dụng rule "Same-day re-scan" bên dưới).
- Nếu khoảng cách > 7 ngày → ghi cảnh báo trong report, cap lại ở 7 ngày gần nhất.

### Step 3 — Search & fetch
- Tìm tin trong scan window, tuân theo `Sources` của module rule.
- Skip URL đã có trong `processed_urls`.
- Fetch failure (timeout, paywall, 4xx/5xx) → ghi vào internal fetch log, KHÔNG retry vô hạn (tối đa 2 lần/URL).

### Step 4 — Analyze
- Cross-verify theo tier system (core).
- Apply scoring rubric (`core.rules.md` → Scoring).
- Cluster + delta compare với report gần nhất (core → Clustering, Delta).
- Apply cross-module linkage check (xem "Cross-module linkage" bên dưới).

### Step 5 — Write output (atomic)
- Ghi file tạm `daily/YYYY-MM-DD.md.tmp` → validate (có đủ section bắt buộc) → rename thành `.md`.
- Ghi `.html` tương tự (tạm → rename).
- **HTML PHẢI được tạo cùng lúc với .md** — không bao giờ chỉ tạo .md mà thiếu .html.
- KHÔNG ghi đè file gốc trực tiếp. Nếu rename fail → giữ file cũ, ghi warning.

#### HTML generation rules
- Dùng template dark mode chuẩn (xem file `.html` gần nhất trong cùng module làm reference).
- Bắt buộc: Table of Contents với anchor links, responsive, badges cho verification/impact.
- Nội dung HTML = render từ nội dung .md, giữ nguyên structure và data.
- Ngôn ngữ HTML = cùng ngôn ngữ với .md (tiếng Việt mặc định).
- CSS inline trong `<style>` tag (không external stylesheet).
- **LINK NGUỒN BẮT BUỘC:** Mỗi tin trong HTML PHẢI có block "Nguồn:" với hyperlinks `<a href="URL">tên nguồn</a>` clickable. Không bao giờ chỉ liệt kê URL trong .md mà không render thành link trong .html.

### Step 6 — Update memory (LAST, atomic)
- Ghi `processed_links.json.tmp` → rename. Tuân `memory.rules.md` (rotation).
- Ghi `last_scan.json.tmp` → rename.
- **Memory update LUÔN là bước cuối cùng.** Nếu report write fail → không update memory (để lần sau retry).
- Nếu memory write fail sau khi report đã ghi → log error lớn, yêu cầu user sync manual.

### Step 7 — Update dashboard
- Theo skill `update-dashboard`.

---

## Same-day re-scan (update-in-place)

Khi user trigger scan 2+ lần trong cùng ngày:

- KHÔNG tạo file mới. Update `daily/YYYY-MM-DD.md` tại chỗ.
- Tăng `run_count` trong `last_scan.json` lên 1.
- Header file: thêm hoặc cập nhật dòng "Cập nhật lần N" kèm timestamp quét mới.
- Bắt buộc thêm section **"🔄 Δ Từ lần quét trước"** ở gần đầu report (sau Tóm tắt nhanh), liệt kê:
  - Tin mới xuất hiện so với lần quét trước cùng ngày
  - Tin có cập nhật đáng kể (thay đổi verification, số liệu, tác động)
  - Tin bị hạ cấp / bác bỏ
- Nếu không có delta → ghi "Không có thay đổi đáng kể từ lần quét trước", KHÔNG viết lại toàn bộ report.

## New-day scan

- Tạo file mới `daily/YYYY-MM-DD.md`.
- Reset `run_count` về 1.
- Ở section "So sánh với giai đoạn trước", link tới file ngày trước gần nhất nếu relevant.

---

## Failure modes

### Không có tin mới trong scan window
- VẪN ghi file report ngắn với:
  - Header chuẩn
  - Section "Không có tin mới đáng chú ý trong khoảng [window]"
  - Liệt kê nguồn đã quét
- VẪN update `last_scan.json` (để window tiếp theo tiến lên).
- KHÔNG thêm URL nào vào `processed_links.json`.

### Tin ít (1-2 tin) trong window 24h
- Mở rộng window lên 3 ngày.
- Nếu vẫn ít → mở rộng tối đa 7 ngày.
- Ghi rõ window thực tế dùng ở đầu report.

### Minimum story count (BẮT BUỘC)
- Mỗi module PHẢI có tối thiểu **8 stories** trong mỗi báo cáo (trừ khi thực sự không có tin mới).
- Nếu window 24h cho < 8 stories → tự động mở rộng lên 3 ngày.
- Nếu 3 ngày vẫn < 8 → mở rộng 7 ngày.
- Ưu tiên: tin impact cao trước, sau đó bổ sung tin impact 5-6 để đủ số lượng.
- Tin impact 3-4 có thể gộp vào section "Các tin khác đáng chú ý" dạng bullet.

### Fetch partial failure
- Nếu ≥ 50% nguồn Tier 1+2 fail → abort scan, giữ nguyên state cũ, báo user.
- Nếu < 50% fail → tiếp tục với nguồn còn lại. **KHÔNG liệt kê nguồn fail trong báo cáo** — đơn giản không dùng nguồn đó.
- **KHÔNG CẦN section "⚠️ Nguồn không truy cập được" trong output** — loại bỏ hoàn toàn section này khỏi báo cáo. Chỉ dùng nguồn đọc được.

### Analyze error / AI generation fail
- KHÔNG update memory.
- Giữ nguyên file report cũ.
- Log error.

---

## Cross-module linkage

Khi một tin có impact score ≥ 8 và liên quan rõ ràng đến domain của module khác, PHẢI:

1. Flag trong section "Ảnh hưởng liên ngành" của module gốc.
2. Nếu module khác có scan cùng ngày / sau đó → module đó phải check event này và thêm section "🔗 Liên kết từ {module khác}" nếu relevant.

Ví dụ cross-link:
- World Conflicts (Iran war) → VN News (CPI, tỷ giá, thâm hụt thương mại)
- AI News (GPU supply, chip export) → VN News (Intel, data center, FDI)
- World Conflicts (NATO, Trump-Xi) → AI News (AI cyber arms race, chip export control)

## Output budget (soft caps)

Để tránh report phình khó đọc NHƯNG VẪN ĐẢM BẢO CHI TIẾT:
- Tóm tắt nhanh / Ultra Brief: ≤ 10 bullets (mỗi bullet 1-2 câu)
- Executive Summary: ≤ 15 items (mỗi item 2-3 câu giải thích, KHÔNG chỉ headline)
- Số story chính phân tích sâu: 8–12 (không bao giờ > 16 trừ khi user request)
- Tin nhỏ gộp vào section "Các tin khác đáng chú ý" dạng bullet list (mỗi bullet vẫn 2-3 câu)

## YÊU CẦU ĐỘ DÀI TỐI THIỂU (BẮT BUỘC)

Mỗi tin CHÍNH (impact 7+) trong Full Analysis PHẢI đạt:
- **Impact 9-10:** Tối thiểu 1000 từ — đủ bối cảnh, diễn biến chi tiết, phân tích sâu, tác động đa chiều
- **Impact 7-8:** Tối thiểu 500 từ — đủ để người đọc hiểu rõ mà không cần mở link
- **Impact 5-6:** Tối thiểu 200 từ — ngắn gọn nhưng đủ context

### Format cho mỗi tin chính:

```
## [Số]. [Icon] [Tiêu đề mô tả đầy đủ — không viết tắt]

### Bối cảnh
2-4 câu giải thích: chuyện gì xảy ra trước, tại sao tin này quan trọng, đặt vào context lớn hơn.

### Diễn biến chi tiết
3-8 đoạn: AI làm gì, khi nào, ở đâu, con số cụ thể, trích dẫn quan trọng, timeline.
Viết dạng ĐOẠN VĂN có narrative flow. Bullet chỉ cho dữ kiện ngắn.

### Phân tích
2-4 đoạn: TẠI SAO chuyện này xảy ra? Cơ chế? Động lực các bên? So sánh với quá khứ? 
Tách rõ: Fact (đã xác minh) vs Analysis (nhận định có evidence).

### Tác động thực tế
2-3 đoạn: Ảnh hưởng ai? Developer? Enterprise? Người dùng? Thay đổi gì measurable?
Nêu cụ thể — không nói chung chung "ảnh hưởng lớn".

### Đánh giá
- Impact Score: X/10
- Verification / Confidence
- Điều cần theo dõi tiếp

### Nguồn
- Link clickable
```

### TUYỆT ĐỐI KHÔNG ĐƯỢC:
- Viết tin Impact 9 chỉ bằng 5-10 bullet points rời rạc
- Bỏ qua phần "Bối cảnh" (reader không hiểu WHY)
- Bỏ qua phần "Phân tích" (reader chỉ biết WHAT không biết SO WHAT)
- Viết Executive Summary chỉ bằng headline 1 dòng

Vượt cap → ưu tiên stories có impact score cao nhất + delta lớn nhất.
