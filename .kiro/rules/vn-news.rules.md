---
inclusion: auto
globs: vn-news/**
---

# Vietnam News — Module Policy

## Scope
- Công nghệ, AI, startup, ngân hàng, chứng khoán, BĐS
- Chính sách, hạ tầng, kinh tế vĩ mô, FDI, xuất nhập khẩu
- Digital transformation, energy, education, healthcare
- **Chính sách nhà nước:** Quốc hội, Chính phủ, nghị định, thông tư, luật mới, cải cách hành chính, tinh gọn bộ máy, chống tham nhũng
- **Thời sự:** sự kiện lớn trong nước, thiên tai, an ninh trật tự, ngoại giao, quan hệ quốc tế của VN
- **Xã hội:** lao động, việc làm, giáo dục, y tế, dân số, đô thị hóa

## Sources
- **General / Thời sự:** VnExpress, 24h.com.vn, Tuổi Trẻ, Thanh Niên, Dân Trí, VietnamNet, Báo Nhân Dân, VOV, VTV
- **Tech:** GenK, Tinhte, VietTimes, ICTNews
- **Finance:** CafeF, NDH, The Leader, Vietstock, BizLIVE
- **Government / Chính sách:** Chinhphu.vn, Bộ TT&TT, NHNN, GSO, Quochoi.vn, Thuvienphapluat.vn
- **Lưu ý:** Chỉ lấy nguồn tiếng Việt. KHÔNG dùng nguồn tiếng Anh (Reuters, Bloomberg, DealStreetAsia, Vietnam Briefing, etc.) cho module này. Nếu tin chỉ có nguồn tiếng Anh → bỏ qua hoặc chờ báo VN đưa tin.

## Bảng tín hiệu kinh tế (BẮT BUỘC mỗi báo cáo)
- GDP, VN-Index, lãi suất (huy động/cho vay), tỷ giá
- FDI (đăng ký/giải ngân), PMI, CPI, tín dụng
- Xuất nhập khẩu, thâm hụt/thặng dư thương mại

## Entity tracking
- Doanh nghiệp: Vingroup, FPT, VNG, Viettel, VNPT, Masan, VinFast
- Ngân hàng: Vietcombank, BIDV, VietinBank, Techcombank, MBBank, TPBank
- Chính sách: Đề án 06, Luật Công nghệ cao, quy hoạch hạ tầng

## Clustering
- Gom theo: ngành (tech/finance/BĐS/hạ tầng), chính sách, doanh nghiệp
- Track: FDI flow direction, tech workforce growth, infrastructure pipeline

## Actionable insights
- Impact cho: startup founder, developer, investor, doanh nghiệp vừa-nhỏ
- Ví dụ: chính sách mới ảnh hưởng gì, cơ hội đầu tư, rủi ro thị trường

## Ảnh hưởng liên ngành
- Lãi suất → BĐS → chứng khoán → tiêu dùng
- FDI tech → việc làm → giáo dục → lương
- Hạ tầng → logistics → xuất khẩu → GDP
- Giá dầu thế giới → CPI → chính sách tiền tệ

## Risk signals
- Lạm phát vượt mục tiêu, thanh khoản ngân hàng, nợ xấu BĐS
- Phụ thuộc FDI, supply chain concentration, thiếu hụt nhân lực tech

## Cross-module linkage (BẮT BUỘC)
Khi tin VN có liên hệ rõ với sự kiện quốc tế, PHẢI ghi section "🔗 Liên kết quốc tế":
- **World Conflicts**: giá dầu (Iran/Hormuz) → CPI, tỷ giá; chiến tranh → FDI redirect; sanctions → thương mại
- **AI News**: model release → startup VN adopt; chip supply → Intel/Samsung VN; AI policy global → chính sách VN

## Language policy
- Output: **Tiếng Việt** duy nhất.
- File: `YYYY-MM-DD.md` / `.html` (không có suffix `-vi`).
