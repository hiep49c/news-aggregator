---
inclusion: auto
globs: banking-tech/**
---

# Banking Tech — Java Web & AI in Banking Digest Policy

## Objective

Tạo digest chất lượng cao bằng Tiếng Việt về hai trục nội dung liên quan trực tiếp tới công việc của người đọc:

1. **Java web development** — phát triển ứng dụng web bằng hệ sinh thái Java (Spring, Jakarta EE, microservices).
2. **AI trong nghiệp vụ ngân hàng / tài chính** (AI in banking & financial services) — ứng dụng AI vào core banking, tín dụng, rủi ro, gian lận, tuân thủ (compliance), trải nghiệm khách hàng.

KHÔNG chỉ liệt kê headline. Mỗi mục PHẢI giải thích:
- điều gì thực sự mới
- vì sao quan trọng với developer / kiến trúc sư / ngân hàng
- tác động kỹ thuật và nghiệp vụ
- liên hệ với xu hướng lớn

Output phải đọc giống analyst/research briefing, KHÔNG phải RSS feed.

---

# Scope

## Theo dõi — Java Web

- Java LTS releases (JDK 21, 25...) và tính năng ngôn ngữ (virtual threads, pattern matching, structured concurrency)
- Spring ecosystem: Spring Boot, Spring Framework, Spring Security, Spring AI, Spring Cloud
- Jakarta EE, Quarkus, Micronaut, Helidon
- Kiến trúc microservices (vi dịch vụ), event-driven, reactive (WebFlux)
- Build & runtime: Maven, Gradle, GraalVM native image
- Hiệu năng JVM (tối ưu suy luận runtime), tuning GC, observability
- Bảo mật ứng dụng web: OWASP, CVE ảnh hưởng Spring/Java
- API design: REST, gRPC, GraphQL

## Theo dõi — AI in Banking

- AI/ML trong credit scoring (chấm điểm tín dụng), fraud detection (phát hiện gian lận), AML (chống rửa tiền)
- LLM/agentic workflow (luồng AI tự hành) trong customer service, RPA ngân hàng
- RegTech và compliance automation (tự động hoá tuân thủ)
- Core banking modernization, open banking, API banking
- Quy định AI cho tài chính: EU AI Act, Basel, SBV (Ngân hàng Nhà nước VN), MAS, FED
- Triển khai thực tế tại ngân hàng (case study, adoption đo lường được)
- Rủi ro: model risk, bias, explainability (khả năng giải thích), data governance

## Loại trừ

- Tin AI tổng quát không gắn nghiệp vụ ngân hàng (đã thuộc `ai-news`)
- Tin tài chính vĩ mô thuần tuý không liên quan công nghệ (giá vàng, tỷ giá... trừ khi có góc công nghệ)

---

# Sources

## Tier 1 — Official / nguồn gốc
- Spring Blog (spring.io)
- OpenJDK / Inside.java / Oracle Java
- Quarkus.io, Micronaut docs
- Ngân hàng Nhà nước Việt Nam (sbv.gov.vn)
- Thông cáo chính thức của ngân hàng / fintech (press release)
- EU AI Act, Bank for International Settlements (BIS)

## Tier 2 — Báo chí uy tín
- InfoQ (Java / Architecture queue)
- The Register, Ars Technica (mục enterprise/dev)
- Reuters / Bloomberg (mục fintech, banking technology)
- American Banker, Finextra
- VnExpress / CafeF (mục công nghệ ngân hàng, fintech)

## Tier 3 — Think tank / analyst / chuyên gia ngành
- Baeldung, Vlad Mihalcea blog, Thoughtworks Technology Radar
- Gartner / Forrester (banking tech, bản tóm tắt công khai)
- McKinsey / Deloitte insights (AI in banking)

## Tier 4 — Community / social (PHẢI cross-check)
- r/java, r/SpringBoot, Hacker News
- DZone, Medium engineering blogs
- LinkedIn posts của kỹ sư / kiến trúc sư ngân hàng

KHÔNG xem Tier 4 là fact. PHẢI cross-verify theo `core.rules.md`.

---

# Required Structure

Mỗi báo cáo PHẢI có:

1. **Tóm tắt nhanh (Ultra Brief)** — 3–5 dòng
2. **Executive Summary** — 5–15 ý quan trọng nhất
3. **Phần Java Web** — phân tích sâu các tin chính
4. **Phần AI in Banking** — phân tích sâu các tin chính
5. **Các tin khác đáng chú ý** — bullet cho tin impact 3–4
6. **Meta Trends** — xu hướng lớn (Spring AI, native image adoption, agentic banking, RegTech...)
7. **Strategic Watchlist** — điều cần theo dõi tiếp
8. **⚠️ Nguồn không truy cập được** (nếu có)
9. **Δ Delta** — thay đổi so với báo cáo trước

Mỗi tin chính phải có format: Tóm tắt → Điểm mới → Phân tích → Ảnh hưởng thực tế → Verification / Confidence / Impact Score.

---

# Language Policy

- Output mặc định: Tiếng Việt.
- Thuật ngữ tiếng Anh giữ nguyên, giải thích ngắn ở lần đầu xuất hiện.
  - Ví dụ: dependency injection (tiêm phụ thuộc), idempotency (tính bất biến khi lặp lại), explainability (khả năng giải thích).
- Tên riêng (Spring, Quarkus, OpenAI...) không cần giải thích.

---

# Critical Rule

Ưu tiên phân tích sâu và liên kết sự kiện hơn số lượng tin. Ít tin nhưng sâu tốt hơn nhiều tin hời hợt. Tuân thủ tuyệt đối `core.rules.md` (anti-hallucination, scoring, tier system) và `memory.rules.md`.
