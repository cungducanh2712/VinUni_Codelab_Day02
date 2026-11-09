# AI Log & Reflection — Lab 02

**Họ và tên:** Trịnh Tuấn Phong  
**Ngày:** 09/11/2026  
**Công cụ AI:** DeepSeek Harness (AI assistant)  
**Phạm vi:** Lab 02 — AI Product Scoping · VinFast Service & Warranty Copilot

## 1. Mở đầu

Lab 02 yêu cầu tôi làm phần cá nhân (01-problem-scan, 03-ai-log, 04-workflow-diagram, prompt prototype) và tham gia báo cáo nhóm (02-deep-dive). Tôi dùng AI như một **thought-partner**: nhờ nó brainstorm, phản biện, dựng khung, tìm lỗi và viết nháp; không giao cho AI quyết định thay.

Tôi không phải trưởng nhóm. Tôi làm trên branch cá nhân `trinhtuanphong`; nhóm đã chốt đề tài **Card #02 — VinFast Service & Warranty Copilot**, và tôi dùng AI để triển khai phần việc của mình theo quyết định đó.

Nguyên tắc ban đầu của tôi khá đơn giản: AI được phép đề xuất, nhưng số liệu, nguồn và quyết định cuối phải do tôi kiểm tra. Sau buổi lab, tôi nhận ra nguyên tắc này cần chặt hơn — vì AI có thể tạo ra những con số nghe rất hợp lý nhưng không có thật.

## 2. AI đã giúp gì


### 2.2. Deep-dive 02

Với 02, AI giúp tôi dựng **Quyết định của nhóm**, 6-field, bảng so sánh Rule/Hybrid/Agent, future flow có HITL/fallback, và Phase 5. Nó cũng giúp chuyển các đoạn văn dài thành bảng/flow.

Tôi đặc biệt thấy hữu ích khi AI đóng vai người phản biện: nó hỏi “con số 5,8 tỷ này lấy từ đâu?”, “tại sao GO trong khi 01 nói chưa có baseline?”, “rủi ro hallucination trong hệ thống pin cao áp đã được kiểm soát chưa?”. Những câu hỏi đó giúp tôi sửa báo cáo trước khi nộp.

### 2.3. Workflow diagram 04

AI viết script Python (Pillow) để vẽ sơ đồ current-state 7 bước, 5 handoff, 3 bottleneck, kèm time badge; sau đó xuất PNG/PDF và một file Mermaid để tôi chỉnh trên mermaid.live. AI cũng chạy autograder `--check-file-4` và báo PASS.

Tôi chỉ yêu cầu **current-state**, không vẽ future-state, và giữ đúng số liệu 3.1 của 02. Khi ảnh đầu tiên bị lỗi `IndexError` (script giả định 6 handoff trong khi flow chỉ có 5), AI tìm ra và sửa bằng cách map handoff theo từng gap. Tôi xem lại ảnh, thấy bố cục rõ và không bị cắt chữ.


## 3. AI sai/hallucination ở đâu

### 3.1. Số liệu “bằng chứng thực tế” không có nguồn

Bản nháp 02 (được AI hỗ trợ tạo) rất mượt, nhưng đưa các con số như **45.000 WO/tháng, 2.500 KTV, 18–22% hồ sơ bị trả lại, 65 phút/lượt, 5,8 tỷ VNĐ/tháng** và gọi đó là “bằng chứng thực tế tại VinFast”. Những con số này không có nguồn, không thể kiểm chứng, và mâu thuẫn trực tiếp với disclaimer trong 01: “các workflow là giả thuyết, chưa có baseline”.

Tôi suýt tin vì báo cáo được viết rất tự tin. Khi tự cộng lại và đối chiếu 01, tôi mới thấy vấn đề. Cách sửa: gắn nhãn **giả định** cho mọi số, thêm box “Giả định dữ liệu” ở đầu 02, đổi readiness từ “✅ Có (90%)” sang **🟡 Một phần/Cần xác minh**, và chuyển 5,8 tỷ thành **kịch bản stretch** thay vì con số duy nhất.



### 3.4. AI có xu hướng mở rộng scope

AI đề xuất thêm một bài toán ngoài VinFast vào 01, và đổi code sang VinFast cho khớp 02. Cả hai đều không cần thiết. Tôi phải nói rõ: **giữ nguyên 5 bài toán/3 card**, **code giữ Xanh SM**. Đây là lỗi “scope creep” điển hình của AI: nó muốn làm nhiều hơn yêu cầu.

## 4. Tôi đã sửa prompt/ranh giới thế nào

Sau vài vòng, tôi đặt ra các prompt/ranh giới cụ thể:

> “Không bịa số. Số nào không có nguồn thì ghi rõ là **giả định**; tính lại tổng trước khi trả lời.”

> “Sơ đồ chỉ **current-state**; 7 bước, 5 handoff, 3 bottleneck; cho thêm code Mermaid.”

Boundary checklist tôi tự đặt:
1. AI không được bịa số/nguồn; nếu không chắc, phải nói “không đủ dữ liệu”.
2. AI không được tự đổi đề tài/scope; mọi thay đổi lớn phải hỏi tôi.
3. AI không được tự quyết định thay nhóm; quyết định nhóm do nhóm chốt.
4. Mọi output của AI phải được tôi đọc, kiểm tra toán và chạy thử (code/ảnh) trước khi dùng.

Những ranh giới này hiệu quả hơn hẳn các prompt kiểu “viết hay hơn”, vì chúng buộc AI phải **nêu giả định** và **giữ đúng phạm vi**.

## 5. Bài học & kết luận

AI giúp tôi tăng tốc rõ rệt ở khâu cấu trúc, ngôn ngữ và phản biện. Nó biến một bản nháp rối thành báo cáo có bố cục, và chỉ ra những lỗi tôi không thấy khi đọc bằng mắt. Nhưng AI cũng là nguồn của **false precision**: những con số nghe rất thuyết phục nhưng không có nguồn, và những phép cộng sai được trình bày rất tự tin.

Nếu làm lại, tôi sẽ:
- Yêu cầu AI **nêu nguồn hoặc gắn nhãn giả định** ngay từ prompt đầu tiên.
- Tự kiểm tra toán và đối chiếu chéo 01/02/04 trước khi tin.
- Chạy thử code/ảnh, không chỉ đọc mô tả.
- Giữ quyền quyết định về đề tài, scope và code — AI chỉ là người phản biện.

Kết luận: AI là một **thought-partner tốt**, nhưng không phải nguồn chân lý. Giá trị của bài làm không nằm ở việc AI viết được bao nhiêu, mà ở chỗ tôi kiểm tra, sửa và chịu trách nhiệm được đến đâu.

## 6. Bảng tóm tắt tương tác

| # | Việc | Prompt chính | AI làm gì | Tôi kiểm tra/sửa gì | Kết quả |
|---|---|---|---|---|---|
| 1 | Scan & 01 | “Trình bày lại như bài mẫu; giữ 5 bài/3 card” | Rút gọn, thêm lens, sửa card #03 | Đủ 4 lens, không thêm bài | 01 rõ hơn (117 dòng) |
| 2 | 02 số liệu | “Không bịa số; ghi giả định; tính lại tổng” | Sửa 140=75+65, 120,5/97, ROI 3 kịch bản | Đối chiếu 01, kiểm tra toán | 02 nhất quán |
| 3 | 02 quyết định | “Bắt đầu từ quyết định nhóm; GO có điều kiện” | Thêm group decision, readiness trung thực | Xác nhận nhóm chốt Card #02 | 02 đúng scope |
| 4 | 04 diagram | “Current-state; 7 bước/5 handoff/3 bottleneck; Mermaid” | Vẽ PNG/PDF, sửa IndexError | Xem ảnh, chạy autograder | PASS |

