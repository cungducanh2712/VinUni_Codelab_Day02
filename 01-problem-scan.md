# 01 — Problem Scan: Vinhomes (Vin Smart Future)

> **Mảng lựa chọn:** 🏢 **Vinhomes — Quản lý vận hành khu đô thị & dịch vụ cư dân.**
>
> **Bối cảnh:** Tính đến 31/12/2024, Vinhomes quản lý vận hành **30 khu đô thị tại 7 tỉnh thành với hơn 110.000 căn hộ, biệt thự, nhà phố**. Cư dân tương tác với Ban Quản lý (BQL) chủ yếu qua hotline, quầy lễ tân và ứng dụng **Vinhomes Resident** (có chức năng gửi phản ánh/góp ý tới BQL và theo dõi trạng thái xử lý). Vinhomes cũng đã thử nghiệm trợ lý ảo AI **"Hey Vinhomes"** trên app, triển khai đầu tiên tại Vinhomes Ocean Park. Theo quy định xử lý khiếu nại của Vinhomes (cập nhật 05/05/2025), yêu cầu đơn giản được xử lý trong **4 ngày làm việc**, yêu cầu phức tạp hẹn phản hồi chính thức trong **6 ngày làm việc**.
>
> ⚠️ *Các số liệu vận hành chi tiết (số phản ánh/ngày, số phút/lượt, tỷ lệ lỗi) bên dưới là **ước tính giả định** cho một khu đô thị quy mô lớn (~10.000 căn), cần xác minh bằng log thực tế của BQL trước khi Deep-Dive. Nguồn tham khảo ở cuối file.*

---

# Phase 1 — SCAN

Sử dụng **4 Lenses** quét qua hoạt động vận hành của BQL các khu đô thị Vinhomes:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinhomes** | Lặp lại | **Phân loại & điều hướng phản ánh cư dân:** Mỗi ngày CSKH đọc thủ công hàng trăm phản ánh trên app/hotline (rò nước, hỏng đèn, ồn, gửi xe, phí...) để xác định loại, tòa nhà, mức độ khẩn và chuyển đúng bộ phận (Kỹ thuật / Vệ sinh / An ninh / Kế toán). Chuyển sai bộ phận khiến phản ánh bị "đá" qua lại. |
| 2 | **Vinhomes** | Tốn thời gian | **Kiểm tra hồ sơ đăng ký thi công nội thất:** Kỹ thuật BQL đối chiếu thủ công từng hồ sơ (đơn, bản vẽ, hạng mục, danh sách thợ, cam kết) với nội quy tòa nhà. Hồ sơ thiếu/sai bị trả lại nhiều lần, cư dân phải chờ nhiều ngày mới được thi công. |
| 3 | **Vinhomes** | Pain từ người khác | **Sự cố diện rộng (mất nước, mất điện, thang máy):** Khi có sự cố, hotline và app bị "dội" hàng trăm phản ánh trùng lặp trong 30 phút đầu. CSKH trả lời từng người, thông báo chung chậm, cư dân bức xúc đăng lên group Facebook cư dân. |
| 4 | **Vinhomes** | Tốn thời gian | **Soạn phản hồi khiếu nại & đánh giá thấp:** CSKH phải đọc lịch sử, tra cứu thông tin liên quan và soạn câu trả lời lịch sự, đúng quy định cho từng khiếu nại (đặc biệt về phí, chất lượng dịch vụ), mất 10–15 phút/khiếu nại. |
| 5 | **Vinhomes** | AI có thể tốt hơn | **Trợ lý tra cứu thủ tục & nội quy theo từng khu đô thị:** Mỗi khu có nội quy, biểu phí và thủ tục riêng (đăng ký gửi xe, thẻ cư dân, chuyển đồ, đăng ký khách). Cư dân vẫn gọi hotline hỏi các câu lặp lại; cần trợ lý trả lời dựa trên đúng tài liệu của khu mình (mở rộng hướng "Hey Vinhomes"). |
| 6 | **Vinhomes** | Lặp lại | **Nhắc phí & đối soát công nợ phí quản lý/gửi xe:** Kế toán BQL đối soát sao kê với danh sách căn hộ, gửi nhắc phí hằng tháng. *(Nhận định ban đầu: bài toán dữ liệu có cấu trúc → Rule-based/ERP phù hợp hơn AI.)* |

**Độ phủ Lens:** Cả 4 lens đều được sử dụng (Lặp lại ×2, Tốn thời gian ×2, Pain từ người khác ×1, AI có thể tốt hơn ×1).

---

# Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn top 3 từ danh sách SCAN: **#1 (Phân loại phản ánh), #2 (Hồ sơ thi công nội thất), #3 (Sự cố diện rộng).**

**Lý do loại các bài toán còn lại:**
* **#4 (Soạn phản hồi khiếu nại):** Giá trị cao nhưng khiếu nại về phí/tranh chấp có rủi ro pháp lý; nên làm sau khi đã có hệ thống phân loại (#1) làm nền.
* **#5 (Trợ lý tra cứu):** Vinhomes đã có hướng "Hey Vinhomes" → trùng với sản phẩm hiện có, cần khảo sát thêm hiệu quả thực tế trước khi đề xuất.
* **#6 (Nhắc phí):** Dữ liệu có cấu trúc, logic rõ ràng → Rule-based/ERP giải quyết tốt hơn, **không cần AI**.

---

## Card #1 — Phân loại & điều hướng phản ánh cư dân

```text
┌──────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                              │
│ Bài toán: Phản ánh của cư dân trên app/hotline được phân     │
│ loại và chuyển bộ phận thủ công, chậm và hay chuyển sai.     │
│ Công ty thành viên: [x] Vinhomes                             │
│                                                              │
│ Ai đang đau? Nhân viên CSKH BQL (quá tải đọc & chuyển),      │
│ Cư dân (chờ lâu, bị hỏi lại thông tin)                       │
│                                                              │
│ Workflow thủ công hiện tại (5 bước):                         │
│   1. Cư dân gửi phản ánh (app / hotline / lễ tân)            │
│   → 2. CSKH đọc nội dung, gọi lại hỏi thêm nếu thiếu thông tin│
│   → 3. CSKH xác định loại sự việc + tòa/căn + mức độ khẩn 🔴 │
│   → 4. Chuyển phiếu cho bộ phận phụ trách 🔄                 │
│   → 5. Bộ phận xử lý & cập nhật trạng thái trên app          │
│                                                              │
│ Bước nào tốn nhất? Bước 2–3 (⏱ ~5 phút/phản ánh, ước tính    │
│ ~300 phản ánh/ngày/khu → ~25 giờ công/ngày). Phản ánh xếp    │
│ hàng chờ ~2 giờ mới được chuyển; ~15% bị chuyển sai bộ phận. │
│                                                              │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3               │
│ (LLM đọc nội dung tự do → trích xuất tòa/căn, loại sự việc,  │
│ mức độ khẩn → gợi ý bộ phận + câu hỏi bổ sung nếu thiếu)     │
│                                                              │
│ Đo thành công bằng gì (Metric có số)?                         │
│ • Thời gian từ lúc nhận → giao đúng bộ phận: ~2 giờ → <10 phút│
│ • Độ chính xác phân loại bộ phận ≥ 90% (đo trên 500 phiếu cũ) │
│ • Tỷ lệ chuyển sai bộ phận: ~15% → <5%                       │
│                                                              │
│ Ranh giới: AI chỉ GỢI Ý phân loại; phiếu độ tin cậy thấp     │
│ hoặc gắn "khẩn cấp" (cháy, rò điện, kẹt thang máy) → bắt buộc│
│ CSKH xác nhận và chuyển hotline an ninh ngay.                │
│                                                              │
│ Quick Architecture: [ ] No AI [ ] Rule [x] LLM [ ] Agent     │
└──────────────────────────────────────────────────────────────┘
```

---

## Card #2 — Kiểm tra sơ bộ hồ sơ đăng ký thi công nội thất

```text
┌──────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│                                                              │
│ Bài toán: Hồ sơ đăng ký thi công nội thất được kiểm tra thủ  │
│ công, hay thiếu/sai, bị trả lại nhiều lần làm chậm thi công. │
│ Công ty thành viên: [x] Vinhomes                             │
│                                                              │
│ Ai đang đau? Kỹ sư kỹ thuật BQL (kiểm tra lặp lại),          │
│ Cư dân mới nhận nhà (chờ duyệt, đi lại bổ sung nhiều lần)    │
│                                                              │
│ Workflow thủ công hiện tại (5 bước):                         │
│   1. Cư dân nộp hồ sơ (đơn, bản vẽ, hạng mục, DS thợ, cam kết)│
│   → 2. Lễ tân tiếp nhận, chuyển cho Kỹ thuật 🔄              │
│   → 3. Kỹ sư đối chiếu từng mục với nội quy & checklist 🔴    │
│   → 4. Trả hồ sơ yêu cầu bổ sung (nếu thiếu/vi phạm) 🔄      │
│   → 5. Kỹ sư phê duyệt, cấp giấy phép thi công               │
│                                                              │
│ Bước nào tốn nhất? Bước 3–4 (⏱ ~30–45 phút/hồ sơ, ước tính   │
│ ~40% hồ sơ bị trả lại ít nhất 1 lần; tổng thời gian duyệt    │
│ 3–5 ngày làm việc).                                          │
│                                                              │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                 │
│ (Rule kiểm tra đủ giấy tờ bắt buộc; LLM đọc mô tả hạng mục   │
│ thi công → đánh dấu hạng mục có nguy cơ vi phạm nội quy như  │
│ đục tường chịu lực, thay đổi mặt đứng, di dời ống kỹ thuật → │
│ draft danh sách "cần bổ sung" gửi cư dân 1 lần duy nhất)     │
│                                                              │
│ Đo thành công bằng gì (Metric có số)?                         │
│ • Thời gian kiểm tra sơ bộ: ~30–45 phút → <5 phút/hồ sơ      │
│ • Tỷ lệ hồ sơ bị trả lại lần 2 trở lên: ~40% → <15%          │
│ • Recall phát hiện hạng mục vi phạm ≥ 95% (không bỏ sót)     │
│                                                              │
│ Ranh giới: AI KHÔNG được phê duyệt hay từ chối hồ sơ; chỉ    │
│ tạo bản kiểm tra nháp. Kỹ sư bắt buộc ký duyệt cuối cùng     │
│ (an toàn kết cấu tòa nhà).                                   │
│                                                              │
│ Quick Architecture: [ ] No AI [x] Rule [x] LLM [ ] Agent     │
│ (Rule cho checklist giấy tờ + LLM cho mô tả hạng mục)        │
└──────────────────────────────────────────────────────────────┘
```

---

## Card #3 — Xử lý phản ánh trùng lặp khi có sự cố diện rộng

```text
┌──────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│                                                              │
│ Bài toán: Khi mất nước/mất điện/hỏng thang máy, BQL nhận     │
│ hàng trăm phản ánh trùng, trả lời từng người và ra thông báo │
│ chung chậm, khiến cư dân bức xúc.                            │
│ Công ty thành viên: [x] Vinhomes                             │
│                                                              │
│ Ai đang đau? Cư dân (không biết tình hình, thời gian khắc    │
│ phục), CSKH (quá tải trả lời lặp lại), Trưởng ca BQL         │
│                                                              │
│ Workflow thủ công hiện tại (5 bước):                         │
│   1. Sự cố xảy ra → cư dân đồng loạt gọi hotline / gửi app   │
│   → 2. CSKH tiếp nhận & trả lời từng phản ánh 🔴              │
│   → 3. CSKH báo Kỹ thuật; Kỹ thuật kiểm tra phạm vi 🔄       │
│   → 4. Trưởng ca soạn thông báo chung 🔴                     │
│   → 5. Đăng thông báo lên app / loa / group cư dân           │
│                                                              │
│ Bước nào tốn nhất? Bước 2 & 4 (⏱ thông báo đầu tiên mất      │
│ ~45–60 phút sau sự cố; ước tính 200–400 phản ánh trùng/sự cố)│
│                                                              │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 4             │
│ (LLM gom cụm phản ánh cùng loại sự cố + cùng tòa trong cửa sổ│
│ thời gian → cảnh báo "sự cố diện rộng" cho trưởng ca → draft │
│ thông báo chung + câu trả lời mẫu cho các phản ánh trùng)    │
│                                                              │
│ Đo thành công bằng gì (Metric có số)?                         │
│ • Thời gian phát thông báo đầu tiên: ~45–60 phút → <10 phút  │
│ • Giảm ≥ 70% số phản ánh trùng phải trả lời thủ công         │
│ • Độ chính xác gom cụm ≥ 90% (đo lại trên log các sự cố cũ)  │
│                                                              │
│ Ranh giới: AI KHÔNG được tự công bố thời gian khắc phục khi  │
│ Kỹ thuật chưa xác nhận; mọi thông báo chung phải được Trưởng │
│ ca duyệt trước khi đăng ([DRAFT_ONLY]).                      │
│                                                              │
│ Quick Architecture: [ ] No AI [ ] Rule [x] LLM [ ] Agent     │
└──────────────────────────────────────────────────────────────┘
```

---

# Đề xuất mang vào họp nhóm

**Đề xuất cá nhân:** Chọn **Card #1 — Phân loại & điều hướng phản ánh cư dân** để Deep-Dive.

* **Tần suất cao, dữ liệu sẵn có:** Phản ánh phát sinh hằng ngày và đã được lưu trên app Vinhomes Resident → có log để test và đo baseline.
* **Rủi ro kiểm soát được:** AI chỉ gợi ý phân loại, CSKH vẫn xác nhận (HITL); phân loại sai chỉ làm chậm chứ không gây nguy hiểm trực tiếp như Card #2 (an toàn kết cấu).
* **Làm nền cho các bài toán khác:** Hệ thống phân loại là tiền đề cho Card #3 (gom cụm sự cố) và bài toán #4 (soạn phản hồi khiếu nại).
* **LLM Feature là đủ:** Không cần Agent vì quy trình có cấu trúc cố định; Rule-based thuần (từ khóa) không xử lý được mô tả tự do như *"nhà em nước nhỏ giọt từ trần nhà vệ sinh xuống"* (→ Kỹ thuật, rò rỉ từ căn tầng trên).
