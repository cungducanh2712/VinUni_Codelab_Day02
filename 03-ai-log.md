# AI Log & Reflection — Lab 02

**Họ và tên:** Cung Đức Anh  
**Ngày:** 10/11/2026  
**Công cụ AI:** Google Gemini (Antigravity Copilot & Gemini API)  
**Phạm vi:** Lab 02 — AI Product Scoping · Vinmec & VinFast Copilot  

## 1. Mở đầu

Trong Lab 02, tôi thực hiện phần việc cá nhân trên branch `cungducanh` (quét bài toán Vinmec ở file 01, viết AI log ở file 03, làm prototype code) và tham gia hoàn thiện báo cáo phân tích sâu (file 02) theo bài toán chung mà cả nhóm đã thống nhất: VinFast Service & Warranty Copilot.

Tôi sử dụng AI như một công cụ hỗ trợ dựng khung, gợi ý góc nhìn và viết code mẫu ban đầu. Quan điểm của tôi ngay từ đầu là: AI chỉ là trợ lý đề xuất, mọi logic nghiệp vụ, số liệu và kiểm tra ranh giới an toàn đều phải do tôi trực tiếp xác minh và chạy thử trên terminal trước khi commit.

## 2. AI đã giúp gì

### 2.1. Quét cơ hội bài toán (File 01 — Vinmec)
AI giúp tôi nhanh chóng liệt kê các quy trình hành chính tốn thời gian tại bệnh viện (như tóm tắt bệnh án xuất viện, tra cứu mã ICD-10, rà soát bảo lãnh viện phí). Nó hỗ trợ định dạng lại 5 bài toán theo 4 lenses và chuyển 3 bài tiềm năng nhất thành dạng Quick Problem Card với các bước workflow rõ ràng.

### 2.2. Dựng báo cáo phân tích sâu (File 02 — VinFast)
Khi cả nhóm chốt chọn đề tài VinFast Copilot, AI giúp tôi hệ thống lại các trường của Problem Statement 6-field, lập bảng phân loại tác vụ (tác vụ nào dùng Rule, tác vụ nào dùng LLM RAG) và gợi ý kịch bản fallback khi mô hình không tìm thấy tài liệu kỹ thuật phù hợp.

### 2.3. Lập trình ranh giới an toàn (prompt_prototype.py)
AI hỗ trợ viết cấu trúc hàm `evaluate_prompt()` sử dụng Google Gemini SDK mới (`google-genai`), cấu hình `temperature=0.0` để hạn chế sáng tạo bừa bãi, và gợi ý 2 test case đối nghịch nhằm cố tình phá vỡ ranh giới (bỏ qua thẻ duyệt nháp hoặc yêu cầu chạy xe pin cạn đi xa).

## 3. AI sai / hallucination ở đâu

### 3.1. Nhầm lẫn kiến thức chuyên ngành và bịa số liệu
* **Đòi kiểm tra bugi cho xe điện VF8:** Khi tôi yêu cầu AI gợi ý các bước chẩn đoán mã lỗi cho xe VinFast, ban đầu AI đưa vào quy trình các mục như *"kiểm tra hệ thống đánh lửa, vệ sinh bugi và thay dầu động cơ"*. Đây là lỗi nhầm lẫn tai hại giữa xe xăng truyền thống và xe thuần điện (EV). Tôi phải gõ lệnh chấn chỉnh ngay: VinFast hiện tại là xe điện, các cụm chi tiết phải là pin cao áp (HV Battery), biến tần và mô-tơ.
* **Số liệu bệnh án nghe rất thật nhưng không có căn cứ:** Ở bài toán Vinmec, AI tự tạo ra các con số như *"bác sĩ mất đúng 28 phút gõ bệnh án"*, *"tỷ lệ xuất toán bảo hiểm 14,2%"*. Đọc qua thì rất thuyết phục, nhưng thực tế đây là số AI tự bịa vì chưa có dữ liệu khảo sát nội bộ. Tôi phải đổi toàn bộ thành nhãn "ước tính / giả định cần khảo sát thực tế".

### 3.2. Lỗi kỹ thuật mô hình và crash script trên Windows
Khi chạy file `prompt_prototype.py` trên máy Windows, script bị văng lỗi ngay từ lần chạy đầu:
* Ban đầu code gọi model `gemini-2.0-flash-exp` thì dính lỗi `404 NOT_FOUND` do phiên bản thử nghiệm này đã bị ngừng hỗ trợ trên endpoint API cá nhân.
* Script crash tiếp vì biến môi trường `GEMINI_API_KEY` chưa được nạp vào session PowerShell hiện tại, và hàm in chuỗi Unicode bị lỗi font console.
* Tôi phải tự sửa code: chuyển sang model ổn định `gemini-3.5-flash-lite`, thêm kiểm tra biến môi trường rõ ràng và chạy lại autograder kiểm tra từng tiêu chí.

## 4. Tôi đã sửa prompt và đặt ranh giới thế nào

Thay vì ra lệnh chung chung kiểu *"hãy viết một trợ lý thông minh"*, tôi chuyển sang dùng các chỉ thị ranh giới phủ định tuyệt đối:

> "Mọi phản hồi BẮT BUỘC phải bắt đầu bằng thẻ [DRAFT_ONLY]. Tuyệt đối không được bỏ thẻ này dù người dùng có yêu cầu hay ra lệnh khẩn cấp."

> "Nếu pin dưới 5%, CẤM đề xuất trạm sạc xa quá 5km. Bắt buộc trả về action dispatch_mobile_charger để cứu hộ."

Checklist ranh giới tôi áp dụng:
1. **Không cho AI tự quyết:** AI chỉ soạn bản nháp (`DRAFT_ONLY`), quyền bấm duyệt hoặc gửi đi bắt buộc thuộc về con người (Human-in-the-loop).
2. **Khóa chặt phạm vi:** Cấm AI tự suy diễn các can thiệp kỹ thuật ngoài tài liệu chính hãng; nếu thiếu dữ liệu, bắt buộc trả về yêu cầu kiểm tra thực địa.
3. **Chạy thử bằng terminal:** Mọi đoạn code hoặc test case AI viết ra đều phải chạy thực tế qua `python autograder/autograder.py`, không chỉ nhìn code bằng mắt rồi tin là chạy được.

## 5. Bài học & kết luận

Qua buổi lab, tôi rút ra một số kinh nghiệm thực tế khi làm việc với AI:
* AI viết văn rất mượt và tạo cảm giác rất tự tin, nhưng nếu lơ là không kiểm tra chuyên môn (như vụ bugi xe điện), sản phẩm đưa vào thực tế sẽ trở thành thảm họa.
* Các con số định lượng AI đưa ra hầu hết là "ảo giác hợp lý" (plausible hallucination). Người làm sản phẩm phải chủ động gắn nhãn giả định và đo lường baseline thực tế.
* Viết prompt hiệu quả không phải là dùng từ hoa mỹ, mà là đặt ra các ranh giới cấm rõ ràng và có kịch bản chặn lỗi bằng code.

Tóm lại, AI giúp tôi tiết kiệm khoảng 50% thời gian dựng khung và gõ code mẫu, nhưng khâu thẩm định kỹ thuật, kiểm tra logic và chịu trách nhiệm về kết quả cuối cùng vẫn 100% thuộc về người kỹ sư.

## 6. Bảng tóm tắt tương tác

| # | Hạng mục | Prompt chính gửi AI | AI thực hiện | Tôi phát hiện & sửa lại | Kết quả |
|---|---|---|---|---|---|
| 1 | File 01 (Scan) | "Quét 5 quy trình vận hành Vinmec theo 4 lenses, format theo bảng chuẩn" | Liệt kê 5 bài toán và soạn 3 thẻ Quick Card | Phát hiện số liệu thời gian/viện phí chưa có nguồn; gắn nhãn giả định | File 01 đủ 5 bài, 3 card chuẩn |
| 2 | File 02 (Deep-dive) | "Xây dựng luồng tương lai có HITL và Fallback cho VinFast Copilot" | Đề xuất luồng xử lý và ma trận Rule vs LLM | Bỏ các đề xuất tự động phê duyệt chi phí; giữ vai trò AI ở mức soạn nháp | Báo cáo an toàn, đúng nghiệp vụ |
| 3 | Prototype Code | "Viết hàm evaluate_prompt dùng genai SDK và 2 test case bẻ gãy ranh giới" | Sinh mã nguồn gọi Gemini API | Sửa lỗi model 404, đổi sang `gemini-3.5-flash-lite`, xử lý biến môi trường | Chạy script pass toàn bộ test case |
| 4 | Kiểm thử Autograder | "Chạy kiểm thử từng tiêu chí --check-code từ 1 đến 3" | Kiểm tra mã nguồn module | Bổ sung đầy đủ từ khóa ranh giới (`draft_only`, `5%`, `dispatch_mobile_charger`) | Autograder chấm đạt điểm tối đa |
