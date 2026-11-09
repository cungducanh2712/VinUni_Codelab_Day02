# Danh sách bài toán AI tiềm năng cho VinFast

> **Phạm vi tài liệu:** Tổng hợp 5 bài toán vận hành đã khảo sát ban đầu và 3 Quick Problem Card được chọn để phân tích sâu.  
> **Lưu ý về dữ liệu:** Các workflow dưới đây là giả thuyết nghiệp vụ cần xác minh với đơn vị vận hành. Các KPI có số là mục tiêu đề xuất cho PoC, không phải kết quả hiện tại hay cam kết hiệu quả của VinFast. Chưa có baseline nội bộ thì cần đo trước khi kết luận về mức tiết kiệm.

## 1. List bài toán của tôi

### List bài toán của tôi:

| **#** | **Subsidiary (VinFast/Xanh SM...)** | **Lens** | **Mô tả ngắn bài toán** |
| ----- | ----------------------------------- | -------- | ----------------------- |
| 1 | VinFast | Repetitive; AI-upgrade; Stakeholder Pain | **CSKH và điều phối cứu hộ:** Tiếp nhận yêu cầu từ hotline/ứng dụng, thu thập thông tin xe và sự cố, phân loại yêu cầu rồi chuyển đến bộ phận kỹ thuật hoặc đội cứu hộ phù hợp. Điểm cần khảo sát là thời gian hỏi lại thông tin, phân loại sai và chuyển tiếp giữa các bộ phận. |
| 2 | VinFast | Repetitive; AI-upgrade; Stakeholder Pain | **Chẩn đoán dịch vụ và xử lý bảo hành:** Kỹ thuật viên/cố vấn dịch vụ tra cứu DTC, tài liệu kỹ thuật, SOP và lịch sử sửa chữa; sau đó lập hồ sơ sửa chữa, chuẩn bị warranty claim. Điểm cần khảo sát là thời gian tìm kiếm thông tin, nhập liệu và tỷ lệ hồ sơ bị trả lại. |
| 3 | VinFast | AI-upgrade; Stakeholder Pain | **Quản lý sức khỏe pin và bảo trì dự đoán:** Phân tích dữ liệu BMS/telematics theo thời gian để nhận diện xu hướng suy giảm bất thường, ưu tiên xe cần kiểm tra và hỗ trợ lập kế hoạch bảo trì. Cần chứng minh mô hình cải thiện được hệ thống ngưỡng/cảnh báo hiện có và không tạo quá nhiều cảnh báo giả. |
| 4 | VinFast | Repetitive; AI-upgrade; Stakeholder Pain | **Phân tích lỗi và tìm nguyên nhân gốc rễ trong nhà máy:** Liên kết dữ liệu lỗi chất lượng, log thiết bị, thông số công đoạn, lô linh kiện và lịch sử bảo trì để giúp kỹ sư khoanh vùng nguyên nhân, đánh giá phạm vi ảnh hưởng và lập báo cáo khắc phục. Trọng tâm không chỉ là phát hiện lỗi mà là rút ngắn thời gian điều tra sự cố. |
| 5 | VinFast | Repetitive; AI-upgrade; Stakeholder Pain | **Quản lý rủi ro nhà cung cấp và thiếu hụt linh kiện:** Tổng hợp tình trạng giao hàng, tồn kho, lead time, chất lượng và thông tin từ nhà cung cấp để cảnh báo nguy cơ giao trễ/thiếu vật tư, đồng thời hỗ trợ đội Procurement ưu tiên xử lý. Cần xác minh mức độ phân tán dữ liệu và phần công việc hiện vẫn phải tổng hợp thủ công. |

### Ghi chú về cách hiểu các lens

- **Repetitive:** Công việc được thực hiện thường xuyên, có khối lượng đủ lớn và tiêu tốn thời gian nhân sự.
- **AI-upgrade:** Chất lượng hoặc tốc độ xử lý hiện tại có thể được cải thiện nhờ hiểu ngôn ngữ tự nhiên, khai thác dữ liệu phi cấu trúc, phát hiện bất thường hoặc dự báo.
- **Stakeholder Pain:** Có nhóm nhân viên, khách hàng hoặc đối tác chịu ảnh hưởng trực tiếp từ chậm trễ, sai sót hay việc phải xử lý lại.

Một quy trình lặp lại không đồng nghĩa phải dùng AI. Nếu đầu vào có cấu trúc và quy tắc xử lý ổn định, workflow automation hoặc rule-based code có thể là lựa chọn tốt hơn.

---

## 2. Quick Problem Cards

### QUICK PROBLEM CARD #01

**Bài toán (1 câu):** Giảm thời gian tiếp nhận và phân loại yêu cầu CSKH/cứu hộ bằng cách tự động trích xuất thông tin từ hội thoại, chuẩn hóa ticket và đề xuất nhóm xử lý phù hợp.

**Công ty thành viên:** [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  [ ] Vinmec  [ ] Khác: __________

**Ai đang đau (Actor)?** Nhân viên tổng đài CSKH, nhân viên điều phối cứu hộ, kỹ thuật viên hiện trường và khách hàng gặp sự cố với xe.

**Workflow thủ công hiện tại (giả thuyết cần xác minh):**

1. Khách hàng gọi hotline hoặc gửi yêu cầu qua ứng dụng.
2. Nhân viên hỏi và ghi nhận thông tin xe, VIN, vị trí, tình trạng và mô tả sự cố.
3. Nhân viên phân loại vấn đề, tra cứu lịch sử và xác định mức độ ưu tiên.
4. Nhân viên liên hệ bộ phận kỹ thuật hoặc đơn vị cứu hộ phù hợp.
5. Nhân viên theo dõi trạng thái xử lý và cập nhật cho khách hàng.

**Bước nào tốn thời gian/lỗi nhất?** Bước 2 và 3: thông tin ban đầu không đầy đủ, phải hỏi lại; mô tả sự cố khó chuẩn hóa hoặc phân loại nhầm.  
**Thời gian:** Chưa có baseline đáng tin cậy. Cần đo median số phút từ lúc tiếp nhận đến khi ticket được phân loại và chuyển đúng nhóm xử lý.

**AI có thể nhảy vào hỗ trợ ở bước nào?**

- Dùng Speech-to-Text để chuyển hội thoại thành văn bản.
- Dùng NLP/LLM để nhận diện ý định, trích xuất VIN, vị trí, triệu chứng và tóm tắt sự cố thành ticket có cấu trúc.
- Đề xuất nhóm xử lý và mức độ ưu tiên dựa trên nội dung đã trích xuất.
- Nhân viên xác nhận các trường hợp không chắc chắn; các quy tắc an toàn và điều phối cuối cùng vẫn do hệ thống nghiệp vụ có kiểm soát thực hiện.

**Đo thành công bằng gì (Metric có số)?** *(Mục tiêu PoC đề xuất, cần hiệu chỉnh sau khi đo baseline)*

- Giảm **ít nhất 30%** median thời gian tiếp nhận và phân loại ticket so với baseline.
- Đạt **ít nhất 95%** độ chính xác phân luồng trên tập đánh giá đã gán nhãn.
- Giảm **ít nhất 20%** tỷ lệ khách hàng phải liên hệ lại trong 7 ngày vì ticket bị phân loại thiếu/sai thông tin.
- Đạt **100% recall trên tập kiểm thử các tình huống nguy hiểm đã được gán nhãn** đối với yêu cầu phải chuyển cấp khẩn cấp. Đây là tiêu chí kiểm thử, không phải cam kết an toàn tuyệt đối trong vận hành.

**Quick Architecture:** [ ] No AI  [x] Rule  [x] LLM  [ ] Agent

**Ranh giới Rule-based và AI:** Geospatial lookup, kiểm tra trạng thái đội cứu hộ, điều kiện phân cấp và quy tắc ưu tiên nên dùng API/rule-based. LLM chỉ nên xử lý phần hội thoại tự do, trích xuất và tóm tắt thông tin. Chưa cần AI Agent tự quyết định điều phối trong MVP.

---

### QUICK PROBLEM CARD #02

**Bài toán (1 câu):** Giảm thời gian kỹ thuật viên/cố vấn dịch vụ tra cứu tài liệu kỹ thuật và chuẩn bị hồ sơ sửa chữa, bảo hành bằng một Service & Warranty Copilot có dẫn nguồn.

**Công ty thành viên:** [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  [ ] Vinmec  [ ] Khác: __________

**Ai đang đau (Actor)?** Kỹ thuật viên tại xưởng dịch vụ, cố vấn dịch vụ, nhân viên quản lý bảo hành và bộ phận kỹ thuật.

**Workflow thủ công hiện tại (giả thuyết cần xác minh):**

1. Tiếp nhận xe, xác định VIN, model, phiên bản và tình trạng xe.
2. Đọc mã lỗi DTC và kiểm tra thông tin kỹ thuật ban đầu.
3. Tra cứu tài liệu sửa chữa, SOP và lịch sử các lỗi/trường hợp tương tự.
4. Kiểm tra xe, xác định phương án sửa chữa và linh kiện cần thay thế.
5. Nhập nội dung sửa chữa, chuẩn bị hồ sơ và gửi yêu cầu bảo hành theo quy trình.

**Bước nào tốn thời gian/lỗi nhất?** Bước 3 và 5: tìm kiếm thông tin ở nhiều tài liệu/hệ thống, tổng hợp lịch sử và nhập hồ sơ. Cần tách thời gian tra cứu, thời gian nhập liệu và thời gian kiểm tra kỹ thuật.  
**Thời gian:** Chưa có baseline. Đo median số phút tra cứu tài liệu và chuẩn bị hồ sơ trên mỗi work order; không gộp thời gian sửa chữa vật lý vào metric này.

**AI có thể nhảy vào hỗ trợ ở bước nào?**

- Dùng RAG trên tài liệu kỹ thuật, SOP và hướng dẫn sửa chữa đã được phê duyệt để tìm đúng nội dung theo model/phiên bản/VIN.
- Dùng LLM tóm tắt mô tả sự cố, giải thích nội dung tài liệu trong phạm vi nguồn, tổng hợp trường hợp tương tự và tạo bản nháp biên bản/work order/warranty claim.
- Câu trả lời phải kèm tài liệu nguồn hoặc báo rõ không đủ căn cứ; kỹ thuật viên xác nhận chẩn đoán và phương án sửa chữa.
- Kiểm tra điều kiện bảo hành, định mức và điều khoản loại trừ bằng rule-based engine, không giao quyền quyết định cho LLM.

**Đo thành công bằng gì (Metric có số)?** *(Mục tiêu PoC đề xuất, cần hiệu chỉnh sau khi đo baseline)*

- Giảm **ít nhất 30%** median thời gian tra cứu tài liệu và chuẩn bị hồ sơ trên mỗi work order.
- Giảm **ít nhất 25%** tỷ lệ hồ sơ bị trả lại do thiếu/sai thông tin.
- **100% câu trả lời trong tập đánh giá** phải dẫn được nguồn tài liệu phù hợp hoặc từ chối kết luận khi không đủ căn cứ.
- First-Time Fix Rate không thấp hơn nhóm đối chứng; theo dõi thêm tỷ lệ sửa chữa lại để bảo đảm tiết kiệm thời gian giấy tờ không làm giảm chất lượng sửa chữa.

**Quick Architecture:** [ ] No AI  [x] Rule  [x] LLM  [ ] Agent

**Ranh giới Rule-based và AI:** RAG/LLM phù hợp với tìm kiếm, đọc hiểu tài liệu và soạn bản nháp. Kiểm tra thời hạn bảo hành, mã linh kiện được bảo hiểm, điều kiện loại trừ, định mức và tính đầy đủ của trường dữ liệu nên dùng rule-based/business workflow. Không dùng LLM để tự phê duyệt bảo hành hoặc tự quyết định thay linh kiện.

---

### QUICK PROBLEM CARD #03

**Bài toán (1 câu):** Phát hiện sớm xu hướng suy giảm bất thường của pin từ dữ liệu vận hành để ưu tiên kiểm tra và lập kế hoạch bảo trì trước khi xảy ra sự cố.

**Công ty thành viên:** [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  [ ] Vinmec  [ ] Khác: __________

**Ai đang đau (Actor)?** Đội ngũ Battery Engineering, Remote Diagnostics, quản lý chất lượng, bảo hành và vận hành xưởng dịch vụ.

**Workflow hiện tại (giả thuyết cần xác minh):**

1. BMS/telematics ghi nhận dữ liệu điện áp, nhiệt độ, dòng điện, trạng thái sạc/xả và các chỉ số pin liên quan.
2. Hệ thống hiện tại áp dụng ngưỡng/cảnh báo để phát hiện một số điều kiện bất thường.
3. Nhân viên kỹ thuật kiểm tra cảnh báo, dữ liệu theo thời gian và lịch sử vận hành của xe.
4. Kỹ sư đánh giá mức độ rủi ro, phân biệt bất thường nhất thời với xu hướng suy giảm và quyết định có cần kiểm tra hay không.
5. Bộ phận vận hành lên lịch kiểm tra, bảo trì hoặc xử lý bảo hành theo quy trình được phê duyệt.

**Bước nào tốn thời gian/lỗi nhất?** Bước 3 và 4: xem xét dữ liệu theo thời gian, đối chiếu nhiều tín hiệu và ưu tiên xe cần kiểm tra. Rủi ro khác là cảnh báo giả nhiều hoặc bỏ sót trường hợp suy giảm bất thường.  
**Thời gian:** Chưa có baseline. Đo median số phút kỹ sư đánh giá mỗi cảnh báo, số cảnh báo trên 1.000 vehicle-month và tỷ lệ cảnh báo được xác nhận là vấn đề thực tế.

**AI có thể nhảy vào hỗ trợ ở bước nào?**

- Dùng Time-series ML/Anomaly Detection để phát hiện tổ hợp tín hiệu bất thường và xu hướng suy giảm mà ngưỡng đơn lẻ có thể bỏ sót.
- Tính risk score, phân nhóm xe cần ưu tiên kiểm tra và đưa ra chỉ báo giải thích cho kỹ sư.
- Có thể nghiên cứu dự báo SoH/RUL khi có dữ liệu chuẩn và nhãn/ground truth đáng tin cậy.
- Kết quả AI chỉ hỗ trợ sàng lọc và lập kế hoạch kiểm tra. BMS và logic an toàn đã được xác nhận tiếp tục chịu trách nhiệm bảo vệ pin; mô hình AI không thay thế các ngưỡng an toàn.

**Đo thành công bằng gì (Metric có số)?** *(Mục tiêu nghiên cứu/PoC đề xuất; cần xác định rõ định nghĩa sự kiện và thời hạn dự báo trước khi kiểm thử)*

- Đạt **ít nhất 80% recall** đối với các sự kiện suy giảm bất thường đã được xác nhận trong tập kiểm thử và trong khoảng thời gian dự báo đã định.
- SoH Prediction MAE **không quá 2 điểm phần trăm** trên tập kiểm thử có ground truth được xác minh, nếu PoC bao gồm dự báo SoH. Không dùng metric này thay thế metric phát hiện sự cố.
- Theo dõi False Alarm Rate và Precision; ngưỡng chấp nhận phải được thống nhất trước với Battery Engineering để tránh tạo thêm tải kiểm tra không cần thiết.
- Mục tiêu vận hành dài hạn: giảm **ít nhất 15%** số lượt sửa chữa pin ngoài kế hoạch trên mỗi 10.000 vehicle-month so với nhóm đối chứng tương đương. Chỉ đánh giá khi đủ thời gian và số lượng sự kiện.

**Quick Architecture:** [ ] No AI  [x] Rule  [ ] LLM  [ ] Agent

**Ranh giới Rule-based và AI:** Rule-based/BMS tiếp tục xử lý giới hạn điện áp, nhiệt độ, dòng điện và các điều kiện bảo vệ an toàn. ML xử lý phân tích đa biến, phát hiện bất thường và dự báo xu hướng. LLM chỉ có thể được bổ sung để diễn giải báo cáo cho kỹ sư, không tham gia quyết định bảo vệ pin.

---

## 3. Điều kiện cần xác minh trước khi xin ngân sách PoC

| Bài toán | Dữ liệu tối thiểu cần tiếp cận | Baseline cần đo | Điều kiện dừng/không triển khai AI |
|---|---|---|---|
| #01 CSKH và cứu hộ | Ticket, hội thoại đã được xử lý theo quy định dữ liệu, thời điểm tiếp nhận/chuyển nhóm/đóng ticket, danh mục sự cố, lịch sử liên hệ lại | Median intake/classification time, AHT, FCR, tỷ lệ phân luồng sai, tỷ lệ liên hệ lại | Nếu phần lớn yêu cầu đã được phân loại bằng rule/API và không còn bottleneck đáng kể thì ưu tiên cải tiến workflow thay vì LLM. |
| #02 Chẩn đoán dịch vụ và bảo hành | DTC, VIN/model, tài liệu kỹ thuật/SOP có phiên bản, work order, warranty claim, lý do hồ sơ bị trả lại | Thời gian tra cứu, thời gian lập hồ sơ, claim rework rate, First-Time Fix Rate | Nếu tài liệu đã được tìm kiếm tốt và phần lớn thời gian nằm ở kiểm tra vật lý thì RAG/LLM không giải quyết được bottleneck chính. |
| #03 Sức khỏe pin | Dữ liệu BMS/telematics có timestamp, cấu hình pin, lịch sử bảo trì/sửa chữa, sự kiện lỗi đã xác nhận và điều kiện vận hành | Số cảnh báo, precision/recall, false alarm, lead time, sự cố trên vehicle-month, thời gian kỹ sư đánh giá | Nếu thiếu dữ liệu lịch sử, nhãn sự cố hoặc ground truth đáng tin cậy thì chưa nên triển khai dự báo; tiếp tục dùng rule-based monitoring và cải thiện dữ liệu trước. |

## 4. Nguyên tắc lựa chọn công nghệ

| Loại công việc | Ưu tiên công nghệ |
|---|---|
| Điều kiện rõ ràng, đầu vào có cấu trúc, cần kết quả nhất quán | Rule-based code / business rules |
| Tra cứu tài liệu nội bộ, cần câu trả lời có dẫn nguồn | RAG |
| Đọc hiểu hội thoại, mô tả sự cố, tài liệu phi cấu trúc và tạo bản nháp | LLM |
| Phát hiện mẫu bất thường hoặc dự báo từ dữ liệu lịch sử theo thời gian | Machine Learning / Time-series ML |
| Điều phối nhiều công cụ và thực hiện chuỗi hành động có kiểm soát | Agent, chỉ cân nhắc khi workflow và quyền hạn đã rõ |

**Nguyên tắc đầu tư:** Không chọn AI chỉ vì bài toán có thể áp dụng AI. Chỉ triển khai AI khi đã chứng minh được bottleneck, có dữ liệu đủ tốt, có baseline để so sánh và AI cải thiện kết quả kinh doanh tốt hơn phương án rule-based/automation đơn giản với tổng chi phí sở hữu hợp lý.
