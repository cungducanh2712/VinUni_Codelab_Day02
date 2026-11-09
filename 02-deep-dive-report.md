# Báo cáo Deep-Dive — VinFast Service & Warranty Copilot

**Lab 02 — AI Product Scoping · Vin Smart Future**  
**Đơn vị:** VinFast — Khối Dịch vụ Hậu mãi & Xưởng dịch vụ (Aftersales Service)  
**Nhóm:** AI Product Management

> **Giả định dữ liệu:** Các con số 45.000 WO/tháng, ~2.500 KTV, 18–22% hồ sơ bị trả lại, 65 phút/lượt và 180.000 VNĐ/giờ là giả thuyết lập kế hoạch cần xác minh với VinFast, không phải số liệu đã kiểm chứng. ROI bên dưới là kịch bản, không phải cam kết.

---

# 🗳️ Quyết định lựa chọn của nhóm

Nhóm đã họp và chốt chọn bài toán **Card #02 — VinFast Service & Warranty Copilot** (Trợ lý AI Chẩn đoán Kỹ thuật & Lập Hồ sơ Bảo hành) để thực hiện Deep-Dive.

## Lý do chọn Card #02
- Bottleneck nằm ở **ngôn ngữ và hành chính**, không phải thao tác sửa chữa vật lý: KTV mất thời gian tra cứu SOP/TSB và soạn hồ sơ 3C — đúng chỗ RAG/LLM có lợi thế.
- Dữ liệu đầu vào chủ yếu là **văn bản** (DTC, SOP/TSB, ghi chú sửa chữa, mẫu 3C) nên có thể thử nghiệm trong phạm vi lab.
- **HITL rõ:** KTV/SA vẫn là người xác nhận chẩn đoán và ký gửi hồ sơ; AI chỉ tạo bản nháp.
- **Metric đo được:** thời gian tra cứu/lập hồ sơ và tỷ lệ hồ sơ bị trả lại — phù hợp để đánh giá PoC.
- Tách được **rule-based** (điều kiện bảo hành, định mức, loại trừ) khỏi **LLM** (đọc hiểu tài liệu, soạn nháp), giảm rủi ro.

## Vì sao không chọn các card khác
- **Card #01 — CSKH & điều phối cứu hộ:** tác động real-time nhưng phụ thuộc API vị trí/trạng thái đội cứu hộ; AI chỉ là một phần của quy trình; khó đo trong phạm vi lab và có rủi ro điều phối.
- **Card #03 — Sức khỏe pin:** cần dữ liệu BMS/telematics có nhãn/ground truth và thời gian quan sát dài; đây là bài toán ML/time-series, không phải LLM.
- **#04–#05 — lỗi nhà máy & rủi ro nhà cung cấp:** dữ liệu phân tán, phạm vi hệ thống rộng, phù hợp giai đoạn sau.

## Phạm vi deep-dive
- **Bài toán:** giảm thời gian tra cứu tài liệu + lập hồ sơ bảo hành 3C cho KTV/SA.
- **Pilot đề xuất:** VF 5 & VF 8, 3 xưởng trọng điểm (Hà Nội/TP.HCM), PoC 6 tuần.

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow

Quy trình xử lý một ca bảo hành tại xưởng VinFast gồm **7 bước**, 4 nhóm nhân sự và 3 hệ thống rời rạc (DMS/SAP, máy chẩn đoán VDS/OBD, kho PDF/SharePoint).

```text
[B1] Tiếp nhận & ghi nhận triệu chứng — SA — 10'
   ↓ 🔄 H1: chuyển RO + chìa khóa xuống xưởng (dễ mất ngữ cảnh)
[B2] Đọc mã lỗi DTC & kiểm tra sơ bộ — KTV — 15'
   ↓ 🔄 H2: chép/chụp DTC từ máy VDS sang máy tra cứu
[B3] 🔴 Tra cứu SOP/TSB/lịch sử — KTV — 28'
   ↓
[B4] Đo kiểm, sửa chữa, thay linh kiện — KTV — 45'
   ↓ 🔄 H3: ghi chú thô/Zalo cho SA/Admin bảo hành
[B5] 🔴 Soạn 3C & tra mã bảo hành — KTV/Admin — 22'
   ↓ 🔄 H4: submit claim + ảnh/log lên Hội sở
[B6] 🔴 Kiểm duyệt & xử lý trả lại — Auditor — 15' (quy đổi)
   ↓ 🔄 H5: phản hồi kết quả duyệt trên DMS/SAP
[B7] Đóng lệnh & quyết toán — SA — 5'
```

**Thời gian hiện tại:**

| Hạng mục | Thời gian | Cách tính |
|---|---:|---|
| Tổng end-to-end | **140 phút/lượt** | 7 bước |
| Vật lý (B1, B2, B4, B7) | **75 phút** | 10 + 15 + 45 + 5 |
| Thông tin/hành chính (B3, B5, B6) | **65 phút** | 28 + 22 + 15 |

**3 bottleneck & 2 handoff rủi ro:**
1. **🔴 B3 — Tra cứu SOP/TSB (28 phút):** tài liệu PDF/TSB cập nhật hàng tuần; `Ctrl+F` không kết hợp được “lời kể khách + DTC + firmware”, KTV mới dễ đoán mò.
2. **🔴 B5 — Soạn 3C & mã bảo hành (22 phút):** chuyển ghi chú thô thành văn bản 3C chuẩn, tra Causal Part/Labor Op Code; KTV yếu kỹ năng hành chính nên gõ chậm, dễ thiếu.
3. **🔴 B6 — Kiểm duyệt & rework (15 phút quy đổi):** ước 18–22% hồ sơ bị trả lại do thiếu ảnh/log hoặc 3C không khớp DTC; mỗi hồ sơ trả lại tốn thêm 45–60 phút, quy đổi ~15 phút/WO.
4. **🔄 H1 & H3:** thông tin rơi rụng khi chuyển từ khách → SA → KTV → Admin bảo hành; ghi chú viết tắt/Zalo làm sai lệch bản chất kỹ thuật.

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | KTV chẩn đoán/sửa chữa, Cố vấn dịch vụ (SA), Admin bảo hành; Chuyên viên Kiểm toán Bảo hành Hội sở là người hưởng lợi gián tiếp. Quy mô tham chiếu ~2.500 KTV (giả định cần xác minh). |
| **2. Current Workflow** | SA tạo RO trên DMS/SAP → KTV đọc DTC bằng VDS/OBD → tra SOP/TSB trên SharePoint bằng Ctrl+F → sửa chữa, ghi chú thô → soạn 3C, tra Causal Part/Labor Code trên DMS/Excel → đính kèm ảnh, nộp Warranty Portal. 7 bước, ~140 phút/WO (giả thuyết). |
| **3. Bottleneck** | B3 tra cứu SOP/TSB (28') + B5 soạn 3C/mã bảo hành (22') + B6 rework (15' quy đổi) = **65 phút thông tin/hành chính**. Ước 18–22% hồ sơ bị trả lại (giả định); KTV mới có thể chẩn đoán sai hướng. |
| **4. Business Impact** | 65 phút × 45.000 WO/tháng (giả định) = **48.750 giờ công/tháng** cho việc tra cứu/giấy tờ. Rework làm chậm quyết toán với OEM, tăng chi phí thay nhầm linh kiện và kéo dài thời gian xe nằm xưởng. |
| **5. Success Metric** | **PoC:** giảm ≥30% thời gian tra cứu + lập hồ sơ (65 → ≤45,5 phút/WO); giảm ≥25% hồ sơ bị trả lại (20% → ≤15%); 100% câu trả lời có nguồn hoặc từ chối khi thiếu căn cứ; FTFR không thấp hơn nhóm đối chứng. **Stretch (sau baseline):** 65 → 22 phút; rework 20% → 5%. |
| **6. Operational Boundary** | **Được phép:** tra cứu/tổng hợp SOP/TSB kèm trích dẫn; tạo bản nháp 3C `[DRAFT]`; gợi ý Labor Op Code; cảnh báo thiếu ảnh/log. **Cấm:** tự suy diễn bước sửa chữa ngoài tài liệu hãng (đặc biệt hệ thống pin cao áp); tự phê duyệt/từ chối bảo hành; tự đóng lệnh trên DMS/SAP. **HITL:** KTV xác nhận kết quả đo kiểm; KTV/SA đọc, sửa và ký gửi bản nháp 3C. |

---

## 3.3. Future-State Flow & AI Fit

**AI Fit:** chọn **Hybrid LLM Feature** — RAG + Structured Output + Rule Engine. Không chọn Rule thuần vì bottleneck nằm ở ngôn ngữ/phi cấu trúc; không chọn Agent vì quy trình bảo hành có trình tự cố định và rủi ro tự động hóa cao.

| Tiêu chí | Rule/SQL thuần | **Hybrid LLM (chọn)** | Agent tự trị |
|---|---|---|---|
| Giải quyết bottleneck | Không hiểu mô tả triệu chứng/ghi chú thô; không viết được 3C | RAG nối triệu chứng với DTC/TSB; LLM soạn nháp 3C; rule kiểm tra bảo hành | Thừa; quy trình cố định, không cần tự lập kế hoạch |
| Kiểm soát rủi ro | An toàn nhưng cứng, dễ bỏ sót do lệch từ khóa | RAG khóa vào tài liệu VinFast, bắt buộc trích dẫn; HITL 2 chốt | Rủi ro tự gọi tool/sửa DMS/SAP ngoài tầm kiểm soát |
| Chi phí/độ trễ | Rẻ, <1 giây | ~$0.03–0.05/WO; 3–6 giây/truy vấn (ước tính) | Đắt, 20–40 giây, nhiều vòng lặp |

```text
[B1] Tiếp nhận & đồng bộ context — SA/KTV
   ↓
[B2] 🔵 AI RAG: top 3 root causes + SOP/TSB citations
   ├─ ↩️ Fallback 1: confidence <0.75 hoặc DTC mới → tìm kiếm DTC truyền thống + Techline
   ↓
[B3] 🟢 HITL: KTV xác nhận & sửa chữa vật lý
   ↓
[B4] 🔵 AI soạn nháp 3C + gợi ý Labor Code
   ├─ ↩️ Fallback 2: không khớp mã → giữ ghi chú gốc, dropdown tra tay
   ↓
[B5] ⚙️ Rule gate + AI consistency check
   ↓
[B6] 🟢 HITL: KTV/SA rà soát & ký gửi
   ↓
[B7] Hội sở duyệt nhanh & đóng lệnh
```

**Time impact** (chỉ tính phần thay đổi; future flow giữ nguyên 75 phút vật lý):

| Kịch bản | Info/admin | End-to-end | Ghi chú |
|---|---:|---:|---|
| Hiện tại | 65' | 140' | 75' vật lý + 65' info/admin |
| PoC −30% | ≤45,5' | ≤120,5' | mục tiêu cam kết PoC |
| Stretch −66% | 22' | 97' | chỉ sau khi baseline xác nhận |

Lưu ý: mức −30%/−66% áp dụng cho phần info/admin; end-to-end giảm ít hơn vì giữ nguyên 75 phút vật lý.

---

# ✅ Phase 5 — EVALUATE (Nhóm)

## 5.1. AI Readiness Checklist

| Câu hỏi | Trạng thái | Bằng chứng / điều kiện |
|---|---|---|
| 1. Có sẵn dữ liệu mẫu/logs sạch để test? | 🟡 **Một phần** | Có tài liệu SOP/TSB, DTC, work order/claim lịch sử (giả định), nhưng cần quyền truy cập DMS/SAP, làm sạch, chuẩn hóa phiên bản và bộ test gán nhãn. Chưa có bộ dữ liệu sạch hoàn chỉnh. |
| 2. Rủi ro khi AI sai có trong tầm kiểm soát? | ✅ **Có** | Copilot chạy song song, không tự ghi DMS/SAP; 2 chốt HITL; 2 fallback; bắt buộc trích dẫn. Rủi ro còn lại: hallucination, tài liệu lỗi thời, KTV bỏ qua cảnh báo. |
| 3. Stakeholders sẵn sàng đổi quy trình? | 🟡 **Cần xác minh** | KTV/SA có động lực giảm giấy tờ; nhưng cần khảo sát/pilot để xác nhận. BGĐ Hậu mãi cần thấy ROI và không gián đoạn vận hành. |

## 5.2. Quyết định

- **[X] GO — có điều kiện:** bắt đầu Prototype + Pilot 6 tuần với scope hẹp (VF 5 & VF 8, 3 xưởng). Điều kiện: có quyền truy cập dữ liệu, làm sạch tài liệu, đo baseline, đạt gate citation/HITL; nếu không đạt → dừng hoặc chuyển NOT YET.
- [ ] NOT YET — trì hoãn toàn bộ.
- [ ] NO-GO — hủy.

## 5.3. Justification

### 1. Kỹ thuật

- Kiến trúc **Hybrid RAG + Structured Output + Rule Engine** là hướng phổ biến, không cần fine-tune; nhưng chưa có kết quả thử nghiệm nội bộ. PoC sẽ đo **Retrieval Recall@5 ≥90%** và **Citation Precision ≥90%**; không dùng con số “đã được chứng minh” khi chưa có bằng chứng.
- Tài liệu có cấu trúc mã DTC/quy trình/bảng mã; hybrid search (BM25 + vector) phù hợp để bắt cả mã lỗi lẫn ngữ nghĩa triệu chứng.

### 2. Kinh tế (3 kịch bản, giả định)

| Kịch bản | Giảm info/admin | Giờ công/tháng | Quy đổi FTE | Tiết kiệm/tháng |
|---|---:|---:|---:|---:|
| Base (PoC −30%) | 19,5' | 14.625 | ~83 | ~2,63 tỷ VNĐ |
| Expected (−50%) | 32,5' | 24.375 | ~139 | ~4,39 tỷ VNĐ |
| Stretch (−66%) | 43' | 32.250 | ~183 | ~5,8 tỷ VNĐ |

**Ghi chú:** 1 FTE ≈ 176 giờ/tháng; 180.000 VNĐ/giờ và 45.000 WO/tháng là giả định. Chưa gồm chi phí build/integration, hạ tầng, làm sạch dữ liệu và đào tạo — cần bổ sung trước khi mở rộng.
- Chi phí API ước ~$0.04/WO (~1.000 VNĐ), tức ~45 triệu VNĐ/tháng ở quy mô 45.000 WO; tiết kiệm base case vẫn lớn hơn chi phí API.
- Nếu quy mô thực tế thấp hơn 45.000 WO/tháng, ROI giảm tuyến tính; cần xác minh trước khi cam kết.

### 3. Rủi ro vận hành

- Chạy **side-by-side Copilot**, không can thiệp lõi ERP/SAP; nếu AI lỗi, xưởng tiếp tục quy trình cũ → **rủi ro gián đoạn thấp**.
- HITL 2 chốt + fallback + citation; không auto-approve bảo hành, không tự đóng lệnh.
- Rủi ro còn lại: dữ liệu PII/VIN, phân quyền tài liệu, tài liệu lỗi thời, KTV tin AI quá mức; cần kiểm soát trong pilot.

### 4. Lộ trình 6 tuần

- **Tuần 1–2:** làm sạch SOP/TSB cho VF5/VF8; trích 500 hồ sơ (250 chuẩn, 250 từng bị trả) làm test set.
- **Tuần 3–4:** xây pipeline RAG + module nháp 3C + rule checklist; đo Recall@5/Citation Precision/3C Completeness.
- **Tuần 5–6:** pilot shadow mode + HITL với 30 KTV tại 3 xưởng; đo thời gian tiết kiệm, rework, FTFR; quyết định mở rộng.

---

## 🏁 Kết luận từ buổi Lab

Nhóm chọn **GO có điều kiện** cho **VinFast Service & Warranty Copilot**: bài toán cụ thể, bottleneck ngôn ngữ/hành chính rõ, giải pháp Hybrid RAG + Rule Engine phù hợp, HITL/fallback kiểm soát được rủi ro. Điều kiện then chốt là xác minh dữ liệu/baseline trong 6 tuần PoC; nếu không đạt gate, dừng hoặc chuyển NOT YET. Đây là quyết định dựa trên kịch bản có gắn nhãn giả định, không phải cam kết ROI.
