# Lab 02 — Phase 1–2: Scan & Quick Assess (VinFast / Vin Smart Future)

*Danh sách bài toán AI tiềm năng cho VinFast — Phase 1 (SCAN) và Phase 2 (QUICK-ASSESS).*

> **TL;DR:** 5 bài toán vận hành VinFast → 3 Quick Problem Card → đề xuất deep-dive **Card #02 — VinFast Service & Warranty Copilot**.

> **Lưu ý về dữ liệu:** Các workflow dưới đây là giả thuyết nghiệp vụ cần xác minh với đơn vị vận hành. Các KPI có số là mục tiêu đề xuất cho PoC, không phải kết quả hiện tại hay cam kết hiệu quả của VinFast. Chưa có baseline nội bộ thì cần đo trước khi kết luận về mức tiết kiệm.

## Bối cảnh

Trong khuôn khổ Lab 02, tôi đóng vai **AI Product Engineer tại Vin Smart Future**, phụ trách mảng **VinFast Aftersales**. 5 bài toán dưới đây là các giả thuyết từ quá trình quét vận hành; 3 Quick Card là top 3 được chọn để phân tích sâu; đề xuất deep-dive là **Card #02**.

## 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội

| # | Subsidiary | Lens | Mô tả ngắn (bottleneck) |
|---|---|---|---|
| 1 | VinFast | Lặp lại (Repetitive) | **CSKH & điều phối cứu hộ:** tiếp nhận yêu cầu từ hotline/app, hỏi thông tin xe/VIN/vị trí, phân loại rồi chuyển kỹ thuật hoặc đội cứu hộ. Nghẽn: hỏi lại thông tin, phân loại sai, chuyển tiếp giữa các bộ phận. |
| 2 | VinFast | Tốn thời gian | **Chẩn đoán dịch vụ & bảo hành:** tra DTC, SOP/TSB, lịch sử sửa chữa; lập hồ sơ 3C và warranty claim. Nghẽn: tra cứu tài liệu + nhập liệu; tỷ lệ hồ sơ bị trả lại. |
| 3 | VinFast | AI-upgrade | **Sức khỏe pin & bảo trì dự đoán:** phân tích BMS/telematics theo thời gian, phát hiện suy giảm bất thường, ưu tiên xe cần kiểm tra. Nghẽn: xem time-series, cảnh báo giả, thiếu ground truth. |
| 4 | VinFast | AI-upgrade (phụ: Lặp lại) | **Phân tích lỗi & tìm nguyên nhân gốc trong nhà máy:** liên kết dữ liệu lỗi, log thiết bị, thông số công đoạn, lô linh kiện và lịch sử bảo trì để khoanh vùng nguyên nhân. Nghẽn: điều tra phân tán, thời gian tìm nguyên nhân gốc. |
| 5 | VinFast | Pain từ người khác (phụ: Lặp lại) | **Rủi ro nhà cung cấp & thiếu hụt linh kiện:** tổng hợp giao hàng, tồn kho, lead time, chất lượng để cảnh báo giao trễ/thiếu vật tư, hỗ trợ Procurement ưu tiên. Nghẽn: dữ liệu phân tán, tổng hợp thủ công. |

**4 lens dùng để quét:**
- **Lặp lại (Repetitive):** công việc thường xuyên, khối lượng đủ lớn, tốn thời gian nhân sự.
- **Tốn thời gian:** tác vụ thủ công ngốn thời gian xử lý.
- **AI-upgrade:** chất lượng/tốc độ có thể cải thiện nhờ hiểu ngôn ngữ tự nhiên, khai thác dữ liệu phi cấu trúc, phát hiện bất thường hoặc dự báo.
- **Pain từ người khác (Stakeholder Pain):** nhân viên/khách hàng/đối tác chịu ảnh hưởng trực tiếp từ chậm trễ, sai sót hoặc phải xử lý lại.

> Một quy trình lặp lại không đồng nghĩa phải dùng AI. Nếu đầu vào có cấu trúc và quy tắc xử lý ổn định, workflow automation hoặc rule-based code có thể là lựa chọn tốt hơn.

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Top 3 từ 5 bài toán: **#01 CSKH & điều phối cứu hộ**, **#02 Chẩn đoán dịch vụ & bảo hành**, **#03 Sức khỏe pin**. Đây là 3 bài có đủ dữ liệu văn bản/tín hiệu để thử AI trong phạm vi lab, tác động vận hành rõ và có thể đo bằng thời gian hoặc tỷ lệ lỗi. Hai bài #04–#05 chưa vào top 3 vì cần dữ liệu nhà máy/chuỗi cung ứng phân tán và phạm vi hệ thống rộng hơn, phù hợp giai đoạn sau.

---

### 🃏 QUICK PROBLEM CARD #01 — CSKH & điều phối cứu hộ

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Giảm thời gian tiếp nhận và phân loại yêu cầu CSKH/cứu hộ bằng cách tự động trích xuất thông tin từ hội thoại, chuẩn hóa ticket và đề xuất nhóm xử lý phù hợp. |
| **Công ty** | [x] VinFast |
| **Actor** | Nhân viên tổng đài CSKH, nhân viên điều phối cứu hộ, kỹ thuật viên hiện trường, khách hàng gặp sự cố. |
| **Workflow 5 bước** | 1. Khách gọi hotline/app → 2. Nhân viên hỏi thông tin xe, VIN, vị trí, mô tả sự cố → 3. Phân loại, tra lịch sử, xác định ưu tiên → 4. Chuyển kỹ thuật/đội cứu hộ → 5. Theo dõi, cập nhật khách. |
| **Bottleneck** | Bước 2–3: thông tin ban đầu thiếu, phải hỏi lại; mô tả sự cố khó chuẩn hóa, dễ phân loại nhầm. ⏱ Ước 10–15 phút/lượt (giả thuyết, cần đo). |
| **AI step** | Speech-to-Text + NLP/LLM trích xuất VIN, vị trí, triệu chứng; tóm tắt thành ticket có cấu trúc; gợi ý nhóm xử lý và ưu tiên. Nhân viên xác nhận case không chắc; quy tắc an toàn/điều phối cuối cùng do hệ thống nghiệp vụ kiểm soát. |
| **Metric** | Giảm ≥30% median thời gian tiếp nhận/phân loại so baseline; ≥95% độ chính xác phân luồng trên tập gán nhãn; giảm ≥20% tỷ lệ khách liên hệ lại trong 7 ngày; 100% recall trên tập kiểm thử tình huống nguy hiểm đã gán nhãn (tiêu chí kiểm thử, không phải cam kết an toàn tuyệt đối). |
| **Quick Architecture** | [x] Rule  [x] LLM (hybrid) |
| **Ranh giới** | Geospatial lookup, trạng thái đội cứu hộ, điều kiện phân cấp và ưu tiên dùng API/rule-based. LLM chỉ xử lý hội thoại tự do, trích xuất, tóm tắt; chưa cần Agent tự quyết định điều phối trong MVP. |

---

### 🃏 QUICK PROBLEM CARD #02 — VinFast Service & Warranty Copilot

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Giảm thời gian KTV/cố vấn dịch vụ tra cứu tài liệu kỹ thuật và chuẩn bị hồ sơ sửa chữa, bảo hành bằng một Service & Warranty Copilot có dẫn nguồn. |
| **Công ty** | [x] VinFast |
| **Actor** | Kỹ thuật viên tại xưởng dịch vụ, cố vấn dịch vụ, nhân viên quản lý bảo hành và bộ phận kỹ thuật. |
| **Workflow 5 bước** | 1. Tiếp nhận xe, xác định VIN/model/phiên bản → 2. Đọc mã lỗi DTC, kiểm tra thông tin kỹ thuật ban đầu → 3. Tra cứu SOP/TSB/lịch sử lỗi tương tự → 4. Kiểm tra xe, xác định phương án sửa chữa và linh kiện → 5. Nhập nội dung sửa chữa, chuẩn bị hồ sơ, gửi warranty claim. |
| **Bottleneck** | Bước 3 & 5: tìm kiếm thông tin ở nhiều tài liệu/hệ thống, tổng hợp lịch sử và nhập hồ sơ. Cần tách thời gian tra cứu, nhập liệu và kiểm tra kỹ thuật. ⏱ Ước 45–65 phút/lượt cho tra cứu + lập hồ sơ (giả thuyết, cần đo). |
| **AI step** | RAG trên tài liệu kỹ thuật/SOP/hướng dẫn đã phê duyệt để tìm đúng nội dung theo model/phiên bản/VIN; LLM tóm tắt mô tả sự cố, giải thích tài liệu trong phạm vi nguồn, tổng hợp trường hợp tương tự, tạo bản nháp 3C/work order/warranty claim; câu trả lời phải kèm nguồn hoặc báo không đủ căn cứ; KTV xác nhận chẩn đoán/phương án. |
| **Metric** | Giảm ≥30% median thời gian tra cứu + chuẩn bị hồ sơ/work order; giảm ≥25% tỷ lệ hồ sơ bị trả lại do thiếu/sai thông tin; 100% câu trả lời trong tập đánh giá phải dẫn nguồn phù hợp hoặc từ chối kết luận khi không đủ căn cứ; First-Time Fix Rate không thấp hơn nhóm đối chứng; theo dõi tỷ lệ sửa chữa lại. |
| **Quick Architecture** | [x] Rule  [x] LLM (hybrid RAG) |
| **Ranh giới** | RAG/LLM: tìm kiếm, đọc hiểu tài liệu, soạn bản nháp. Rule-based/business workflow: thời hạn bảo hành, mã linh kiện được bảo hiểm, điều kiện loại trừ, định mức và tính đầy đủ của trường dữ liệu. Không dùng LLM để tự phê duyệt bảo hành hoặc tự quyết định thay linh kiện. |

---

### 🃏 QUICK PROBLEM CARD #03 — Sức khỏe pin & bảo trì dự đoán

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Phát hiện sớm xu hướng suy giảm bất thường của pin từ dữ liệu vận hành để ưu tiên kiểm tra và lập kế hoạch bảo trì trước khi xảy ra sự cố. |
| **Công ty** | [x] VinFast |
| **Actor** | Đội ngũ Battery Engineering, Remote Diagnostics, quản lý chất lượng, bảo hành và vận hành xưởng dịch vụ. |
| **Workflow 5 bước** | 1. BMS/telematics ghi dữ liệu điện áp, nhiệt độ, dòng điện, trạng thái sạc/xả → 2. Hệ thống hiện tại áp dụng ngưỡng/cảnh báo → 3. Kỹ thuật kiểm tra cảnh báo, dữ liệu theo thời gian, lịch sử vận hành → 4. Kỹ sư đánh giá rủi ro, phân biệt bất thường nhất thời với xu hướng suy giảm → 5. Lên lịch kiểm tra, bảo trì hoặc xử lý bảo hành. |
| **Bottleneck** | Bước 3–4: xem xét dữ liệu theo thời gian, đối chiếu nhiều tín hiệu, ưu tiên xe cần kiểm tra; rủi ro cảnh báo giả hoặc bỏ sót suy giảm bất thường. ⏱ Ước 20–30 phút/cảnh báo (giả thuyết, cần đo). |
| **AI step** | Time-series ML/Anomaly Detection phát hiện tổ hợp tín hiệu bất thường và xu hướng suy giảm mà ngưỡng đơn lẻ có thể bỏ sót; tính risk score, phân nhóm xe ưu tiên và chỉ báo giải thích cho kỹ sư; có thể nghiên cứu dự báo SoH/RUL khi có dữ liệu chuẩn và ground truth. Kết quả AI chỉ hỗ trợ sàng lọc và lập kế hoạch kiểm tra, không thay thế ngưỡng an toàn của BMS. |
| **Metric** | ≥80% recall sự kiện suy giảm bất thường đã xác nhận trong tập kiểm thử và khoảng thời gian dự báo; SoH Prediction MAE ≤2 điểm phần trăm trên tập có ground truth xác minh (nếu PoC gồm dự báo SoH); theo dõi False Alarm Rate và Precision, ngưỡng thống nhất trước với Battery Engineering; mục tiêu dài hạn giảm ≥15% số lượt sửa chữa pin ngoài kế hoạch trên 10.000 vehicle-month so nhóm đối chứng tương đương (chỉ đánh giá khi đủ thời gian/số sự kiện). |
| **Quick Architecture** | [x] Rule  [x] ML (time-series) — ML là thành phần chính, rule giữ ngưỡng an toàn. |
| **Ranh giới** | BMS/rule-based tiếp tục xử lý giới hạn điện áp, nhiệt độ, dòng điện và điều kiện bảo vệ an toàn. ML xử lý phân tích đa biến, phát hiện bất thường, dự báo xu hướng. LLM (nếu bổ sung) chỉ diễn giải báo cáo cho kỹ sư, không tham gia quyết định bảo vệ pin. |

*Ghi chú: Template Quick Architecture chỉ có No AI/Rule/LLM/Agent; ML/time-series được ghi bổ sung vì là thành phần chính của bài toán.*

---

## 🗳️ Đề xuất chọn bài toán deep-dive

| Card | Điểm mạnh | Điều cần cân nhắc |
|---|---|---|
| **#01 CSKH & cứu hộ** | Tác động real-time, dễ thấy; AI có thể hỗ trợ trích xuất/tóm tắt hội thoại. | Phụ thuộc API vị trí/trạng thái đội cứu hộ; AI chỉ là một phần; khó đo trong lab; rủi ro điều phối. |
| **#02 Chẩn đoán dịch vụ & bảo hành** | Bottleneck ngôn ngữ + hành chính; dữ liệu văn bản (SOP/TSB/ghi chú); RAG/LLM phù hợp; HITL rõ; metric đo được. | Cần tài liệu có phiên bản/quyền truy cập; cần kiểm chứng baseline. |
| **#03 Sức khỏe pin** | Tác động lớn, AI-upgrade rõ. | Cần dữ liệu BMS/telematics có nhãn/ground truth; thời gian dài; ML không phải LLM. |

**Kết luận:** Chọn **Card #02 — VinFast Service & Warranty Copilot** cho deep-dive. Lý do: bottleneck nằm ở tra cứu tài liệu + soạn hồ sơ 3C (ngôn ngữ, phi cấu trúc), đúng chỗ RAG/LLM có lợi thế; có thể giữ HITL ở bước KTV xác nhận và rule engine ở bước kiểm tra bảo hành; metric (thời gian tra cứu, tỷ lệ hồ sơ bị trả lại) đo được trong PoC hẹp. #01 thiên về điều phối real-time và phụ thuộc nhiều hệ thống; #03 cần dữ liệu cảm biến có nhãn và thời gian quan sát dài. Đây là đề xuất cá nhân; nhóm sẽ chốt.

---

## 📌 Phụ lục A — Điều kiện cần xác minh trước khi xin ngân sách PoC

| Bài toán | Dữ liệu tối thiểu cần tiếp cận | Baseline cần đo | Điều kiện dừng/không triển khai AI |
|---|---|---|---|
| **#01 CSKH & cứu hộ** | Ticket, hội thoại đã xử lý theo quy định dữ liệu, thời điểm tiếp nhận/chuyển nhóm/đóng ticket, danh mục sự cố, lịch sử liên hệ lại. | Median intake/classification time, AHT, FCR, tỷ lệ phân luồng sai, tỷ lệ liên hệ lại. | Nếu phần lớn yêu cầu đã được phân loại bằng rule/API và không còn bottleneck đáng kể thì ưu tiên cải tiến workflow thay vì LLM. |
| **#02 Chẩn đoán dịch vụ & bảo hành** | DTC, VIN/model, tài liệu kỹ thuật/SOP có phiên bản, work order, warranty claim, lý do hồ sơ bị trả lại. | Thời gian tra cứu, thời gian lập hồ sơ, claim rework rate, First-Time Fix Rate. | Nếu tài liệu đã được tìm kiếm tốt và phần lớn thời gian nằm ở kiểm tra vật lý thì RAG/LLM không giải quyết được bottleneck chính. |
| **#03 Sức khỏe pin** | Dữ liệu BMS/telematics có timestamp, cấu hình pin, lịch sử bảo trì/sửa chữa, sự kiện lỗi đã xác nhận và điều kiện vận hành. | Số cảnh báo, precision/recall, false alarm, lead time, sự cố trên vehicle-month, thời gian kỹ sư đánh giá. | Nếu thiếu dữ liệu lịch sử, nhãn sự cố hoặc ground truth đáng tin cậy thì chưa nên triển khai dự báo; tiếp tục dùng rule-based monitoring và cải thiện dữ liệu trước. |

## 🧭 Phụ lục B — Nguyên tắc chọn công nghệ

| Loại công việc | Ưu tiên công nghệ |
|---|---|
| Điều kiện rõ ràng, đầu vào có cấu trúc, cần kết quả nhất quán | Rule-based code / business rules |
| Tra cứu tài liệu nội bộ, cần câu trả lời có dẫn nguồn | RAG |
| Đọc hiểu hội thoại, mô tả sự cố, tài liệu phi cấu trúc và tạo bản nháp | LLM |
| Phát hiện mẫu bất thường hoặc dự báo từ dữ liệu lịch sử theo thời gian | Machine Learning / Time-series ML |
| Điều phối nhiều công cụ và thực hiện chuỗi hành động có kiểm soát | Agent, chỉ cân nhắc khi workflow và quyền hạn đã rõ |

**Nguyên tắc đầu tư:** Không chọn AI chỉ vì bài toán có thể áp dụng AI. Chỉ triển khai AI khi đã chứng minh được bottleneck, có dữ liệu đủ tốt, có baseline để so sánh và AI cải thiện kết quả kinh doanh tốt hơn phương án rule-based/automation đơn giản với tổng chi phí sở hữu hợp lý.
