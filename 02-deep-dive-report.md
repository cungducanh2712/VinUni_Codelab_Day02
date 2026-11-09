# 02 — Deep-Dive Report: VinFast Service & Warranty Copilot

> **Bài toán nhóm lựa chọn:** Quick Problem Card #02 — **Chẩn đoán dịch vụ & Xử lý bảo hành (VinFast)**
>
> **Một câu:** Giảm thời gian kỹ thuật viên / cố vấn dịch vụ tra cứu tài liệu kỹ thuật và chuẩn bị hồ sơ sửa chữa, bảo hành bằng một **Service & Warranty Copilot có dẫn nguồn**.

---

## Bối cảnh

* **Quy mô mạng lưới tăng rất nhanh:** Số xưởng dịch vụ VinFast tăng gấp 3 trong một năm, từ **111 xưởng (11/2024) lên hơn 330 xưởng (10/2025)**; xưởng thứ **350** khai trương ngày 29/11/2025 và hãng đặt mục tiêu **400 xưởng**. Riêng 11 tháng năm 2025, VinFast bàn giao **147.450 xe** tại Việt Nam.
* **Nhiều dòng xe, nhiều chính sách:** Danh mục gồm VF 3, VF 5, VF 6, VF 7, VF 8, VF 9, với thời hạn bảo hành khác nhau theo nhóm xe (báo chí ghi nhận khoảng **7 năm / 160.000 km** cho VF 3–VF 6 và **10 năm / 200.000 km** cho VF 7–VF 9; pin cao áp có điều khoản riêng). Các nguồn báo chí **không thống nhất** về con số chi tiết theo phiên bản và thời điểm áp dụng.
* **Hệ quả:** Xưởng mới mở nhiều → nhiều kỹ thuật viên (KTV) mới, ít kinh nghiệm với từng dòng xe; lượng xe vào xưởng tăng theo số xe đã bán; tài liệu kỹ thuật và điều kiện bảo hành phân tán theo model / phiên bản / thời điểm.


---

## Quyết định lựa chọn của nhóm

Nhóm chọn **Card #02 — VinFast Service & Warranty Copilot** sau khi so sánh Quick Cards của các thành viên.

**Lý do chọn:**
* **Đúng "sweet spot" của LLM:** Thông tin cần tìm nằm trong tài liệu phi cấu trúc (SOP, hướng dẫn sửa chữa, bản tin kỹ thuật), phải kết hợp mô tả triệu chứng bằng ngôn ngữ tự nhiên + mã lỗi DTC + model / phiên bản. Tìm từ khóa (Ctrl+F) hoặc rule thuần không làm tốt được việc này.
* **Rủi ro kiểm soát được bằng thiết kế:** AI chỉ tìm kiếm có trích dẫn và soạn nháp; quyết định chẩn đoán, thay linh kiện và phê duyệt bảo hành vẫn do con người và rule engine đảm nhiệm.
* **Áp lực thật và đang tăng:** Mạng lưới xưởng tăng gấp 3 trong 1 năm → bài toán đào tạo và năng suất KTV mới là có thật.

---

# Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow

Quy trình xử lý một work order (WO) sửa chữa / bảo hành tại xưởng dịch vụ hiện tại *(giả thuyết, cần xác minh tại xưởng)*:

```text
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│ Bước 1           │     │ Bước 2           │     │ Bước 3           │
│ Tiếp nhận xe     │     │ Đọc mã lỗi DTC & │     │ Tra cứu SOP,     │
│ VIN, model,      │ ──→ │ kiểm tra ban đầu │ ──→ │ tài liệu sửa chữa│
│ phiên bản, triệu │ 🔄  │                  │     │ & ca tương tự    │
│ chứng            │     │                  │     │                  │
│ Ai: CVDV         │     │ Ai: KTV          │     │ Ai: KTV          │
│ ⏱ ~10 phút       │     │ ⏱ ~10 phút       │     │ ⏱ ~20 phút 🔴    │
│ In: Xe + khách   │     │ In: Máy chẩn đoán│     │ In: DTC + triệu  │
│ Out: Phiếu tiếp  │     │ Out: Danh sách   │     │ chứng + model    │
│ nhận trên DMS    │     │ DTC              │     │ Out: Hướng xử lý │
└──────────────────┘     └──────────────────┘     └──────────────────┘
                                                           │
                                                           ▼
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│ Bước 6           │     │ Bước 5           │     │ Bước 4           │
│ Bảo hành TT xét  │     │ Nhập nội dung    │     │ Kiểm tra xe, xác │
│ duyệt claim      │ ←── │ sửa chữa, lập hồ │ ←── │ định phương án & │
│                  │ 🔄  │ sơ & gửi warranty│     │ linh kiện thay   │
│                  │     │ claim            │     │                  │
│ Ai: Bảo hành     │     │ Ai: KTV / CVDV   │     │ Ai: KTV          │
│ trung tâm        │     │ ⏱ ~20 phút 🔴    │     │ ⏱ (không tính —  │
│                  │     │ In: Kết quả sửa  │     │ thao tác vật lý) │
│                  │     │ Out: WO + claim  │     │                  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
         │
         └──↩ Hồ sơ thiếu / sai → TRẢ VỀ xưởng bổ sung (rework) 🔴🔄
```

**Chú thích:** 🔴 Bottleneck · 🔄 Handoff (CVDV → KTV, KTV → Bảo hành, Bảo hành trung tâm → xưởng khi trả hồ sơ).

**Tổng thời gian tra cứu + lập hồ sơ (Bước 3 + 5) ≈ 40 phút / WO**. Thời gian sửa chữa vật lý (Bước 4) **không** tính vào metric của bài toán này.

---

## 3.2. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | **Kỹ thuật viên (KTV)** tại xưởng dịch vụ VinFast (người tra cứu và chẩn đoán); **Cố vấn dịch vụ (CVDV)** (tiếp nhận xe, lập WO); **Nhân viên bảo hành** tại xưởng (lập và gửi claim). Bên liên quan: **Bảo hành trung tâm** (xét duyệt claim), **Kỹ thuật trung tâm** (hỗ trợ ca khó). |
| **2. Current Workflow** | CVDV tiếp nhận xe (VIN, model, phiên bản, triệu chứng) → KTV đọc DTC → KTV tự tra cứu SOP / tài liệu sửa chữa / ca tương tự trên nhiều nguồn → KTV kiểm tra xe, quyết định phương án → KTV / CVDV nhập WO và lập warranty claim → Bảo hành trung tâm xét duyệt, trả lại nếu thiếu / sai. 6 bước, tra cứu và lập hồ sơ hoàn toàn thủ công, ước tính **~40 phút / WO** cho phần tra cứu + giấy tờ. |
| **3. Bottleneck** | **Bước 3 (tra cứu, ~20 phút):** tài liệu phân tán theo model / phiên bản phần mềm / thời điểm; KTV mới chưa quen dòng xe; khó tìm ca tương tự trong lịch sử WO. **Bước 5 (lập hồ sơ, ~20 phút):** nhập tay nhiều trường, dễ thiếu / sai mã linh kiện, mã lỗi, mô tả → hồ sơ bị **trả về** và phải làm lại. |
| **4. Business Impact** | *Ước lượng quy mô (giả định, cần đo):* 350 xưởng × 15 WO/ngày × 40 phút tra cứu + giấy tờ ≈ **3.500 giờ công/ngày** toàn mạng lưới. Giảm 30% ≈ **1.050 giờ công/ngày ≈ 130 KTV quy đổi toàn thời gian** được trả về cho việc sửa chữa thực tế. Ngoài ra: hồ sơ bị trả về kéo dài thời gian xe nằm xưởng, làm chậm thanh toán bảo hành cho xưởng; chẩn đoán sai dẫn tới thay linh kiện không cần thiết (tăng chi phí bảo hành) hoặc khách phải quay lại sửa lần 2. |
| **5. Success Metric** | **Hiệu quả:** Giảm **≥ 30% median** thời gian tra cứu + lập hồ sơ mỗi WO (so với nhóm đối chứng).<br>**Chất lượng hồ sơ:** Giảm **≥ 25%** tỷ lệ claim bị trả về do thiếu / sai thông tin.<br>**Độ tin cậy:** **100%** câu trả lời trong tập đánh giá có trích dẫn tài liệu đúng model / phiên bản, **hoặc** từ chối kết luận khi không đủ căn cứ.<br>**Guardrail:** First-Time Fix Rate **không thấp hơn** nhóm đối chứng; **0** claim được gửi đi mà không có người duyệt. |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** tìm kiếm trong kho tài liệu **đã phê duyệt**; tóm tắt và giải thích nội dung **trong phạm vi nguồn**, kèm trích dẫn; gợi ý ca tương tự; soạn **bản nháp** WO và warranty claim.<br>**AI TUYỆT ĐỐI KHÔNG ĐƯỢC:** (1) phê duyệt hoặc từ chối bảo hành; (2) tự quyết định thay linh kiện; (3) đưa hướng dẫn sửa chữa **không có nguồn** — đặc biệt các thao tác trên **hệ thống cao áp / pin**; (4) trả lời điều kiện bảo hành từ "trí nhớ" của mô hình; (5) dùng tài liệu **không khớp** model / phiên bản của xe.<br>**BẮT BUỘC CON NGƯỜI DUYỆT:** KTV xác nhận chẩn đoán và phương án; CVDV / nhân viên bảo hành duyệt và tự bấm gửi claim. |

---

## 3.3. AI Fit & Future-State Flow

### So sánh Rule vs LLM vs Agent cho từng tác vụ

| Tác vụ | Rule / State-Machine | LLM Feature (RAG + Structured Output) | Agentic Loop | Chọn |
|---|---|---|---|---|
| Lấy thông tin xe (model, phiên bản, lịch sử WO) theo VIN | ✅ Truy vấn DMS có cấu trúc | Không cần | Không cần | **Rule** |
| Tìm SOP / hướng dẫn theo triệu chứng tự nhiên + DTC + phiên bản | ❌ Không hiểu ngôn ngữ tự nhiên | ✅ Hiểu ngữ cảnh, trả lời kèm trích dẫn | Thừa | **LLM (RAG)** |
| Tóm tắt ca tương tự trong lịch sử | ❌ | ✅ | Thừa | **LLM** |
| Soạn nháp WO / claim từ ghi chú của KTV | ❌ Không đọc được ghi chú tự do | ✅ Output JSON theo schema | Thừa | **LLM** |
| Kiểm tra thời hạn bảo hành, mã linh kiện được bảo hành, điều khoản loại trừ, định mức, trường bắt buộc | ✅ Logic rõ ràng, phải nhất quán và kiểm toán được | ❌ Có thể "bịa" điều khoản | ❌ | **Rule engine** |
| Gửi claim / đặt linh kiện | — | — | ❌ Hành động rủi ro cao, cần người duyệt | **Con người** |

**Kết luận AI Fit: LLM Feature (RAG + Structured Output) kết hợp Rule engine — KHÔNG dùng Agent.**
* Quy trình có cấu trúc cố định, không cần AI tự lập kế hoạch nhiều bước.
* Các hành động cuối (gửi claim, thay linh kiện) có hậu quả tài chính và an toàn → không giao quyền tự hành động cho AI.
* Điều kiện bảo hành thay đổi theo dòng xe và thời điểm (chính báo chí còn ghi nhận không thống nhất) → **phải** đọc từ nguồn chính thức qua rule engine, không để LLM tự trả lời.

### Future-State Flow

```text
┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ Bước 1       │   │ ⚙️ Auto-pull  │   │ Bước 2       │   │ 🔵 RAG Copilot│
│ CVDV tiếp    │──→│ VIN → model, │──→│ KTV đọc DTC  │──→│ Tìm SOP đúng │
│ nhận xe      │   │ phiên bản,   │   │ + nhập triệu │   │ model/phiên  │
│              │   │ lịch sử WO   │   │ chứng        │   │ bản, trả lời │
│              │   │ (Rule)       │   │              │   │ KÈM TRÍCH DẪN│
└──────────────┘   └──────────────┘   └──────────────┘   └──────────────┘
                                                                │
                                                                ▼
┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ 🟢 CVDV / NV │   │ ⚙️ Rule engine│   │ 🔵 LLM draft  │   │ 🟢 KTV kiểm   │
│ bảo hành     │←──│ kiểm tra điều│←──│ WO + claim   │←──│ tra xe, XÁC  │
│ DUYỆT & tự   │   │ kiện BH, mã  │   │ (JSON theo   │   │ NHẬN chẩn    │
│ bấm gửi claim│   │ linh kiện,   │   │ schema)      │   │ đoán & phương│
│              │   │ trường bắt   │   │ [DRAFT_ONLY] │   │ án           │
│              │   │ buộc         │   │              │   │              │
└──────────────┘   └──────────────┘   └──────────────┘   └──────────────┘
```

**Chú thích:** 🔵 AI Step · 🟢 Human Step (HITL) · ⚙️ Rule / hệ thống có cấu trúc.

### Fallback

| Tình huống | Hành vi của hệ thống |
|---|---|
| Không tìm thấy tài liệu phù hợp / điểm truy xuất thấp | Trả lời **"Không đủ căn cứ"**, không đoán; gợi ý chuyển ca cho **Kỹ thuật trung tâm** |
| Tài liệu tìm được không khớp model / phiên bản của xe | **Chặn** câu trả lời, báo rõ lý do |
| Câu hỏi liên quan thao tác hệ thống cao áp / pin | Chỉ trích nguyên văn SOP an toàn đã phê duyệt; không có SOP → từ chối và chuyển Kỹ thuật trung tâm |
| Rule engine thiếu dữ liệu hoặc lỗi | Đánh dấu claim **"cần kiểm tra thủ công"**, nhân viên bảo hành kiểm tra như quy trình cũ |
| Bản nháp WO / claim sai | KTV / CVDV sửa tay trước khi duyệt; lỗi được ghi lại để cải thiện |
| Hệ thống Copilot ngừng hoạt động | Quay về quy trình thủ công hiện tại — **xưởng không bị dừng** |

---

# Phase 5 — EVALUATE

### AI Readiness Checklist

| # | Câu hỏi | Trạng thái | Bằng chứng / Ghi chú |
|---|---|---|---|
| 1 | Có sẵn dữ liệu mẫu / logs sạch để test? | ⚠️ **Chưa xác nhận** | Là nhà sản xuất, VinFast chắc chắn có SOP, tài liệu sửa chữa, danh mục DTC, lịch sử WO. Nhưng nhóm **chưa có quyền truy cập**, chưa biết định dạng (PDF scan hay văn bản), mức độ quản lý phiên bản, và lịch sử WO cần **ẩn danh** thông tin khách hàng. |
| 2 | Rủi ro khi AI sai có nằm trong tầm kiểm soát (HITL / Fallback)? | ✅ **Có** | AI không có quyền gửi claim hay quyết định thay linh kiện; 2 điểm HITL; rule engine cho điều kiện bảo hành; "citation-or-refuse". **Rủi ro còn lại:** KTV tin AI quá mức (automation bias) → giảm thiểu bằng bắt buộc hiển thị nguồn, theo dõi First-Time Fix Rate. |
| 3 | Stakeholders sẵn sàng thay đổi quy trình? | ⚠️ **Chưa xác nhận** | Chưa khảo sát KTV / CVDV. Nguy cơ: thêm một công cụ làm chậm thay vì nhanh; KTV lâu năm không tin AI. Cần nhúng Copilot vào ngay DMS hiện có. |

### Quyết định cuối cùng

- [ ] **GO** (Bắt đầu xây dựng Prototype)
- [x] **NOT YET** (Cần tích lũy thêm dữ liệu / xác lập baseline)
- [ ] **NO-GO** (Không khả thi / Rule-based tốt hơn)

### Justification

**Không phải NO-GO**, vì AI Fit rất rõ: bài toán tìm kiếm và đọc hiểu tài liệu phi cấu trúc theo ngữ cảnh là việc rule-based không làm được, và thiết kế đã tách bạch phần LLM (tìm kiếm có trích dẫn, soạn nháp) với phần rule (điều kiện bảo hành) và phần con người (chẩn đoán, phê duyệt).

**Nhưng chưa GO**, vì 3 lý do có thể kiểm chứng:
1. **Chưa có baseline:** Toàn bộ con số "~20 phút tra cứu", "~20 phút lập hồ sơ", "15 WO/ngày" là giả định của nhóm. Không có baseline thì mục tiêu "giảm ≥ 30%" và "giảm ≥ 25% claim bị trả về" không đo được.
2. **Chưa xác nhận dữ liệu:** Chất lượng RAG phụ thuộc hoàn toàn vào kho tài liệu (đúng phiên bản, có cấu trúc, được phê duyệt). Chưa thấy dữ liệu thì chưa thể cam kết metric "100% có trích dẫn đúng".
3. **Chưa xác nhận người dùng:** Chưa có khảo sát KTV / CVDV về điểm đau thực tế và sẵn sàng dùng công cụ mới.

### Kế hoạch chuyển từ NOT YET → GO (4–6 tuần)

| Tuần | Việc | Tiêu chí hoàn thành |
|---|---|---|
| 1–2 | **Đo baseline** tại 2–3 xưởng (1 xưởng lớn, 1 xưởng mới mở): bấm giờ Bước 3 và 5 trên ~100 WO; thống kê tỷ lệ claim bị trả về và lý do | Có median thời gian thật và tỷ lệ trả về thật |
| 1–3 | **Kiểm kê tài liệu** cho 1 dòng xe (đề xuất VF 3 hoặc VF 5 — số lượng lớn): định dạng, phiên bản, quyền truy cập | Kho tài liệu đã phê duyệt cho 1 dòng xe |
| 2–3 | **Phỏng vấn** 10–15 KTV / CVDV | Xác nhận đúng bottleneck, thu thập 50+ câu hỏi thật làm tập đánh giá |
| 3–6 | **Đánh giá offline:** RAG trên tập 50+ câu hỏi; thử tấn công các ranh giới ở mục 3.2 (dụ AI tự duyệt bảo hành, bỏ trích dẫn, dùng SOP sai model, bỏ bước an toàn cao áp, trả lời điều kiện bảo hành từ trí nhớ) | ≥ 95% câu trả lời có trích dẫn đúng hoặc từ chối đúng; không ranh giới nào bị vượt qua |

**Điều kiện GO:** Baseline cho thấy Bước 3 + 5 chiếm đáng kể thời gian WO **và** đánh giá offline đạt ngưỡng trên → PoC tại 1–2 xưởng, 1 dòng xe, chế độ **chỉ đọc + soạn nháp**, có nhóm đối chứng.

**Điều kiện dừng (→ NO-GO):** Baseline cho thấy tra cứu + giấy tờ chỉ chiếm phần nhỏ thời gian WO, **hoặc** kho tài liệu không đủ chất lượng để trích dẫn đáng tin cậy.

---
