# 📊 News Aggregator

Hệ thống tổng hợp và phân tích tin tức tự động, duy trì knowledge base dài hạn.

## Mở Dashboard

Mở `index.html` trên trình duyệt.

## Modules

### 🤖 AI News (`ai-news/`)
- Theo dõi: OpenAI, Anthropic, Google DeepMind, Meta AI, Microsoft Research, HuggingFace, arXiv
- Ưu tiên: model releases, benchmarks, agent systems, reasoning, inference optimization, open-source LLM
- Output: báo cáo Markdown + HTML (tiếng Việt & English)

### 🌍 World Conflicts (`world-conflicts/`)
- Theo dõi: Ukraine/Russia, Iran/Mỹ, Israel/Gaza/Lebanon, Trung Quốc/Đài Loan, Ấn Độ/Pakistan, NATO
- Nguyên tắc: tách biệt sự kiện xác nhận / tuyên bố / chưa xác minh / phân tích
- Cross-verify từ nhiều nguồn (Reuters, AP, BBC, ISW, CSIS)
- Output: báo cáo Markdown + HTML tiếng Việt

### 🇻🇳 Vietnam News (`vn-news/`)
- Theo dõi: công nghệ, AI, startup, ngân hàng, chứng khoán, BĐS, chính sách, hạ tầng
- Thêm: chính sách nhà nước, thời sự, xã hội, ngoại giao
- Nguồn: VnExpress, Tuổi Trẻ, CafeF, VietnamNet, Chinhphu.vn, VOV, VTV, Quochoi.vn
- Output: báo cáo Markdown + HTML tiếng Việt

### 🔬 Science & Technology (`sci-tech/`)
- Theo dõi: vật lý, sinh học, y học, năng lượng, vũ trụ, vật liệu, quantum, robotics
- Nguồn quốc tế: Nature, Science, MIT Tech Review, New Scientist, Ars Technica, SpaceNews
- Nguồn VN: VnExpress Khoa học, Tuổi Trẻ, VAST, Bộ KH&CN
- Output: báo cáo Markdown + HTML tiếng Việt

## Cấu trúc thư mục

```
news-aggregator/
├── index.html                  ← Dashboard chính
├── ai-news/
│   ├── memory/
│   │   ├── last_scan.json
│   │   └── processed_links.json
│   └── daily/
│       ├── YYYY-MM-DD.md
│       └── YYYY-MM-DD.html
├── world-conflicts/
│   ├── memory/
│   │   ├── last_scan.json
│   │   └── processed_links.json
│   └── daily/
│       ├── YYYY-MM-DD.md
│       └── YYYY-MM-DD.html
├── vn-news/
│   ├── memory/
│   │   ├── last_scan.json
│   │   └── processed_links.json
│   └── daily/
│       ├── YYYY-MM-DD.md
│       └── YYYY-MM-DD.html
├── sci-tech/
│   ├── memory/
│   │   ├── last_scan.json
│   │   └── processed_links.json
│   └── daily/
│       ├── YYYY-MM-DD.md
│       └── YYYY-MM-DD.html
└── .kiro/
    ├── rules/
    └── skills/
```

## Cách hoạt động

1. **Load Memory** — Đọc `last_scan.json` và `processed_links.json` để biết lần quét cuối và URL đã xử lý.
2. **Search** — Tìm tin mới hơn timestamp cuối, bỏ qua URL đã xử lý.
3. **Analyze** — Phân tích, cross-verify, phân loại từng tin.
4. **Report** — Tạo file `.md` và `.html` trong thư mục `daily/`.
5. **Update Memory** — Ghi lại timestamp và URLs mới vào memory.

Mỗi lần chạy chỉ xử lý tin MỚI — không trùng lặp giữa các phiên.
