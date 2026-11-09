# 02 — Deep-Dive Report: VinFast Service & Warranty Copilot

> **Dự án:** Lab 02 — AI Product Scoping (Vin Smart Future)  
> **Đơn vị công tác:** Khối Công nghệ Vin Smart Future — Phối hợp Khối Dịch vụ Hậu mãi VinFast Auto  
> **Thành viên thực hiện:** Nhóm Dự án Vin Smart Future (Đại diện: Cung Đức Anh - Branch: `cungducanh`)  
> **Bài toán lựa chọn:** Hỗ trợ Kỹ thuật viên & Cố vấn Dịch vụ Chẩn đoán Mã lỗi DTC và Soạn thảo Hồ sơ Bảo hành (Service & Warranty Claim Copilot)  

---

## 🏛️ 1. Bối cảnh Nghiệp vụ & Vận hành

**VinFast Auto** là nhà sản xuất xe điện (EV) hàng đầu Đông Nam Á với dải sản phẩm trải rộng từ mini SUV (VF3) đến SUV cao cấp (VF8, VF9). Khi số lượng xe điện lưu hành tăng trưởng bùng nổ, hệ thống xưởng dịch vụ ủy quyền (3S/1S) trên toàn quốc đang phải đối mặt với áp lực vận hành khổng lồ.

Khác với xe động cơ đốt trong truyền thống, xe điện là một hệ sinh thái phần mềm và phần cứng tích hợp cao (Software-Defined Vehicles). Mỗi sự cố kỹ thuật có thể kích hoạt đồng thời hàng chục mã lỗi chẩn đoán phức tạp (**DTC — Diagnostic Trouble Codes**) liên quan đến khối pin cao áp (HV Battery), biến tần (Inverter), mô-tơ điện, hệ thống sạc On-Board (OBC) hoặc cụm điều khiển trung tâm (VCU).

Hiện nay, Kỹ thuật viên xưởng dịch vụ và Cố vấn dịch vụ đang mất từ **30 đến 45 phút cho mỗi lượt xe** chỉ để lật giở thủ công hàng trăm trang tài liệu Quy trình Thao tác Chuẩn (**SOP — Standard Operating Procedures**), tra cứu lịch sử sửa chữa và gõ biên bản giải trình bảo hành (**Warranty Claim**). Điểm nghẽn giấy tờ này kéo dài thời gian chờ đợi của chủ xe, gây quá tải xưởng và dẫn đến tỷ lệ claim bị từ chối hoặc trả về làm lại (**Claim Rework**) lên tới 18%, gây thất thoát ngân sách hàng chục tỷ đồng mỗi năm.

---

# 🏗️ Phase 3 — DEEP-DIVE: Phân Tích Chuyên Sâu

## 3.1. Current-State Workflow Mapping (Quy trình Hiện tại)

Quy trình tiếp nhận, chẩn đoán và chuẩn bị hồ sơ bảo hành hiện tại tại một xưởng dịch vụ VinFast:

```text
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│ Bước 1          │      │ Bước 2          │      │ Bước 3          │
│ Tiếp nhận xe &  │      │ Đọc mã lỗi DTC  │      │ Tra cứu SOP &   │
│ xác định VIN    │ ───> │ qua máy chẩn    │ ───> │ tài liệu KT     │
│                 │      │ đoán OBD-II     │      │                 │
│ Ai: Cố vấn DV   │      │ Ai: Kỹ thuật viên│     │ Ai: Kỹ thuật viên│
│ ⏱ 5 phút        │      │ ⏱ 5 phút        │      │ ⏱ 20 phút 🔴    │
│ In: Xe & Khách  │      │ In: Cổng OBD-II │      │ In: Mã DTC & VIN│
│ Out: Phiếu khám │      │ Out: Danh sách  │      │ Out: Hướng dẫn  │
│                 │      │      mã lỗi raw │      │      thao tác   │
└─────────────────┘      └─────────────────┘      └─────────────────┘
                                                           │
                                                           ▼ 🔄 Handoff
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│ Bước 6          │      │ Bước 5          │      │ Bước 4          │
│ Gửi hồ sơ lên   │      │ Soạn biên bản & │      │ Thao tác kiểm   │
│ cổng bảo hành   │ <─── │ warranty claim  │ <─── │ tra vật lý xe   │
│                 │      │                 │      │                 │
│ Ai: Quản lý BH  │      │ Ai: Cố vấn DV   │      │ Ai: Kỹ thuật viên│
│ ⏱ 3 phút        │      │ ⏱ 15 phút 🔴    │      │ ⏱ 20 phút       │
│ In: Hồ sơ claim │      │ In: Báo cáo KT  │      │ In: Hướng dẫn   │
│ Out: Trạng thái │      │ Out: Claim form │      │ Out: Linh kiện  │
│      chờ duyệt  │      │      đầy đủ     │      │      hỏng hóc   │
└─────────────────┘      └─────────────────┘      └─────────────────┘

🔴 Điểm nghẽn cổ chai (Bottlenecks): Bước 3 (Tra cứu SOP) và Bước 5 (Soạn claim bảo hành) ngốn 35 phút/xe!
🔄 Điểm chuyển giao (Handoff): Giữa Kỹ thuật viên xưởng và Cố vấn dịch vụ lập hồ sơ.
⏱ Tổng thời gian quy trình hành chính + chẩn đoán: ~68 phút/lượt.
```

*(Chi tiết sơ đồ trực quan được lưu trữ tại file ảnh `04-workflow-diagram.png`).*

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Trường thông tin | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | **Kỹ thuật viên xưởng dịch vụ (Technician)** và **Cố vấn dịch vụ (Service Advisor)** tại các xưởng dịch vụ VinFast 3S/1S, dưới sự giám sát của Trưởng phòng Bảo hành. |
| **2. Current Workflow** | Xe vào xưởng -> Đọc mã DTC bằng máy quét OBD-II chuyên dụng -> Kỹ thuật viên mở máy tính tra cứu danh mục SOP, tài liệu Service Manual PDF theo model/phiên bản -> Thực hiện đo đạc vật lý -> Ghi chú kết quả ra giấy chuyển cho Cố vấn dịch vụ -> Cố vấn dịch vụ tra cứu điều kiện bảo hành, mã phụ tùng thay thế và tự gõ diễn giải hồ sơ Warranty Claim lên phần mềm DMS. |
| **3. Bottleneck** | **Bước 3 & Bước 5:** Mất tổng cộng 35 phút để tìm kiếm thủ công trong hàng nghìn trang SOP rời rạc và soạn thảo văn bản giải trình bảo hành; thường xuyên sai sót mã phụ tùng hoặc thiếu dẫn chứng tài liệu kỹ thuật của VinFast. |
| **4. Business Impact** | Mỗi ngày một xưởng dịch vụ lớn tiếp nhận 30–50 lượt xe. Điểm nghẽn giấy tờ làm lãng phí ~25 giờ công/ngày/xưởng; kéo dài thời gian xe nằm xưởng thêm 0.5 ngày; tỷ lệ hồ sơ bảo hành bị nhà máy từ chối thanh toán/yêu cầu giải trình lại là **18%**, làm đóng băng dòng vốn phụ tùng ước tính **3.5 tỷ VNĐ/tháng** trên toàn hệ thống. |
| **5. Success Metric** | 1. **Hiệu suất (Efficiency):** Giảm thời gian tra cứu SOP và lập hồ sơ claim từ 35 phút xuống **dưới 8 phút/xe**.<br>2. **Chất lượng (Accuracy):** Giảm tỷ lệ hồ sơ bảo hành bị trả về (Claim Rework Rate) từ 18% xuống **dưới 4%**.<br>3. **Độ tin cậy RAG (Groundedness):** **100%** nội dung hướng dẫn sửa chữa được AI tạo ra phải có trích dẫn chính xác số trang/phiên bản SOP chính hãng của VinFast. |
| **6. Operational Boundary (Ranh giới cấm)** | **AI ĐƯỢC PHÉP:** Truy xuất tài liệu SOP, tóm tắt mã lỗi DTC, đề xuất mã phụ tùng tương thích, tự động điền nháp form Warranty Claim gắn nhãn `[DRAFT_ONLY]`.<br>🛑 **AI TUYỆT ĐỐI CẤM:**<br>• Không được tự ý phê duyệt bảo hành hoặc gửi thẳng yêu cầu chi trả lên hệ thống tài chính.<br>• Không được tự ý đưa ra kết luận sửa chữa khi độ tin cậy trích xuất tài liệu (Retrieval Similarity) dưới 80%.<br>• Bắt buộc **100% Human-in-the-loop (HITL):** Kỹ thuật viên trưởng và Cố vấn dịch vụ phải ký xác nhận trước khi phát hành. |

---

## 3.3. Future-State Flow & AI Fit (Quy Trình Tương Lai Có Tích Hợp AI)

### Phân tích AI-Fit Matrix:
* **Tại sao không dùng Rule-based thuần túy?** Danh mục mã DTC kết hợp ngữ cảnh xe (phiên bản phần mềm ECU, lịch sử bảo dưỡng, triệu chứng mô tả bằng tiếng Việt từ khách hàng) có hàng trăm nghìn biến thể. Rule tĩnh không thể đọc hiểu và tổng hợp biên bản giải trình linh hoạt.
* **Tại sao không dùng Multi-Agent tự trị?** Trong bảo hành ô tô, rủi ro pháp lý và an toàn vận hành xe cực kỳ cao. Không thể trao quyền tự trị (Autonomous Agent) cho AI tự quyết định thay thế linh kiện hoặc thanh toán tiền.
* **Lựa chọn tối ưu:** **RAG + LLM Feature kết hợp Rule-Engine kiểm soát (Co-Pilot Architecture)**.
  * **RAG (Retrieval-Augmented Generation):** Đảm bảo câu trả lời luôn bám sát tài liệu SOP VinFast, triệt tiêu ảo giác (Zero Hallucination).
  * **Rule Engine:** Kiểm tra điều kiện loại trừ bảo hành cứng (xe ngập nước, quá hạn bảo hành, độ chế ngoài xưởng).
  * **LLM Feature:** Tóm tắt, giải thích và soạn thảo văn bản nháp `[DRAFT_ONLY]`.

### Quy trình tương lai (Future-State Flow):

```text
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│ Bước 1          │      │ Bước 2          │      │ Bước 3          │
│ Cắm máy chẩn    │      │ 🔵 AI RAG Engine │      │ 🔵 LLM Co-Pilot  │
│ đoán, nạp VIN   │ ───> │ tự động truy xuất│ ───> │ soạn thảo nháp  │
│ & danh sách DTC │      │ SOP & Service   │      │ biên bản KT &   │
│                 │      │ Manual chuẩn    │      │ Warranty Claim  │
│ Ai: Kỹ thuật viên│     │ Ai: AI Service  │      │ Ai: AI Service  │
│ ⏱ 2 phút        │      │ ⏱ 30 giây       │      │ ⏱ 1 phút        │
└─────────────────┘      └─────────────────┘      └─────────────────┘
                                                           │
                                                           ▼
                                                  ┌─────────────────┐
                                                  │ 🟢 Human Review  │
                                                  │ Cố vấn & KTV    │
                                                  │ kiểm tra thực địa│
                                                  │ và duyệt claim  │
                                                  │ Ai: KTV & Cố vấn │
                                                  │ ⏱ 5 phút        │
                                                  └─────────────────┘
                                                           │
                                     ┌─────────────────────┴─────────────────────┐
                                     │                                           │
                                     ▼                                           ▼
                            [Độ tin cậy ≥ 80%]                         ↩️ Fallback [Cờ Vàng]:
                            Bấm [Xác nhận]                             Độ tương đồng < 80% hoặc DTC lạ:
                            DMS xuất lệnh sửa chữa &                   Hệ thống báo "Không đủ dữ liệu SOP",
                            chuyển Quản lý BH ký số                     chuyển KTV tra cứu thủ công như cũ.
```

---

# 🏁 Phase 5 — EVALUATE: Thẩm Định Sẵn Sàng & Quyết Định Dự Án

### Bảng Kiểm Tra Sẵn Sàng Ứng Dụng AI (AI Readiness Checklist):

| Câu hỏi thẩm định | Trạng thái | Đánh giá thực tế tại VinFast |
|---|:---:|---|
| **1. Có sẵn dữ liệu mẫu/logs sạch để test không?** | ✅ ĐẠT | Toàn bộ tài liệu SOP, sổ tay Service Manual của các dòng xe VF5/VF8 đã được chuẩn hóa PDF; hệ thống DMS lưu trữ lịch sử hơn 100.000 hồ sơ claim cũ. |
| **2. Rủi ro khi AI sai có nằm trong tầm kiểm soát không?** | ✅ ĐẠT | Kiểm soát tuyệt đối qua 2 lớp phòng vệ: (1) Ranh giới cứng `[DRAFT_ONLY]` bắt buộc KTV và Cố vấn duyệt; (2) Cơ chế Fallback cờ vàng khi độ tin cậy < 80%. |
| **3. Stakeholders sẵn sàng thay đổi quy trình làm việc không?** | ✅ ĐẠT | Khối Hậu mãi VinFast cực kỳ ủng hộ vì xưởng dịch vụ đang quá tải và cần giải phóng thời gian nhập liệu cho đội ngũ kỹ thuật. |

---

### Quyết định Thẩm định của Ban Giám đốc Vin Smart Future:

# 🟢 [x] **GO (Bắt đầu xây dựng PoC / Prototype)**

### Lý giải quyết định (Justification):
1. **Giá trị kinh tế vượt trội (High ROI):** Dự án tác động trực tiếp vào điểm nghẽn nghiêm trọng nhất của chuỗi xưởng dịch vụ VinFast: giải phóng 27 phút giấy tờ/xe, giảm tỷ lệ claim rework từ 18% về dưới 4%, giúp giải tỏa hàng chục tỷ đồng dòng vốn bảo hành mỗi quý.
2. **Kiến trúc công nghệ thực tế (Practical & Safe):** Sử dụng RAG bám sát kho tài liệu SOP chính hãng kết hợp LLM drafting. Không cần mô hình tự trị phức tạp, kiểm soát 100% rủi ro qua cơ chế Human-in-the-loop (HITL).
3. **Kế hoạch triển khai hẹp (Phased Rollout):**
   * **Giai đoạn PoC (6 tuần):** Thử nghiệm tại 3 xưởng dịch vụ trọng điểm (Hà Nội, TP.HCM, Hải Phòng) giới hạn trên 2 dòng xe chủ lực là **VF5 và VF8** để tinh chỉnh độ chính xác trích xuất SOP.
   * **Giai đoạn Pilot (3 tháng):** Mở rộng ra 10 xưởng dịch vụ lớn trước khi triển khai toàn quốc.
