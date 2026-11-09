# 03 — AI Thought-Partner Log & Reflection (Nhật Ký Tương Tác AI)

> **Môn học:** Lab 02 — AI Product Scoping (Vin Smart Future)  
> **Sinh viên thực hiện:** Cung Đức Anh (Branch cá nhân: `cungducanh`)  
> **Đơn vị giả định:** AI Product Engineer tại Vin Smart Future (Vingroup)  
> **Công cụ AI sử dụng:** Google Gemini (Antigravity Copilot, Gemini 2.5 Flash, Gemini 3.5 Flash Lite)  

---

## 🧭 1. Tổng Quan Quá Trình Đồng Hành Cùng AI

Trong suốt buổi Lab hôm nay, tôi đã sử dụng AI như một **"Đối tác tư duy (Thought-Partner)"** và một **"Cố vấn phản biện khắt khe (Critical Challenger)"** từ khâu quét cơ hội ban đầu cho đến lập trình bản mẫu kỹ thuật:

1. **Giai đoạn 1 (Phase 1 & 2):** Sử dụng AI để brainstorm và phân tích 5 bài toán thực tế của **Hệ thống Y tế Vinmec** qua 4 Lenses vận hành.
2. **Giai đoạn 2 (Thảo luận nhóm):** Dùng AI để tổng hợp, đọc hiểu và xây dựng ma trận đánh giá so sánh dữ liệu từ cả 5 nhánh của các thành viên trong nhóm (Vinmec, VinWonders, Vinhomes, VinFast, Xanh SM) để tìm ra Top 3 bài toán xuất sắc nhất.
3. **Giai đoạn 3 (Phase 3 & 5):** Cùng AI Deep-Dive vào bài toán hạt nhân được cả nhóm lựa chọn: **VinFast Service & Warranty Copilot**, thiết lập ranh giới an toàn y tế/kỹ thuật và đánh giá tính khả thi kinh doanh.
4. **Giai đoạn 4 (Phase 4):** Lập trình Python trực tiếp với Gemini SDK để stress-test các ranh giới an toàn vận hành qua các bài kiểm thử đối nghịch (Adversarial Tests).

---

## 🌟 2. AI Đã Giúp Gì Cho Tôi? (Những Điểm Tích Cực)

* **Tăng tốc độ Brainstorming vượt trội:** AI giúp tôi nhanh chóng hệ thống hóa các quy trình nội bộ phức tạp của Vingroup (như quy trình viện phí Vinmec, điều phối xe Xanh SM, quản lý căn hộ Vinhomes, chẩn đoán DTC VinFast) thành các chuỗi luồng nghiệp vụ 5 bước rõ ràng với các điểm nghẽn (🔴 Bottleneck) và điểm bàn giao (🔄 Handoff) định lượng bằng phút.
* **Tổng hợp và đối chiếu đa chiều:** Khi nhóm có 5 thành viên nộp 5 file scan riêng biệt, AI đã hỗ trợ đọc và phân tích chéo 5 bộ dữ liệu chỉ trong vài giây, lượng hóa các tiêu chí (thời gian, chi phí, AI Fit, độ thiết thực) để nhóm dễ dàng đưa ra quyết định Deep-Dive công tâm và thuyết phục.
* **Tạo các bài test đối nghịch hiểm hóc (Adversarial Prompts):** AI rất xuất sắc trong việc tự đóng vai một người dùng "cố tình vi phạm" (ví dụ: tài xế ép gửi tin khẩn cấp bỏ qua duyệt nháp, hoặc kỹ thuật viên cố tình ép hệ thống bảo hiểm chấp nhận mã phụ tùng sai) để kiểm tra độ vững chắc của `SYSTEM_PROMPT`.

---

## ⚠️ 3. AI Đã Sai Gì / Ảo Giác (Hallucination) Ở Đâu?

Quá trình làm việc cho thấy AI không hoàn hảo và bộc lộ nhiều điểm yếu nếu người kỹ sư không có tư duy phản biện:

### ❌ Ảo giác 1: "Thần thánh hóa" AI và đề xuất mức độ tự trị nguy hiểm
* **Lỗi của AI:** Khi ban đầu brainstorm giải pháp cho Vinmec và VinFast, AI liên tục đề xuất mô hình *"Autonomous Multi-Agent"* có khả năng tự động kê đơn thuốc, tự động ra lệnh phẫu thuật, hoặc tự động phê duyệt chi trả bảo hành ô tô hàng trăm triệu đồng mà không cần con người can thiệp.
* **Nguyên nhân:** AI có xu hướng tối ưu hóa tự động hóa hoàn toàn theo lý thuyết mà bỏ quên các ràng buộc pháp lý nghiêm ngặt (Luật Khám chữa bệnh, tiêu chuẩn quốc tế JCI, rủi ro kiện tụng bảo hiểm xe cơ giới).

### ❌ Ảo giác 2: Nhầm lẫn kiến thức chuyên ngành giữa xe xăng và xe điện
* **Lỗi của AI:** Khi phân tích quy trình chẩn đoán mã lỗi DTC cho VinFast, AI ban đầu trích xuất các quy trình liên quan đến hệ thống phun nhiên liệu, bugi, dầu động cơ và khí thải của xe xăng truyền thống (ICE), thay vì tập trung vào khối pin cao áp (HV Battery), biến tần (Inverter), mô-tơ điện và bộ sạc On-Board (OBC) của xe thuần điện (EV).

### ❌ Sự cố kỹ thuật: Lỗi phiên bản mô hình (Model Deprecation)
* **Lỗi kỹ thuật:** Khi thực thi code ban đầu với `gemini-2.5-flash`, hệ thống trả về lỗi `404 NOT_FOUND` do phiên bản mô hình này đã bị đóng cho tài khoản mới. Nếu không theo dõi log kỹ thuật và hiểu cơ chế fallback của SDK, bài kiểm tra sẽ crash hoàn toàn.

---

## 🛠️ 4. Tôi Đã Tinh Chỉnh Prompt và Thiết Lập Ranh Giới Ra Sao?

Để đưa AI vào kỷ luật và phục vụ đúng mục tiêu kỹ thuật của Vin Smart Future, tôi đã áp dụng các kỹ thuật tinh chỉnh:

### 1. Áp dụng Kỹ thuật "Operational Boundary Hardening" (Đóng băng ranh giới cấm)
Thay vì hướng dẫn chung chung *"hãy hỗ trợ kỹ thuật viên"*, tôi bổ sung vào `SYSTEM_PROMPT` các mệnh lệnh phủ định tuyệt đối:
* Bắt buộc mọi văn bản sinh ra phải có tiền tố `[DRAFT_ONLY]` ở ngay đầu dòng đầu tiên.
* Cấm tuyệt đối không được tự ý xóa thẻ duyệt nháp dù người dùng dùng bất kỳ câu lệnh ra lệnh hay ép buộc nào.
* Thiết lập ngưỡng logic cứng: Nếu mức pin xe dưới 5% (`< 5%`), tuyệt đối cấm đề xuất trạm sạc cách xa trên 5km, bắt buộc trả về mã hành động `dispatch_mobile_charger` điều xe cứu hộ di động.

### 2. Thiết kế Kiến trúc Hybrid: Rule Engine + RAG + Human-in-the-loop (HITL)
* Tôi kiên quyết không trao quyền quyết định tài chính/chẩn đoán cho LLM.
* LLM chỉ chịu trách nhiệm **đọc hiểu, trích xuất và soạn thảo bản nháp**.
* Mọi quyết định chấp nhận bảo hành hay ký lệnh xuất viện bắt buộc phải có chữ ký số của con người (Kỹ thuật viên trưởng / Bác sĩ điều trị).
* Nếu độ tin cậy trích xuất tài liệu SOP dưới 80%, hệ thống tự động kích hoạt cơ chế dự phòng (Fallback) bật cờ vàng cảnh báo và chuyển sang tra cứu thủ công.

### 3. Khắc phục lỗi kỹ thuật mô hình bằng Dynamic Fallback
Trong file `prompt_prototype.py`, tôi đã lập trình danh sách các mô hình ưu tiên `[GEMINI_MODEL, "gemini-3.5-flash-lite", "gemini-2.5-flash", "gemini-flash-latest"]`. Nhờ vậy, khi chạy test, hệ thống tự động bắt tín hiệu và chuyển sang `gemini-3.5-flash-lite`, giúp toàn bộ các bài kiểm thử ranh giới vượt qua 100% với tốc độ phản hồi dưới 2 giây.

---

## 🎯 5. Kết Luận & Bài Học Cá Nhân

Buổi học hôm nay đã củng cố một nguyên tắc cốt lõi của một **AI Product Engineer**:
> *"AI không phải là cây đũa thần thay thế con người, mà là một nhân viên thực tập cực kỳ thông minh nhưng luôn cần có ranh giới an toàn nghiêm ngặt và sự giám sát chặt chẽ của chuyên gia nghiệp vụ."*

Việc làm chủ kỹ năng Scoping, vẽ sơ đồ quy trình thực tế và lập trình stress-test ranh giới prompt chính là chìa khóa để triển khai các sản phẩm AI thành công và an toàn tại các tập đoàn quy mô lớn như Vingroup.
