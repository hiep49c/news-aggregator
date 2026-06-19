---
inclusion: auto
globs: java-ai-tech/**
---

# Java AI Tech — Java Web & Ứng Dụng AI Digest Policy

## Objective

Tạo digest chất lượng cao bằng Tiếng Việt về hệ sinh thái Java web và ứng dụng AI trong doanh nghiệp — phạm vi RỘNG, không giới hạn riêng ngành ngân hàng.

Hai trục nội dung chính:

1. **Java web development** — Spring ecosystem, Jakarta EE, Quarkus, Micronaut, microservices, hiệu năng JVM, bảo mật ứng dụng, JDK releases, build & runtime tools.
2. **Ứng dụng AI trong doanh nghiệp** — AI/ML cho nghiệp vụ (banking, fintech, e-commerce, logistics, healthcare, manufacturing...), agentic workflow, LLM integration trong ứng dụng Java, RAG, AI coding tools, RegTech, MLOps.

Trong đó **ngân hàng/tài chính là MỘT mục con** quan trọng (phù hợp kinh nghiệm người đọc), không phải toàn bộ phạm vi.

Output phải đọc giống analyst/research briefing, KHÔNG phải RSS feed.

---

# Scope

## Theo dõi — Java Web (core)

- Java LTS releases (JDK 21, 25, 27...) và tính năng ngôn ngữ (virtual threads, pattern matching, structured concurrency)
- Spring ecosystem: Spring Boot, Spring Framework, Spring Security, Spring AI, Spring Cloud
- Jakarta EE, Quarkus, Micronaut, Helidon
- Kiến trúc microservices, event-driven, reactive (WebFlux)
- Build & runtime: Maven, Gradle, GraalVM native image
- Hiệu năng JVM, tuning GC, observability (OpenTelemetry, Micrometer)
- Bảo mật ứng dụng web: OWASP, CVE ảnh hưởng Spring/Java
- API design: REST, gRPC, GraphQL

## Theo dõi — AI Ứng Dụng Doanh Nghiệp (rộng)

- Spring AI, LangChain4j, Google ADK for Java — tích hợp LLM vào ứng dụng Java
- AI/ML trong nghiệp vụ ngân hàng (credit scoring, fraud detection, AML) — mục con quan trọng
- AI/ML trong e-commerce, logistics, healthcare, manufacturing, HR, legal
- Agentic workflow trong doanh nghiệp (Citi, JPMorgan, SAP, Salesforce...)
- AI coding tools (Copilot, Cursor, Devin, Codex) và tác động tới developer workflow
- RAG (Retrieval-Augmented Generation), vector DB, knowledge graph
- RegTech và compliance automation
- MLOps, model serving, inference optimization cho enterprise
- Quy định AI: EU AI Act, SR 26-2 (Mỹ), SBV (Việt Nam), MAS

## Loại trừ

- Tin AI thuần túy về model capability không gắn ứng dụng doanh nghiệp (đã thuộc `ai-news`)
- Tin tài chính vĩ mô thuần tuý không liên quan công nghệ (thuộc `vn-news`)
- Frontend/mobile development (ngoài scope Java backend)

---

# Sources

## Tier 1 — Official / nguồn gốc
- Spring Blog (spring.io)
- OpenJDK / Inside.java / Oracle Java
- Quarkus.io, Micronaut docs
- Thông cáo chính thức công ty (AWS, Google Cloud, Microsoft, SAP, Salesforce...)
- Cơ quan quản lý (SBV, EU Commission, Fed/OCC)

## Tier 2 — Báo chí uy tín
- InfoQ (Java / Architecture / AI queue)
- The Register, Ars Technica (enterprise/dev)
- Reuters / Bloomberg (fintech, enterprise tech)
- TechCrunch, CNBC Tech
- VnExpress / CafeF (công nghệ doanh nghiệp)

## Tier 3 — Think tank / analyst / chuyên gia ngành
- Baeldung, Vlad Mihalcea blog, Thoughtworks Technology Radar
- Gartner / Forrester (enterprise tech)
- McKinsey / Deloitte insights (AI in enterprise)
- Martin Fowler blog, DZone

## Tier 4 — Community / social (PHẢI cross-check)
- r/java, r/SpringBoot, Hacker News
- Medium engineering blogs
- LinkedIn posts kỹ sư / kiến trúc sư

KHÔNG xem Tier 4 là fact. PHẢI cross-verify theo `core.rules.md`.

---

# Required Structure

Mỗi báo cáo PHẢI có:

1. **Tóm tắt nhanh (Ultra Brief)** — 3–5 dòng
2. **Executive Summary** — 5–15 ý quan trọng nhất
3. **Phần Java Web** — phân tích sâu (releases, security, performance, architecture)
4. **Phần AI Ứng Dụng Doanh Nghiệp** — phân tích sâu (gồm mục con banking/fintech nếu có tin)
5. **Các tin khác đáng chú ý** — bullet cho tin impact 3–4
6. **Meta Trends** — xu hướng lớn (Spring AI, agentic enterprise, inference economics, Java + AI-native...)
7. **Strategic Watchlist** — điều cần theo dõi tiếp
8. **⚠️ Nguồn không truy cập được** (nếu có)
9. **Δ Delta** — thay đổi so với báo cáo trước

Mỗi tin chính phải có: Tóm tắt → Điểm mới → Phân tích → Ảnh hưởng thực tế → Verification / Confidence / Impact Score.

---

# Language Policy

- Output mặc định: Tiếng Việt.
- Thuật ngữ tiếng Anh giữ nguyên, giải thích ngắn ở lần đầu xuất hiện.
- Tên riêng không cần giải thích.

---

# Critical Rule

Ưu tiên phân tích sâu và liên kết sự kiện hơn số lượng tin. Ít tin nhưng sâu tốt hơn nhiều tin hời hợt. Tuân thủ tuyệt đối `core.rules.md` và `memory.rules.md`.

---

# Depth Policy (BẮT BUỘC)

## Độ dài tối thiểu

| Impact | Tối thiểu | Yêu cầu |
|--------|----------|----------|
| 9–10 | 1000+ từ | Bối cảnh đầy đủ, diễn biến chi tiết, phân tích cơ chế, tác động đa chiều, kịch bản |
| 7–8 | 500+ từ | Bối cảnh + diễn biến + phân tích + tác động thực tế |
| 5–6 | 200+ từ | Ngắn gọn nhưng đủ context |

## Quy tắc

- Viết dạng ĐOẠN VĂN có narrative flow — KHÔNG chỉ bullet
- Người đọc PHẢI hiểu được tin chỉ từ báo cáo, KHÔNG cần mở link nguồn
- Mỗi tin phải có: Bối cảnh (WHY/context) → Diễn biến (WHAT) → Phân tích (SO WHAT/cơ chế) → Tác động (FOR WHOM)
- Giải thích thuật ngữ đầy đủ — đối tượng là Java dev 12+ năm nhưng không chuyên ngành tin đó
- CẤM: viết 5 bullets cho tin Impact 8, bỏ bối cảnh, copy headline không phân tích
