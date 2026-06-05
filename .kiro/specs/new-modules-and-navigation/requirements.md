# Requirements Document

## Introduction

Tính năng này mở rộng hệ thống News Aggregator theo hai hướng:

1. **Thêm 2 module nội dung mới**, xây dựng theo đúng khuôn mẫu các module hiện có (`ai-news`, `world-conflicts`, `vn-news`, `sci-tech`):
   - **Module Banking Tech** — tổng hợp và phân tích tin tức về phát triển Java web (Java web development) và ứng dụng AI trong nghiệp vụ ngân hàng / tài chính (AI in banking & financial services).
   - **Module IT Career** — tổng hợp tin tuyển dụng IT (IT recruitment) và đánh giá / review các công ty IT, thu thập từ các diễn đàn nghề nghiệp và các trang như ITviec, VietnamWorks.

2. **Hai cải tiến điều hướng (navigation)** áp dụng cho toàn hệ thống:
   - **Thanh điều hướng ngang trên cùng (Report Top Nav)** trong mọi báo cáo HTML hằng ngày: liệt kê tóm tắt từng phần nội dung, cho phép click để nhảy thẳng tới phần đó.
   - **Cơ chế ẩn/hiện (expand/collapse)** trên dashboard `index.html`: mỗi nhóm menu cha có mũi tên để thu gọn hoặc mở rộng danh sách các mục con bên trong.

### Giả định cần xác nhận (Assumptions)

Để tạo bản nháp ban đầu mà không chặn tiến độ, tài liệu này dùng các tên thư mục module sau. Người dùng có thể yêu cầu đổi tên ở vòng review:
- Module Banking Tech → thư mục `banking-tech/`
- Module IT Career → thư mục `it-career/`

Các quyết định mở khác được liệt kê ở cuối tài liệu trong mục **Open Questions**.

## Glossary

- **System**: Toàn bộ hệ thống News Aggregator, gồm các module nội dung, dashboard, và bộ rules/skills trong `.kiro/`.
- **Module**: Một đơn vị nội dung độc lập gồm thư mục `memory/` và thư mục `daily/`, ví dụ `ai-news/`.
- **Banking_Tech_Module**: Module nội dung mới về Java web development và ứng dụng AI trong nghiệp vụ ngân hàng, đặt tại thư mục `banking-tech/`.
- **IT_Career_Module**: Module nội dung mới về tuyển dụng IT và review công ty IT, đặt tại thư mục `it-career/`.
- **Report_Generator**: Quy trình tạo báo cáo hằng ngày (định nghĩa trong skill `scan-news`), sinh ra cặp file `YYYY-MM-DD.md` và `YYYY-MM-DD.html` trong thư mục `daily/` của module.
- **Daily_Report_HTML**: File báo cáo HTML hằng ngày `YYYY-MM-DD.html` của một module.
- **Latest_Report**: `Daily_Report_HTML` có giá trị ngày `YYYY-MM-DD` lớn nhất trong thư mục `daily/` của một module.
- **Report_Top_Nav**: Thanh điều hướng ngang cố định ở đầu mỗi `Daily_Report_HTML`, chứa các mục liên kết tới từng phần nội dung của báo cáo.
- **Dashboard**: File `index.html` ở thư mục gốc dự án, đóng vai trò trang điều hướng chính.
- **Sidebar_Nav**: Thanh điều hướng dọc bên trái của `Dashboard`.
- **Parent_Menu_Group**: Một nhóm trong `Sidebar_Nav` gồm một nhãn nhóm (section label) và các mục con (link báo cáo) thuộc nhóm đó.
- **Collapse_Toggle**: Phần tử điều khiển (mũi tên) cho phép ẩn hoặc hiện các mục con của một `Parent_Menu_Group`.
- **Module_Rules_File**: File rule riêng của module trong `.kiro/rules/{module}.rules.md` định nghĩa scope, nguồn, và chính sách phân tích.
- **Memory_State**: Cặp file `memory/last_scan.json` và `memory/processed_links.json` của một module.
- **Section_Summary**: Chuỗi văn bản ngắn mô tả nội dung của một phần báo cáo, hiển thị trong `Report_Top_Nav`.

## Requirements

### Requirement 1: Tạo cấu trúc thư mục module Banking Tech

**User Story:** As a Java web developer working on AI in banking, I want a dedicated content module structured like the existing modules, so that fresh news on Java web and AI banking is aggregated consistently.

#### Acceptance Criteria

1. THE System SHALL tạo thư mục `banking-tech/` ở thư mục gốc dự án.
2. THE System SHALL tạo hai thư mục con `banking-tech/memory/` và `banking-tech/daily/` bên trong `banking-tech/`.
3. THE System SHALL tạo file `banking-tech/memory/last_scan.json` chứa các trường bắt buộc `last_scan_timestamp` (chuỗi ISO 8601 kèm múi giờ, ví dụ `2026-05-19T10:00:00+07:00`), `last_scan_date` (định dạng `YYYY-MM-DD`), `run_count` (số nguyên ≥ 0), và `stories_found` (số nguyên ≥ 0) theo schema định nghĩa trong `memory.rules.md`.
4. THE System SHALL tạo file `banking-tech/memory/processed_links.json` chứa trường `processed_urls` được khởi tạo là một mảng rỗng `[]`.
5. WHEN `Report_Generator` chạy cho `Banking_Tech_Module` và toàn bộ output bắt buộc được tạo thành công, THE `Report_Generator` SHALL tạo cặp file `banking-tech/daily/YYYY-MM-DD.md` và `banking-tech/daily/YYYY-MM-DD.html`, trong đó `YYYY-MM-DD` là ngày chạy theo múi giờ UTC+07:00.
6. IF việc tạo một trong hai file báo cáo (`.md` hoặc `.html`) thất bại, THEN THE `Report_Generator` SHALL giữ nguyên `Memory_State` trước đó của module, ghi một cảnh báo lỗi vào phần metadata/cảnh báo của báo cáo, và tiếp tục phiên chạy mà không dừng toàn bộ pipeline.
7. IF thư mục `banking-tech/` hoặc các file memory đã tồn tại và hợp lệ (parse JSON thành công và đủ trường bắt buộc), THEN THE System SHALL giữ nguyên nội dung hiện có và không ghi đè dữ liệu.
8. THE `Banking_Tech_Module` SHALL tạo toàn bộ nội dung báo cáo bằng Tiếng Việt.

### Requirement 2: Tạo cấu trúc thư mục module IT Career

**User Story:** As an IT professional, I want a dedicated content module for IT job postings and IT company reviews, so that recruitment trends and company evaluations are aggregated consistently.

#### Acceptance Criteria

1. THE System SHALL tạo thư mục `it-career/` tại thư mục gốc của dự án (cùng cấp với các module hiện có như `ai-news/`, `vn-news/`, `sci-tech/`, `world-conflicts/`).
2. THE System SHALL tạo hai thư mục con `it-career/memory/` và `it-career/daily/` bên trong thư mục `it-career/`.
3. THE System SHALL tạo file `it-career/memory/last_scan.json` chứa đầy đủ các trường bắt buộc: `last_scan_timestamp` (chuỗi định dạng ISO 8601 kèm múi giờ, ví dụ `2026-05-13T15:03:36+07:00`), `last_scan_date` (chuỗi định dạng `YYYY-MM-DD`), `run_count` (số nguyên ≥ 0), và `stories_found` (số nguyên ≥ 0), theo schema định nghĩa trong `memory.rules.md`.
4. THE System SHALL tạo file `it-career/memory/processed_links.json` chứa trường `processed_urls` là một mảng, được khởi tạo là mảng rỗng `[]` khi tạo mới.
5. WHEN `Report_Generator` chạy cho `IT_Career_Module`, THE `Report_Generator` SHALL tạo cặp file `it-career/daily/YYYY-MM-DD.md` và `it-career/daily/YYYY-MM-DD.html`, trong đó `YYYY-MM-DD` là ngày chạy tương ứng với giá trị `last_scan_date`.
6. IF việc tạo một trong hai file báo cáo (`.md` hoặc `.html`) thất bại do lỗi hệ thống, THEN THE `Report_Generator` SHALL ghi một bản ghi log lỗi mô tả nguyên nhân thất bại, giữ nguyên trạng thái memory trước đó (không cập nhật `last_scan.json` và `processed_links.json`), và tiếp tục phiên chạy mà không dừng toàn bộ pipeline.
7. IF thư mục `it-career/` hoặc các file `last_scan.json`/`processed_links.json` đã tồn tại và hợp lệ (parse JSON thành công và đủ trường bắt buộc), THEN THE System SHALL giữ nguyên nội dung hiện có và không ghi đè dữ liệu.
8. THE `IT_Career_Module` SHALL tạo toàn bộ nội dung văn bản của báo cáo bằng Tiếng Việt.

### Requirement 3: Phạm vi và nguồn nội dung module Banking Tech

**User Story:** As a reader of the Banking Tech module, I want the report scoped to Java web and AI banking topics from defined sources, so that the content is relevant and verifiable.

#### Acceptance Criteria

1. THE `Banking_Tech_Module` SHALL bao gồm nội dung thuộc các chủ đề Java web development, Spring ecosystem, kiến trúc microservices, và ứng dụng AI trong nghiệp vụ ngân hàng / tài chính; THE `Banking_Tech_Module` SHALL loại trừ nội dung không thuộc các chủ đề này.
2. THE `Banking_Tech_Module` SHALL thu thập nội dung từ các nguồn được liệt kê trong `Module_Rules_File`, phân loại theo tier system định nghĩa trong `core.rules.md`.
3. THE `Banking_Tech_Module` SHALL gán cho mỗi tin một điểm Verification, một điểm Confidence, và một điểm Impact Score theo thang đo định nghĩa trong `core.rules.md`.
4. IF một nguồn không truy cập được (connection timeout, bị chặn, hoặc không có phản hồi sau số lần thử tối đa định nghĩa trong skill `scan-news`), THEN THE `Banking_Tech_Module` SHALL ghi nguồn đó kèm nguyên nhân vào danh sách nguồn lỗi ở cuối báo cáo và tiếp tục với các nguồn còn lại.
5. IF từ 50% số nguồn trở lên không truy cập được, THEN THE `Banking_Tech_Module` SHALL dừng phiên quét, giữ nguyên `Memory_State`, và ghi cảnh báo lỗi trong báo cáo.
6. IF số tin trong cửa sổ quét 24 giờ nhỏ hơn 8, THEN THE `Banking_Tech_Module` SHALL mở rộng cửa sổ quét theo bước 24 giờ → 3 ngày → 7 ngày theo quy tắc trong skill `scan-news`.
7. THE `Banking_Tech_Module` SHALL giải thích mỗi thuật ngữ tiếng Anh bằng Tiếng Việt ở lần xuất hiện đầu tiên.

### Requirement 4: Phạm vi và nguồn nội dung module IT Career

**User Story:** As a reader of the IT Career module, I want recruitment news and company reviews from defined sources, so that I can assess job opportunities and companies.

#### Acceptance Criteria

1. THE `IT_Career_Module` SHALL bao gồm nội dung thuộc các chủ đề tin tuyển dụng IT, đánh giá công ty IT, mức lương, và xu hướng thị trường lao động IT.
2. THE `IT_Career_Module` SHALL thu thập nội dung từ các nguồn ITviec và VietnamWorks, cùng các diễn đàn nghề nghiệp được liệt kê trong `Module_Rules_File`.
3. WHERE một tin bắt nguồn từ nguồn Tier 4 (cộng đồng / diễn đàn) theo `core.rules.md`, THE `IT_Career_Module` SHALL gán điểm Confidence mức LOW cho tới khi tin được cross-check.
4. WHEN một tin từ nguồn Tier 4 được xác nhận bởi official source hoặc ≥2 nguồn Tier 1-2 độc lập, THE `IT_Career_Module` SHALL gán điểm Confidence mức HIGH theo `core.rules.md`.
5. WHEN một tin từ nguồn Tier 4 được xác nhận bởi một nguồn uy tín nhưng chưa có independent confirmation, THE `IT_Career_Module` SHALL gán điểm Confidence mức MEDIUM theo `core.rules.md`.
6. IF một nguồn không truy cập được (connection timeout, paywall, hoặc nội dung rỗng), THEN THE `IT_Career_Module` SHALL ghi nguồn đó kèm nguyên nhân vào danh sách nguồn lỗi ở cuối báo cáo và tiếp tục với các nguồn còn lại.
7. IF số tin trong cửa sổ quét 24 giờ nhỏ hơn 8, THEN THE `IT_Career_Module` SHALL mở rộng cửa sổ quét theo bước 24 giờ → 3 ngày → 7 ngày theo quy tắc trong skill `scan-news`.
8. THE `IT_Career_Module` SHALL gán cho mỗi tin một điểm Verification, một điểm Confidence, và một điểm Impact Score theo thang đo định nghĩa trong `core.rules.md`.

### Requirement 5: File rule cho mỗi module mới

**User Story:** As a maintainer, I want each new module to have its own rules file following the existing convention, so that module behavior is governed consistently.

#### Acceptance Criteria

1. THE System SHALL tạo file `.kiro/rules/banking-tech.rules.md` với front-matter chứa `inclusion: auto` và `globs: banking-tech/**`.
2. THE System SHALL tạo file `.kiro/rules/it-career.rules.md` với front-matter chứa `inclusion: auto` và `globs: it-career/**`.
3. THE `Module_Rules_File` của mỗi module mới SHALL chứa bốn mục có nội dung không rỗng: Objective, Scope, Sources, và Required Structure.
4. THE `Module_Rules_File` của mỗi module mới SHALL liệt kê tối thiểu 3 nguồn cụ thể được nêu tên.
5. THE `Module_Rules_File` của mỗi module mới SHALL ánh xạ mỗi nguồn tới đúng một trong các tier (Tier 1–4) định nghĩa trong `core.rules.md`.
6. IF việc tạo một `Module_Rules_File` thất bại, THEN THE System SHALL ghi log lỗi, giữ nguyên các file rule hiện có, và không ghi đè dữ liệu.

### Requirement 6: Thanh điều hướng ngang trên cùng trong báo cáo HTML

**User Story:** As a report reader, I want a horizontal top navigation bar listing section summaries, so that I can click an item and jump straight to that section's content.

#### Acceptance Criteria

1. WHEN `Report_Generator` tạo một `Daily_Report_HTML`, THE `Report_Generator` SHALL chèn một `Report_Top_Nav` ở vị trí trên cùng của phần thân nội dung, phía trước phần nội dung đầu tiên.
2. THE `Report_Top_Nav` SHALL chứa một mục cho mỗi phần nội dung cấp cao nhất có `id` anchor riêng, tối đa 20 mục.
3. THE `Report_Top_Nav` SHALL hiển thị `Section_Summary` cho mỗi mục, với độ dài tối đa 80 ký tự cho mỗi `Section_Summary`.
4. THE mỗi mục trong `Report_Top_Nav` SHALL là một anchor link trỏ tới `id` của phần nội dung tương ứng trong cùng tài liệu.
5. WHEN người dùng kích hoạt một mục trong `Report_Top_Nav`, THE `Daily_Report_HTML` SHALL cuộn sao cho tiêu đề của phần nội dung tương ứng nằm trong khung nhìn và không bị `Report_Top_Nav` che khuất.
6. WHILE người dùng cuộn nội dung báo cáo, THE `Report_Top_Nav` SHALL giữ cố định ở mép trên của khung nhìn (sticky) và mọi mục của nó SHALL vẫn truy cập được.
7. THE `Report_Top_Nav` SHALL áp dụng cho báo cáo HTML của tất cả các module, gồm cả các module hiện có và các module mới.
8. THE `Report_Top_Nav` SHALL giữ nguyên giao diện dark mode và hành vi responsive tại breakpoint 768px hiện có của báo cáo.
9. IF một `Section_Summary` dài hơn 80 ký tự, THEN THE `Report_Generator` SHALL cắt ngắn và thêm chỉ báo cắt (ví dụ dấu `…`).
10. IF tổng các mục của `Report_Top_Nav` vượt quá chiều rộng khung nhìn, THEN THE `Report_Top_Nav` SHALL cho phép cuộn ngang để truy cập mọi mục.

### Requirement 7: Ẩn/hiện mục con của nhóm menu trên dashboard

**User Story:** As a dashboard user, I want an arrow to expand or collapse the child items inside each parent menu group, so that I can control which report links are visible.

#### Acceptance Criteria

1. THE `Dashboard` SHALL hiển thị một `Collapse_Toggle` cho mỗi `Parent_Menu_Group` có ít nhất một mục con trong `Sidebar_Nav`.
2. WHEN người dùng kích hoạt `Collapse_Toggle` của một `Parent_Menu_Group` đang ở trạng thái mở rộng, THE `Dashboard` SHALL ẩn các mục con của `Parent_Menu_Group` đó trong vòng 300 mili-giây và chuyển trạng thái của nhóm sang thu gọn.
3. WHEN người dùng kích hoạt `Collapse_Toggle` của một `Parent_Menu_Group` đang ở trạng thái thu gọn, THE `Dashboard` SHALL hiện các mục con của `Parent_Menu_Group` đó trong vòng 300 mili-giây và chuyển trạng thái của nhóm sang mở rộng.
4. THE `Collapse_Toggle` SHALL có hai trạng thái hiển thị phân biệt được, mỗi trạng thái tương ứng với trạng thái mở rộng hoặc thu gọn của `Parent_Menu_Group`.
5. WHEN `Dashboard` được mở lần đầu, THE `Dashboard` SHALL đặt mọi `Parent_Menu_Group` có mục con ở trạng thái mở rộng.
6. THE `Dashboard` SHALL giữ nguyên giao diện dark mode hiện có của `Sidebar_Nav` khi thêm `Collapse_Toggle`.
7. THE `Dashboard` SHALL giữ nguyên hành vi responsive hiện có của `Sidebar_Nav` tại breakpoint 768px.
8. WHILE phiên làm việc hiện tại của `Dashboard` chưa được tải lại, THE `Dashboard` SHALL giữ trạng thái mở rộng/thu gọn của mỗi `Parent_Menu_Group` cho tới khi `Collapse_Toggle` tương ứng được kích hoạt lại.

### Requirement 8: Tích hợp module mới vào dashboard

**User Story:** As a dashboard user, I want the two new modules to appear in the sidebar and on the home cards, so that I can navigate to their reports.

#### Acceptance Criteria

1. THE `Dashboard` SHALL hiển thị một `Parent_Menu_Group` cho `Banking_Tech_Module` và một `Parent_Menu_Group` cho `IT_Career_Module` trong `Sidebar_Nav`.
2. WHEN người dùng kích hoạt link `Latest_Report` của một module mới, THE `Dashboard` SHALL nạp `Latest_Report` của module đó vào khung nội dung.
3. THE `Dashboard` SHALL hiển thị một thẻ module (module card) ở khu vực trang chủ cho mỗi module mới.
4. WHEN skill `update-dashboard` chạy sau khi một báo cáo mới được tạo cho một module mới, THE `Dashboard` SHALL được cập nhật để link của module đó trỏ tới `Latest_Report`.
5. THE `Dashboard` SHALL trỏ mỗi link tới một file `Daily_Report_HTML` thực sự tồn tại.
6. IF một module mới chưa có `Daily_Report_HTML` nào, THEN THE `Dashboard` SHALL hiển thị `Parent_Menu_Group` của module đó ở trạng thái rỗng (không có link báo cáo) thay vì trỏ tới file không tồn tại.
7. IF việc nạp một `Daily_Report_HTML` thất bại, THEN THE `Dashboard` SHALL hiển thị thông báo lỗi trong khung nội dung mà không làm hỏng `Sidebar_Nav`.

### Requirement 9: Toàn vẹn trạng thái bộ nhớ của module mới

**User Story:** As a maintainer, I want the new modules to follow the existing memory integrity rules, so that scans are idempotent and state is never corrupted.

#### Acceptance Criteria

1. WHEN `Report_Generator` ghi `Memory_State` của một module mới, THE `Report_Generator` SHALL ghi dữ liệu vào file tạm `.tmp`, validate (parse JSON thành công, đủ các trường bắt buộc, schema hợp lệ, timestamp hợp lệ), rồi đổi tên nguyên tử (atomic rename) sang file đích theo `memory.rules.md`.
2. IF bất kỳ file đầu ra bắt buộc (`YYYY-MM-DD.md` hoặc `YYYY-MM-DD.html`) ghi thất bại hoặc validate đầu ra thất bại, THEN THE `Report_Generator` SHALL giữ nguyên `Memory_State` cũ của module, không cập nhật bộ nhớ, và ghi log lỗi.
3. IF một URL — sau khi chuẩn hoá canonical (canonical normalization) theo `memory.rules.md` — đã tồn tại trong `processed_urls` của một module mới, THEN THE `Report_Generator` SHALL bỏ qua URL đó, không xử lý lại và không tăng bộ đếm `stories_found`.
4. IF file `last_scan.json` của một module mới bị thiếu, THEN THE `Report_Generator` SHALL xử lý như cold start với cửa sổ quét 24 giờ, đặt `run_count` = 1, và ghi cảnh báo cold start trong báo cáo.
5. IF việc validate file `.tmp` thất bại khi ghi `Memory_State`, THEN THE `Report_Generator` SHALL xoá file `.tmp`, giữ nguyên file `Memory_State` cũ, và ghi log lỗi.
6. IF file `processed_links.json` của một module mới bị thiếu, THEN THE `Report_Generator` SHALL không bỏ qua URL nào trong lần chạy đó và ghi cảnh báo trong báo cáo.
7. IF file `last_scan.json` hoặc `processed_links.json` của một module mới không parse được (corrupt), THEN THE `Report_Generator` SHALL đổi tên file lỗi thành `{file}.corrupt-{timestamp}` theo `memory.rules.md`, xử lý như file bị thiếu, và ghi cảnh báo trong báo cáo.

## Open Questions

1. **Tên thư mục module**: Tài liệu giả định `banking-tech/` và `it-career/`. Người dùng có muốn dùng tên khác (ví dụ `java-banking-ai/`, `it-jobs/`) không?
2. **Tách hay gộp chủ đề Banking Tech**: Java web development và AI trong ngân hàng có nên nằm chung một module, hay tách thành hai module riêng?
3. **Trùng lặp với module hiện có**: Một số tin tuyển dụng / ngân hàng có thể trùng phạm vi với `vn-news`. Có cần quy tắc chống trùng lặp cross-module cụ thể giữa các module này không?
4. **Danh sách nguồn diễn đàn**: Ngoài ITviec và VietnamWorks, người dùng muốn liệt kê cụ thể các diễn đàn nào (ví dụ Reddit, Voz, LinkedIn) cho module IT Career?
5. **Áp dụng Report Top Nav cho báo cáo cũ**: Thanh điều hướng ngang chỉ áp dụng cho báo cáo tạo mới, hay cần backfill cho các file HTML đã tồn tại?
