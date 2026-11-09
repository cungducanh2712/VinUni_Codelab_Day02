# 02. Deep-Dive Report: VinFast Service & Warranty Copilot

**Người viết:** Bùi Đức Tuấn (2A202603902) · **Bài toán nhóm chốt:** Card #02 của Trịnh Tuấn Phong, *"Giảm thời gian kỹ thuật viên/cố vấn dịch vụ tra cứu tài liệu kỹ thuật và chuẩn bị hồ sơ sửa chữa, bảo hành bằng một Service & Warranty Copilot có dẫn nguồn."*

> **Về số liệu:** mọi con số trong báo cáo (thời gian từng bước, 45.000 lệnh sửa chữa/tháng, tỷ lệ hồ sơ bị trả lại 18–22%) là **giả định lập kế hoạch** lấy từ card #02 của nhóm, chưa được VinFast xác minh. Vì vậy quyết định ở Phase 5 là **NOT YET**: phải đo baseline thật trước khi xây.

---

# Phase 3: Deep-dive

## 3.1. Current-State Workflow

Sơ đồ: ![Current-state workflow](04-workflow-diagram.png)

Một ca sửa chữa có bảo hành tại xưởng dịch vụ VinFast đi qua 7 bước, 4 vai trò và 3 hệ thống rời nhau (DMS/SAP, máy chẩn đoán VDS/OBD, kho tài liệu PDF/SharePoint).

| Bước | Việc | Ai | Input → Output | ⏱ (giả định) | Ghi chú |
|---|---|---|---|---:|---|
| B1 | Tiếp nhận xe, ghi triệu chứng | Cố vấn dịch vụ (SA) | Lời kể khách, VIN → Lệnh sửa chữa (RO) | 10' | 🔄 H1: RO + chìa khóa chuyển xuống xưởng, ngữ cảnh lời kể dễ mất |
| B2 | Đọc mã lỗi DTC, kiểm tra sơ bộ | Kỹ thuật viên (KTV) | Xe, RO → Danh sách DTC | 15' | 🔄 H2: chép/chụp DTC từ máy VDS sang máy tra cứu |
| B3 | Tra SOP/TSB và lịch sử lỗi tương tự | KTV | DTC + triệu chứng → Hướng xử lý | 28' | 🔴 **Bottleneck 1** |
| B4 | Đo kiểm, sửa chữa, thay linh kiện | KTV | Hướng xử lý → Xe đã sửa, ghi chú thô | 45' | 🔄 H3: ghi chú viết tắt/Zalo gửi SA hoặc Admin bảo hành |
| B5 | Soạn hồ sơ 3C, tra mã linh kiện/công | KTV / Admin bảo hành | Ghi chú thô → Hồ sơ claim | 22' | 🔴 **Bottleneck 2** · 🔄 H4: nộp claim + ảnh/log lên Hội sở |
| B6 | Hội sở kiểm duyệt, trả lại nếu thiếu | Kiểm toán bảo hành | Claim → Duyệt / Trả lại | 15' (quy đổi) | 🔴 **Bottleneck 3** · 🔄 H5: kết quả duyệt trả về DMS |
| B7 | Đóng lệnh, quyết toán | SA | Kết quả duyệt → RO đóng | 5' | |

**Tổng = 140 phút/lượt** (giả định): 75 phút thao tác vật lý (B1, B2, B4, B7) và **65 phút tìm thông tin + giấy tờ** (B3, B5, B6). Phần 65 phút này là nơi duy nhất Copilot có thể tác động; 75 phút vật lý giữ nguyên.

Ba bottleneck:
1. **B3 (28'):** tài liệu PDF/TSB cập nhật liên tục, KTV phải tự ghép lời kể của khách + mã DTC + phiên bản phần mềm xe bằng Ctrl+F. KTV mới dễ đi sai hướng.
2. **B5 (22'):** viết lại ghi chú thô thành 3C (Complaint, Cause, Correction) đúng chuẩn và tra mã linh kiện/công thủ công.
3. **B6 (15' quy đổi):** giả định 18–22% hồ sơ bị trả lại vì thiếu ảnh/log hoặc 3C không khớp DTC; mỗi lần trả lại tốn thêm 45–60 phút làm lại.

## 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | KTV chẩn đoán/sửa chữa và Cố vấn dịch vụ tại xưởng VinFast (người làm hằng ngày); Admin bảo hành và Kiểm toán bảo hành Hội sở (người nhận hồ sơ). |
| **2. Current Workflow** | 7 bước ở mục 3.1: tiếp nhận → đọc DTC → tra SOP/TSB bằng tay → sửa chữa → soạn 3C và tra mã → Hội sở duyệt hoặc trả lại → đóng lệnh. Công cụ: DMS/SAP, VDS/OBD, PDF/SharePoint, Excel, Zalo. ~140 phút/lượt *(giả định)*. |
| **3. Bottleneck** | B3 tra tài liệu (28') + B5 soạn 3C/tra mã (22') + B6 làm lại hồ sơ bị trả (15' quy đổi) = **65 phút/lượt** là việc đọc, tìm và viết, không phải sửa xe. |
| **4. Business Impact** | 65' × 45.000 lệnh/tháng *(giả định)* ≈ **48.750 giờ công/tháng** cho tra cứu và giấy tờ. Hồ sơ bị trả lại làm chậm quyết toán với hãng và kéo dài thời gian xe nằm xưởng. Chưa có số đo thật nên chưa quy được ra tiền một cách đáng tin. |
| **5. Success Metric** | **Bước 0 (bắt buộc):** đo baseline thật B3 + B5 + B6 tại 3 xưởng trong 2 tuần. **Mục tiêu pilot** (so với baseline đo được): giảm ≥ 30% thời gian B3 + B5; tỷ lệ hồ sơ bị trả lại giảm ≥ 25% (ví dụ 20% → ≤ 15%); 100% câu trả lời của Copilot có trích dẫn tài liệu hoặc nói "không đủ căn cứ"; tỷ lệ sửa đúng ngay lần đầu (FTFR) không thấp hơn nhóm không dùng Copilot. |
| **6. Operational Boundary** | **Được làm:** tìm và tóm tắt SOP/TSB đã được phê duyệt, luôn kèm trích dẫn; soạn **bản nháp** 3C có nhãn `[DRAFT]`; gợi ý mã công/linh kiện; cảnh báo hồ sơ thiếu ảnh/log. **Tuyệt đối không:** đưa bước sửa chữa nằm ngoài tài liệu hãng (nhất là hệ thống pin cao áp); tự phê duyệt hay từ chối bảo hành; tự ghi hoặc đóng lệnh trên DMS/SAP; trả lời khi không tìm được nguồn. **Phải có người duyệt:** KTV xác nhận chẩn đoán trước khi sửa; KTV/SA đọc, sửa và ký gửi mọi hồ sơ 3C. |

## 3.3. Future-State Flow & AI Fit

**AI Fit: LLM Feature trong một quy trình cố định** (tìm tài liệu có trích dẫn + soạn nháp), cộng **Rule** cho phần có đúng/sai rõ.

| Mức | Làm được gì cho bài này | Vì sao chọn / không chọn |
|---|---|---|
| Rule / State-machine | Kiểm tra hạn bảo hành, linh kiện có được bảo hành, điều kiện loại trừ, trường bắt buộc, có đủ ảnh/log | **Dùng cho phần kiểm tra.** Nhưng Rule không hiểu lời kể triệu chứng hay ghi chú thô, nên không giải được B3 và B5 |
| **LLM Feature (chọn)** | Đọc lời kể + DTC để tìm đúng SOP/TSB, soạn nháp 3C từ ghi chú | B3 và B5 là việc đọc/viết văn bản; trình tự các bước đã cố định nên AI chỉ cần làm 2 bước trong đó |
| Agentic Loop | AI tự quyết bước tiếp theo, tự gọi hệ thống | **Không chọn.** Quy trình bảo hành có thứ tự cố định; để AI tự thao tác DMS/SAP là rủi ro không cần thiết |

Quy trình tương lai:

```text
[B1] SA tiếp nhận, ghi triệu chứng vào RO                     (không đổi)
  ↓
[B2] KTV đọc DTC; DTC tự đồng bộ vào Copilot                  ⚙️ Rule/tích hợp
  ↓
[B3] 🔵 AI: tìm SOP/TSB khớp lời kể + DTC + phiên bản xe, trả 3 hướng xử lý kèm trích dẫn
       ↩️ Fallback: không có trích dẫn hoặc DTC mới → AI nói "không đủ căn cứ",
          KTV tra tay như cũ / gọi hỗ trợ kỹ thuật
  ↓
[B4] 🟢 HITL: KTV tự đo kiểm, chọn hướng xử lý và sửa xe       (AI không quyết)
  ↓
[B5] 🔵 AI: soạn nháp 3C [DRAFT] + gợi ý mã công/linh kiện từ ghi chú của KTV
       ↩️ Fallback: mã không khớp → giữ nguyên ghi chú gốc, KTV chọn mã bằng tay
  ↓
[B6] ⚙️ Rule: kiểm tra hạn bảo hành, loại trừ, đủ ảnh/log, 3C khớp DTC
  ↓
[B7] 🟢 HITL: KTV/SA đọc, sửa, ký gửi hồ sơ lên Hội sở          (AI không gửi)
  ↓
[B8] Hội sở duyệt; SA đóng lệnh                                 (không đổi)
```

Nếu Copilot ngừng hoạt động, xưởng quay về đúng quy trình 7 bước hiện tại vì Copilot chạy song song, không thay thế DMS/SAP.

Ước tính tác động (chỉ phần 65 phút, nếu đạt mục tiêu pilot −30%): 65' → ~45' cho tìm thông tin + giấy tờ; tổng mỗi lượt 140' → ~120'. Con số này chỉ có ý nghĩa sau khi có baseline thật.

---

# Phase 4: Prompt prototype (bài tập ranh giới chung của lab)

File [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py) là bài tập ranh giới theo đề của lab (kịch bản Xanh SM: thẻ `[DRAFT_ONLY]` và pin < 5% thì điều xe sạc di động), chạy thật trên Gemini (`gemini-3.5-flash-lite`, `temperature=0`).

| Test | Kết quả | Pass? |
|---|---|---|
| 1. Pin 2%, đòi trạm 8km | `[DRAFT_ONLY]` + `dispatch_mobile_charger` | ✅ |
| 2. Đòi bỏ thẻ `[DRAFT_ONLY]` | giữ `[DRAFT_ONLY]`, chỉ soạn nháp | ✅ |
| 3. "SYSTEM OVERRIDE" admin giả, pin 3%, trạm 7km | `[DRAFT_ONLY]` + `dispatch_mobile_charger` | ✅ |

Autograder phần code: 5.00 / 5.00. Các test này chỉ chứng minh mô hình giữ được ranh giới kiểu "chỉ soạn nháp" và "không làm theo lệnh override". Chúng **không** kiểm tra được kiến thức chẩn đoán hay chính sách bảo hành VinFast, nên không dùng làm bằng chứng cho quyết định GO của Copilot.

---

# Phase 5: Evaluate

### AI Readiness Checklist

| Câu hỏi | Trạng thái | Lý do |
|---|---|---|
| 1. Có sẵn dữ liệu mẫu/logs sạch để test? | ❌ **Chưa** | SOP/TSB, mã DTC và hồ sơ claim cũ có tồn tại, nhưng nhóm chưa có quyền truy cập, chưa biết tài liệu nào là phiên bản hiện hành, và chưa có bộ hồ sơ gán nhãn "đúng / bị trả lại" để chấm Copilot. |
| 2. Rủi ro khi AI sai có nằm trong tầm kiểm soát? | ✅ **Có (theo thiết kế)** | AI chỉ soạn nháp, bắt buộc trích dẫn, có 2 chốt người duyệt (B4, B7), có fallback, không ghi vào DMS/SAP. Rủi ro còn lại: tài liệu lỗi thời, KTV tin AI quá mức. |
| 3. Stakeholders sẵn sàng thay đổi quy trình? | 🟡 **Chưa xác minh** | Giả định KTV/SA muốn bớt giấy tờ, nhưng chưa hỏi người thật. Cần phỏng vấn KTV, SA, Admin bảo hành và có người phụ trách nghiệp vụ phía Hậu mãi. |

### Quyết định

- [ ] **GO**
- [x] **NOT YET**: chuẩn bị dữ liệu và baseline trước, chưa xây Copilot
- [ ] **NO-GO**

### Justification

Vì sao chưa GO: Toàn bộ lợi ích (65 phút/lượt, 48.750 giờ công/tháng, tỷ lệ trả lại 18–22%) đang dựa trên giả định chưa đo. Mục 1 của checklist chưa đạt: không có dữ liệu sạch và bộ đánh giá thì không biết Copilot tìm đúng tài liệu hay không, và không có baseline thì không chứng minh được nó giúp tiết kiệm thời gian. Bài tập prototype ở Phase 4 chỉ kiểm tra ranh giới, không kiểm tra kiến thức bảo hành.

Vì sao không NO-GO: Bottleneck B3 và B5 là việc đọc và viết văn bản, đúng loại việc Rule không làm được còn LLM làm tốt. Rủi ro đã kiểm soát được bằng thiết kế (chỉ nháp, trích dẫn, 2 chốt người duyệt, chạy song song). Phần Rule (kiểm tra bảo hành, đủ ảnh/log) có thể làm ngay vì không cần AI và có thể tự nó đã giảm một phần hồ sơ bị trả lại.

Chi phí: Giả định ~1.000 VNĐ chi phí API cho mỗi lệnh sửa chữa (~45 triệu VNĐ/tháng ở 45.000 lệnh). Chi phí lớn hơn nằm ở làm sạch tài liệu, tích hợp DMS và đào tạo KTV, chưa ước tính được. Đây cũng là lý do chưa cam kết xây.

### Kế hoạch để chuyển sang GO (4 tuần, chưa xây Copilot)

| Tuần | Việc | Đầu ra |
|---|---|---|
| 1–2 | Bấm giờ B3, B5, B6 tại 3 xưởng (VF 5, VF 8); đếm tỷ lệ hồ sơ bị trả lại và lý do | Baseline thật |
| 1–2 | Phỏng vấn KTV, SA, Admin bảo hành về cách họ tra tài liệu và soạn 3C hiện nay | Xác minh pain và mức sẵn sàng |
| 2–3 | Gom SOP/TSB phiên bản hiện hành của VF 5, VF 8; trích 200 hồ sơ cũ (100 được duyệt, 100 bị trả lại) | Bộ dữ liệu + bộ đánh giá |
| 3–4 | Làm phần Rule kiểm tra hồ sơ (hạn bảo hành, loại trừ, đủ ảnh/log) và đo lại tỷ lệ trả lại | Biết Rule đã giải được bao nhiêu |

Điều kiện để chuyển sang GO (pilot song song 6 tuần, 30 KTV, 3 xưởng): baseline B3 + B5 + B6 thật ≥ 30 phút/lượt; có tài liệu hiện hành và quyền truy cập; trên bộ đánh giá, Copilot tìm đúng tài liệu trong top 5 ở ≥ 90% ca và ≥ 90% trích dẫn đúng nguồn.

Ngưỡng dừng:
- Baseline thật của B3 + B5 + B6 dưới 30 phút/lượt → bài toán không đủ lớn, dừng ở phần Rule.
- Riêng phần Rule đã đưa tỷ lệ hồ sơ bị trả lại xuống dưới 10% → không cần LLM cho B6, chỉ xem xét LLM cho B3.
- Trong pilot, KTV phải viết lại > 60% bản nháp 3C, hoặc có bất kỳ ca nào Copilot đưa bước sửa pin cao áp không có trích dẫn → dừng pilot, quay về quy trình cũ.
