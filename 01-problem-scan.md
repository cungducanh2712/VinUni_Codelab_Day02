# 01 — Problem Scan & Quick Problem Cards (Vin Smart Future — Vinmec Edition)

> **Dự án:** Lab 02 — AI Product Scoping (Vin Smart Future)  
> **Thành viên thực hiện:** Cung Đức Anh (Branch: `cungducanh`)  
> **Đơn vị công tác:** Khối Công nghệ Vin Smart Future — Phối hợp Khối Vận hành Hệ thống Y tế Vinmec  

---

## 🏛️ Bối cảnh: Tôi là ai?

Tôi là **Cung Đức Anh**, AI Product Engineer tại **Vin Smart Future (Vingroup)**. Trong đợt sáp nhập công nghệ toàn tập đoàn, tôi được phân công phụ trách khảo sát, scoping và đánh giá cơ hội ứng dụng Trí tuệ Nhân tạo (AI) vào chuỗi vận hành của **Hệ thống Y tế Đa khoa Quốc tế Vinmec**.

Y tế là một ngành đặc thù với tính chất "Zero-Error Tolerance" (Dung sai sai sót bằng 0) và yêu cầu bảo mật dữ liệu y tế nghiêm ngặt (HIPAA / Chuẩn JCI). Sau khi khảo sát thực địa tại Bệnh viện Đa khoa Quốc tế Vinmec Times City và phỏng vấn trực tiếp các Bác sĩ điều trị, Dược sĩ lâm sàng và Trưởng phòng Giám định Bảo hiểm, tôi nhận thấy có rất nhiều điểm nghẽn hành chính - giấy tờ đang tiêu tốn hàng nghìn giờ làm việc quý báu của đội ngũ y bác sĩ mỗi tháng.

Dưới đây là kết quả quét cơ hội (Phase 1) và phân tích nhanh 3 bài toán khả thi nhất (Phase 2).

---

# 🔍 Phase 1 — SCAN: Bảng Quét Cơ Hội Vận Hành Vinmec (4 Lenses)

Sử dụng **4 Lenses** chuẩn của Vin Smart Future để rà soát toàn bộ chuỗi tiếp nhận, chẩn đoán, điều trị và xuất viện tại Vinmec:

| # | Subsidiary | Lens | Mô tả ngắn bài toán | Đối tượng chịu đau (Actor) |
|---|------------|:---:|---------------------|----------------------------|
| **1** | **Vinmec** | ⏱ **Tốn thời gian** | **Soạn thảo Tóm tắt Bệnh án Xuất viện (Discharge Summary Drafting):** Bác sĩ mất 20–30 phút/bệnh nhân để tổng hợp thủ công kết quả xét nghiệm, chẩn đoán hình ảnh, diễn biến điều trị và phác đồ thuốc ra viện từ EHR thành văn bản tóm tắt song ngữ cho bệnh nhân và bảo hiểm. | Bác sĩ điều trị nội trú |
| **2** | **Vinmec** | 🔁 **Lặp lại** | **Gợi ý mã hóa ICD-10 & Rà soát hồ sơ quyết toán BHYT/Bảo lãnh viện phí:** Nhân viên đọc thủ công từng bệnh án để đối chiếu triệu chứng, phẫu thuật và chỉ định lâm sàng với hàng nghìn mã ICD-10 và quy định bảo hiểm; sai sót gây chậm trễ thanh toán và xuất toán viện phí ~5–8%. | Nhân viên Mã hóa & Giám định BHYT |
| **3** | **Vinmec** | 💔 **Pain từ người khác** | **Phân loại triệu chứng ban đầu & Điều hướng chuyên khoa đặt hẹn (Triage & Routing):** Bệnh nhân mô tả triệu chứng mơ hồ qua app/tổng đài; nhân viên tiếp đón không có chuyên môn y khoa dễ định hướng sai chuyên khoa (Tim mạch vs Tiêu hóa), làm bệnh nhân phải khám lại, bức xúc và lãng phí thời gian của bác sĩ. | Bệnh nhân & Tổng đài viên |
| **4** | **Vinmec** | 💡 **AI có thể tốt hơn** | **Cảnh báo tương tác thuốc & Dị ứng lâm sàng theo ngữ cảnh bệnh nhân:** Hệ thống EHR cũ bật cảnh báo tĩnh tràn lan (>80% là tương tác nhẹ vô hại), gây hiện tượng "mệt mỏi vì cảnh báo" (Alert Fatigue) khiến bác sĩ có thói quen bấm "Bỏ qua" và có nguy cơ bỏ sót tương tác thuốc nguy kịch thật sự. | Bác sĩ kê đơn & Dược sĩ lâm sàng |
| **5** | **Vinmec** | ⏱ **Tốn thời gian** | **Diễn giải kết quả khám sức khỏe tổng quát bằng ngôn ngữ bình dân:** Bệnh nhân nhận file kết quả khám định kỳ 15–20 trang với các chỉ số sinh hóa khó hiểu, liên tục gọi hotline hỏi dồn dập trong khi bác sĩ tư vấn không đủ thời gian giải thích chi tiết cho từng người. | Khách hàng gói khám VIP & CSKH |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards Tiềm Năng Nhất

Từ 5 bài toán trên, tôi chọn **Top 3 bài toán** có giá trị kinh tế cao, tần suất lớn và ranh giới an toàn kiểm soát tốt nhất để hoàn thiện 3 Thẻ Đánh Giá Nhanh:

---

## 📇 QUICK PROBLEM CARD #1: Soạn Thảo Tóm Tắt Bệnh Án Xuất Viện

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                       │
│                                                                             │
│ Bài toán (1 câu): Tự động trích xuất dữ liệu cận lâm sàng và phác đồ từ     │
│ EHR để soạn thảo bản nháp Tóm tắt Xuất viện (Discharge Summary) chuẩn hóa. │
│ Công ty thành viên: [x] Vinmec                                              │
│                                                                             │
│ Ai đang đau (Actor)? Bác sĩ điều trị nội trú (bị quá tải giấy tờ hành chính)│
│                                                                             │
│ Workflow thủ công hiện tại (5 bước):                                        │
│   1. Mở EHR bệnh nhân, rà soát lịch sử điều trị và nhật ký theo dõi hàng ngày│
│   ──> 2. Mở tab Xét nghiệm & CĐHA (CT/MRI) lọc ra các chỉ số bất thường     │
│   ──> 3. Tự gõ tóm tắt chẩn đoán vào viện, quá trình điều trị, phẫu thuật  │
│   ──> 4. Nhập danh mục thuốc ra viện, lời dặn dinh dưỡng và hẹn tái khám    │
│   ──> 5. In bản nháp, đọc soát lại lỗi chính tả và ký tên bàn giao hồ sơ    │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 18-20 phút/bệnh nhân).       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4                         │
│ (AI trích xuất tự động dữ liệu EHR -> Sinh bản nháp Discharge Summary).     │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│   - Giảm thời gian soạn tóm tắt từ 25 phút ──> dưới 4 phút/bệnh nhân.       │
│   - Tỷ lệ thông tin lâm sàng chính xác đạt 99% sau bước Bác sĩ duyệt (HITL).│
│                                                                             │
│ Quick Architecture: [x] LLM Feature (Structured Extraction + Human Review)  │
└─────────────────────────────────────────────────────────────────────────────┘
```

* **Tại sao bài toán này khả thi?** Dữ liệu trong hệ thống bệnh án điện tử (EHR) của Vinmec đã được số hóa tương đối hoàn chỉnh. Tác vụ tóm tắt văn bản là thế mạnh tuyệt đối của LLM, đồng thời quy trình có bước Bác sĩ bắt buộc phải ký số xác nhận (HITL) nên kiểm soát được 100% rủi ro y khoa.

---

## 📇 QUICK PROBLEM CARD #2: Tự Động Gợi Ý Mã Hóa ICD-10 & Rà Soát Bảo Hiểm Viện Phí

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                       │
│                                                                             │
│ Bài toán (1 câu): Đọc hiểu chẩn đoán lâm sàng để tự động đề xuất mã ICD-10   │
│ chính xác và cảnh báo thiếu chứng từ thanh toán BHYT/Bảo lãnh viện phí.     │
│ Công ty thành viên: [x] Vinmec                                              │
│                                                                             │
│ Ai đang đau (Actor)? Chuyên viên Giám định Bảo hiểm & Coder bệnh án Vinmec │
│                                                                             │
│ Workflow thủ công hiện tại (5 bước):                                        │
│   1. Nhận hồ sơ bệnh án đã đóng sau khi bệnh nhân ra viện                   │
│   ──> 2. Đọc dò từng dòng kết luận lâm sàng, thủ thuật phẫu thuật thực hiện │
│   ──> 3. Tra từ điển danh mục ICD-10 và quy định điều khoản bảo hiểm        │
│   ──> 4. Gõ mã ICD-10 chính/phụ và tải chứng từ viện phí lên cổng bảo hiểm  │
│   ──> 5. Rà soát giải trình nếu cơ quan bảo hiểm từ chối thanh toán/xuất toán│
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 12-15 phút/hồ sơ).           │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3                            │
│ (AI phân tích văn bản bệnh án -> Đề xuất Top 3 mã ICD-10 kèm trích dẫn).    │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│   - Giảm thời gian mã hóa hồ sơ từ 15 phút ──> dưới 3 phút/hồ sơ.           │
│   - Giảm tỷ lệ hồ sơ bị cơ quan bảo hiểm từ chối/xuất toán từ 7.5% ──> < 1.5%│
│                                                                             │
│ Quick Architecture: [x] LLM Feature (Clinical NLP Entity Extraction & Match)│
└─────────────────────────────────────────────────────────────────────────────┘
```

* **Tại sao bài toán này khả thi?** Danh mục ICD-10 và thông tư thanh toán BHYT là tập quy tắc chuẩn hóa cao. LLM đọc hiểu ngữ cảnh tiếng Việt trong bệnh án tốt hơn nhiều so với hệ thống tìm kiếm từ khóa thông thường, giúp bảo vệ dòng doanh thu viện phí của Vinmec.

---

## 📇 QUICK PROBLEM CARD #3: Phân Loại Triệu Chứng & Điều Hướng Đặt Hẹn Thông Minh

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                       │
│                                                                             │
│ Bài toán (1 câu): Phân tích triệu chứng ban đầu của khách hàng để gợi ý đúng │
│ chuyên khoa khám và sàng lọc sớm dấu hiệu cấp cứu (Red Flags) qua App/Tổng đài│
│ Công ty thành viên: [x] Vinmec                                              │
│                                                                             │
│ Ai đang đau (Actor)? Khách hàng đặt khám & Tổng đài viên CSKH Vinmec        │
│                                                                             │
│ Workflow thủ công hiện tại (5 bước):                                        │
│   1. Khách hàng gọi tổng đài hoặc nhắn tin qua App mô tả tình trạng ốm/đau  │
│   ──> 2. Tổng đài viên hỏi thăm triệu chứng (thiếu kiến thức lâm sàng sâu)  │
│   ──> 3. Tra cứu danh sách bác sĩ và chuyên khoa còn trống lịch             │
│   ──> 4. Chọn chuyên khoa theo phán đoán chủ quan và chốt giờ hẹn           │
│   ──> 5. Khách đến viện khám, nếu sai khoa thì bác sĩ phải viết giấy chuyển  │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 4 (⏱ 6-8 phút; tỉ lệ nhầm 15-20%) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3                            │
│ (Hỏi đào sâu theo cây triệu chứng -> Lọc cờ đỏ cấp cứu -> Điều hướng khoa). │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│   - Rút ngắn thời gian tư vấn đặt hẹn từ 7 phút ──> dưới 2 phút.            │
│   - Tăng độ chính xác phân luồng đúng chuyên khoa từ 78% ──> trên 96%.      │
│   - 100% trường hợp có dấu hiệu cấp cứu (Red Flags) được cảnh báo tức thì. │
│                                                                             │
│ Quick Architecture: [x] LLM + Rule Guardrail (Hybrid Decision Support)     │
└─────────────────────────────────────────────────────────────────────────────┘
```

* **Tại sao bài toán này khả thi?** Giải quyết bài toán chạm trực tiếp vào trải nghiệm khách hàng (Customer Journey), giảm tải áp lực cho Call-center Vinmec vào khung giờ cao điểm và ngăn chặn việc lãng phí tài nguyên bác sĩ chuyên khoa đầu ngành.

---

## ⚖️ Tiêu Chí So Sánh & Lựa Chọn Cho Bước Deep-Dive Tiếp Theo

| Tiêu chí đánh giá | Card #1: Discharge Summary | Card #2: ICD-10 Coding | Card #3: Triage & Routing |
| :--- | :---: | :---: | :---: |
| **Giá trị tiết kiệm thời gian (ROI)** | ⭐⭐⭐⭐⭐ (Cắt 20 min/bệnh nhân) | ⭐⭐⭐⭐ (Cắt 12 min/hồ sơ) | ⭐⭐⭐⭐ (Cắt 5 min/cuộc gọi) |
| **Độ sẵn sàng của dữ liệu (Data Readiness)**| ⭐⭐⭐⭐⭐ (EHR đã có sẵn tại viện) | ⭐⭐⭐⭐ (Cần mapping từ điển ICD-10)| ⭐⭐⭐⭐ (Logs chat & cuộc gọi tổng đài)|
| **Độ an toàn / Ranh giới y tế** | Rất cao (Bác sĩ bắt buộc duyệt) | Cao (Chuyên viên BHYT duyệt) | Trung bình (Cần guardrail cấp cứu) |
| **Tính biểu diễn đồ án (Lab Fit)** | **Xuất sắc (Tương đồng chuẩn Vingroup)** | Tốt | Tốt |

