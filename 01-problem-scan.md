# Problem Scan — Xanh SM

- Người thực hiện: [Điền họ tên]
- Phạm vi phụ trách: Xanh SM — vận hành taxi điện
- Trạng thái: Bản nháp phục vụ bài lab

> Các bài toán dưới đây là đề xuất để khảo sát, chưa phải kết luận về hoạt động thực tế của Xanh SM. Thời gian, tần suất và mức độ ảnh hưởng cần được xác minh; không dùng số liệu giả định như số liệu thực tế.

## Phase 1 — SCAN: 5 bài toán tiềm năng

| # | Công ty | Lens | Ai gặp khó khăn? | Công việc hiện tại cần khảo sát | Điểm nghẽn giả định cần xác minh |
|---|---|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Tài xế và điều phối viên | Tiếp nhận sự cố pin, xác minh vị trí và mức pin, tìm phương án hỗ trợ, soạn hướng dẫn | Tổng hợp thông tin từ nhiều nguồn và soạn hướng dẫn thủ công có thể kéo dài thời gian hỗ trợ |
| 2 | Xanh SM | Pain từ người khác | Tài xế và hành khách | Gọi điện hoặc nhắn tin làm rõ điểm đón khi mô tả của khách khác với vị trí trên bản đồ | Mô tả vị trí thiếu rõ ràng có thể khiến hai bên mất thời gian tìm nhau |
| 3 | Xanh SM | Lặp lại | Nhân viên vận hành | Đọc ghi chú, phản hồi và dữ liệu hủy chuyến để phân loại nguyên nhân | Thông tin dạng văn bản có thể khó tổng hợp và phân loại nhất quán |
| 4 | Xanh SM | Lặp lại | Tài xế và nhân viên hỗ trợ | Tiếp nhận, xác nhận và chuyển yêu cầu thay đổi điểm đến theo quy trình được phép | Yêu cầu thiếu thông tin hoặc nhiều lần chuyển giao có thể gây chậm và sai lệch thông tin |
| 5 | Xanh SM | AI-upgrade | Tài xế và nhân viên hỗ trợ | Đọc phản ánh của tài xế, hỏi bổ sung, phân loại và chuyển bộ phận xử lý | Phản ánh dài hoặc thiếu dữ liệu có thể khiến việc phân loại và chuyển xử lý mất thời gian |

### Thông tin cần bổ sung sau khảo sát

- Nguồn thông tin: [Phỏng vấn / quan sát / tài liệu / dữ liệu mẫu]
- Các bước và công cụ đang sử dụng: [Điền]
- Thời gian xử lý và số lượt: [Điền nếu có bằng chứng]
- Bài toán có ảnh hưởng lớn nhất và lý do: [Điền]

## Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn ba bài toán sau để đánh giá sơ bộ. Có thể thay đổi sau khi khảo sát.

### Card 1 — Hỗ trợ xử lý sự cố pin

- **Bài toán (một câu):** Hỗ trợ điều phối viên tổng hợp thông tin và soạn hướng dẫn xử lý sự cố pin cho tài xế Xanh SM để rút ngắn thời gian xử lý yêu cầu, trong khi mọi hành động vẫn cần người phê duyệt.
- **Actor / người chịu ảnh hưởng:** Điều phối viên là người sử dụng trực tiếp; tài xế chờ hỗ trợ và hành khách có chuyến đi bị gián đoạn là những người chịu ảnh hưởng.
- **Workflow hiện tại (5 bước, giả định theo bài mẫu, cần khảo sát lại):**
  1. Tài xế gọi hoặc nhắn tin báo sự cố pin cho điều phối viên.
  2. Điều phối viên xác minh vị trí, mức pin, dòng xe và tình trạng sự cố.
  3. Tra cứu dữ liệu trạm sạc phù hợp hoặc phương án hỗ trợ được phép.
  4. Soạn và kiểm tra hướng dẫn gửi tài xế hoặc đề xuất hỗ trợ tại chỗ.
  5. Điều phối viên gửi hướng dẫn hoặc liên hệ đơn vị hỗ trợ, sau đó ghi nhận xử lý.
- **Bước chậm hoặc dễ sai nhất:** Bước 3–4: đối chiếu thông tin từ nhiều nguồn và viết hướng dẫn. Rủi ro là dùng dữ liệu trạm đã cũ, thiếu thông tin về xe hoặc đề xuất quãng đường không phù hợp với tình trạng pin.
- **Thời gian xử lý và nguồn đo:** Chưa đo thực tế. Tài liệu `02-deliverable-example.md` minh họa tổng thời gian 15 phút/lượt và 10 phút cho bước 3–4; chỉ dùng các số này làm giả định ban đầu của lab. Cần đo thời gian từ tiếp nhận đủ thông tin đến khi điều phối viên gửi phương án đã duyệt.
- **AI có thể hỗ trợ ở bước nào:** Tóm tắt mô tả sự cố ở bước 2 và soạn bản nháp tiếng Việt ở bước 4 dựa trên dữ liệu đã xác minh. Việc lấy dữ liệu vị trí/trạm do phần mềm tích hợp thực hiện; LLM không tự bịa dữ liệu hoặc khoảng cách. Prototype hiện chỉ nhận dữ liệu trong đầu vào, chưa kết nối các hệ thống vận hành.
- **Metric thành công có số (mục tiêu đề xuất, chưa phải kết quả):**
  - Thời gian xử lý trung bình dưới 3 phút/lượt trên tập tình huống mô phỏng có đủ dữ liệu; so sánh với cách xử lý thủ công trên cùng tập tình huống trước khi kết luận mức cải thiện.
  - Ít nhất 90% bản nháp được người đánh giá chấp nhận mà không cần sửa nội dung nghiệp vụ trên tập 20 tình huống mô phỏng.
  - 100% phản hồi trong bộ test bắt đầu bằng `[DRAFT_ONLY]`; mọi trường hợp pin dưới 5% phải đề xuất `dispatch_mobile_charger` và không hướng dẫn đến trạm xa hơn 5 km. Đây là tiêu chí của lab, không phải bảo đảm cho mọi tình huống thực tế.
- **Quick Architecture:** Rule + LLM Feature + Human-in-the-loop. Rule kiểm tra ngưỡng pin và dữ liệu bắt buộc; LLM xử lý mô tả tự nhiên và soạn lời nhắn; điều phối viên xác minh và phê duyệt trước khi gửi tin hoặc điều hỗ trợ. Quy trình có các bước cố định nên chưa cần Agent tự trị.
- **Operational Boundary / Fallback:** Chỉ tạo bản nháp; không tự gửi tin, không thực hiện điều xe và không tuyên bố đã thực hiện. Theo quy tắc lab, khi pin dưới 5%, đề xuất xe sạc di động để người điều phối duyệt. Thiếu hoặc mâu thuẫn dữ liệu thì yêu cầu xác minh; bản nháp sai hoặc API lỗi thì chuyển về xử lý thủ công.
- **Bằng chứng cần thu thập:** Phỏng vấn điều phối viên để xác minh quy trình; đo thời gian xử lý mẫu; chuẩn bị tình huống mô phỏng có đáp án mong đợi; ghi kết quả chạy prototype và đánh giá bản nháp. Hiện chưa thực hiện các hoạt động này.

### Card 2 — Làm rõ điểm đón

- **Bài toán (một câu):** Hỗ trợ làm rõ mô tả điểm đón của hành khách để tài xế và hành khách thống nhất đúng vị trí, giảm thời gian trao đổi qua lại.
- **Actor / người chịu ảnh hưởng:** Tài xế và nhân viên hỗ trợ là người sử dụng đề xuất; hành khách chịu ảnh hưởng khi chờ lâu hoặc phải giải thích nhiều lần.
- **Workflow hiện tại (5 bước, giả định cần khảo sát):**
  1. Tài xế nhận vị trí điểm đón và ghi chú của hành khách.
  2. Đối chiếu vị trí trên bản đồ với mô tả như tên cổng, số nhà hoặc địa danh.
  3. Khi chưa rõ hoặc có mâu thuẫn, gọi hoặc nhắn tin hỏi thêm hành khách.
  4. Hai bên xác nhận lại điểm đón cụ thể và cập nhật theo quy trình được phép.
  5. Tài xế di chuyển theo bản đồ và quy định giao thông để đón khách.
- **Bước chậm hoặc dễ sai nhất:** Bước 2–3: hiểu mô tả mơ hồ như “cổng sau” hoặc “đối diện quán cà phê” và đặt câu hỏi bổ sung đúng thông tin còn thiếu. Cùng một địa danh có thể có nhiều cổng hoặc nhiều vị trí trùng tên.
- **Thời gian xử lý và nguồn đo:** Chưa đo. Cần đo từ lúc phát hiện điểm đón chưa rõ đến khi hai bên xác nhận được vị trí; ghi số lượt trao đổi và tách thời gian làm rõ khỏi thời gian xe di chuyển.
- **AI có thể hỗ trợ ở bước nào:** Trích xuất địa danh, cổng, số nhà từ ghi chú ở bước 2; phát hiện thông tin chưa đủ và soạn bản nháp câu hỏi ở bước 3. Việc đối chiếu tọa độ và tìm đường do dịch vụ bản đồ thực hiện; LLM chỉ sử dụng kết quả được cung cấp.
- **Metric thành công có số (mục tiêu đề xuất, chưa phải kết quả):**
  - Thời gian làm rõ điểm đón trung bình không quá 2 phút trên 20 tình huống mô phỏng; so sánh với cách làm thủ công trên cùng tập tình huống.
  - Ít nhất 90% câu hỏi nháp được người đánh giá chấp nhận vì hỏi đúng thông tin còn thiếu và không thêm thông tin sai.
  - Không có tọa độ, địa danh hoặc xác nhận của hành khách bị bịa trong bộ test; mọi tình huống thiếu dữ liệu phải hỏi bổ sung hoặc chuyển người xử lý.
- **Quick Architecture:** Rule + LLM Feature + Human-in-the-loop. Rule kiểm tra trường dữ liệu bắt buộc; LLM hiểu mô tả tiếng Việt và soạn câu hỏi; tài xế hoặc nhân viên hỗ trợ duyệt trước khi gửi, hành khách xác nhận điểm đón. Nếu chỉ thiếu một trường cố định, ưu tiên biểu mẫu hoặc câu hỏi mẫu thay vì gọi LLM.
- **Operational Boundary / Fallback:** Không tự đổi điểm đón, không tự gửi tin và không tuyên bố khách đã xác nhận. Không suy đoán tọa độ từ mô tả mơ hồ hoặc hướng dẫn dừng đỗ trái quy định. Khi bản đồ và mô tả mâu thuẫn, chuyển người hỗ trợ xác minh; hệ thống lỗi thì quay về gọi hoặc nhắn tin theo quy trình hiện có.
- **Bằng chứng cần thu thập:** Ghi chú điểm đón mẫu đã ẩn thông tin cá nhân; phỏng vấn tài xế; đo thời gian và số lượt trao đổi; chuẩn bị tình huống có đáp án về thông tin cần hỏi thêm. Hiện chưa có dữ liệu khảo sát hoặc kết quả thử nghiệm.

### Card 3 — Phân loại nguyên nhân hủy chuyến

- **Bài toán (một câu):** Hỗ trợ nhân viên vận hành phân loại lý do hủy chuyến từ ghi chú và phản hồi dạng văn bản để tổng hợp các vấn đề cần cải thiện nhanh và nhất quán hơn.
- **Actor / người chịu ảnh hưởng:** Nhân viên phân tích vận hành là người sử dụng trực tiếp; quản lý vận hành dùng báo cáo để xác định vấn đề cần khảo sát thêm; tài xế và hành khách có thể chịu ảnh hưởng nếu thông tin bị diễn giải sai.
- **Workflow hiện tại (5 bước, giả định cần khảo sát):**
  1. Thu thập dữ liệu chuyến bị hủy và ghi chú liên quan từ nguồn được phép truy cập.
  2. Loại bản ghi trùng, ẩn thông tin cá nhân không cần thiết và chuẩn hóa dữ liệu.
  3. Đọc từng ghi chú, đối chiếu danh mục lý do và gán nhãn.
  4. Kiểm tra các trường hợp mơ hồ, có nhiều lý do hoặc thông tin mâu thuẫn.
  5. Tổng hợp số lượng, tỷ lệ theo nhóm lý do và viết báo cáo để quản lý xem xét.
- **Bước chậm hoặc dễ sai nhất:** Bước 3–4: đọc nhiều cách diễn đạt khác nhau và phân biệt lý do được ghi nhận với suy đoán của người viết. Ví dụ “đợi lâu nên đặt xe khác” có nhiều ý; cần quy tắc gán nhãn nhất quán thay vì tùy người đọc.
- **Thời gian xử lý và nguồn đo:** Chưa đo. Cần ghi tổng thời gian phân loại, kiểm tra và sửa nhãn cho một lô dữ liệu; tính thời gian trung bình mỗi bản ghi và so sánh thủ công với có AI trên cùng tập dữ liệu.
- **AI có thể hỗ trợ ở bước nào:** Đề xuất nhãn từ văn bản ở bước 3, kèm đoạn bằng chứng trong đầu vào; đánh dấu trường hợp cần người kiểm tra ở bước 4. Danh mục thử nghiệm gồm: chờ đón lâu, khó thống nhất điểm đón, thay đổi nhu cầu, sự cố phương tiện, khác và không đủ thông tin. Các nhãn này cần được người phụ trách nghiệp vụ xác nhận trước khi dùng.
- **Metric thành công có số (mục tiêu đề xuất, chưa phải kết quả):**
  - Giảm ít nhất 50% tổng thời gian phân loại và kiểm tra so với xử lý thủ công trên tập 50 ghi chú mô phỏng có nhãn tham chiếu do người đánh giá chốt trước.
  - Macro-F1 đạt ít nhất 0,85 trên tập đánh giá, tức chất lượng phân loại được tính riêng cho từng nhóm rồi lấy trung bình để tránh nhóm nhiều mẫu che lấp nhóm ít mẫu. Báo cáo kèm số mẫu từng nhóm và các trường hợp nhầm lẫn.
  - 100% nhãn được chấp nhận phải có đoạn bằng chứng phù hợp trong đầu vào; bản ghi không có căn cứ phải được đánh dấu không đủ thông tin hoặc chuyển người đánh giá.
- **Quick Architecture:** Rule + LLM Feature + Human Review. Dùng mã lý do có cấu trúc hoặc rule khi dữ liệu đã rõ; dùng LLM cho ghi chú tự do; người đánh giá xử lý trường hợp mơ hồ và duyệt báo cáo. Tổng hợp số lượng và tỷ lệ bằng code thông thường, không giao LLM tự tính. Chưa cần Agent vì đây là quy trình xử lý theo lô có các bước cố định.
- **Operational Boundary / Fallback:** Chỉ phân loại lý do được ghi nhận, không khẳng định nguyên nhân gốc hoặc quy lỗi khi chưa có bằng chứng. Không tự phạt tài xế, hoàn tiền hay sửa dữ liệu chuyến. Chỉ dùng dữ liệu được phép và đã giảm thiểu thông tin cá nhân; ngoài phạm vi thử nghiệm văn bản thì chưa xử lý ghi âm. Thiếu dữ liệu, nhãn ngoài danh mục hoặc đầu ra sai định dạng thì chuyển người phân loại thủ công.
- **Bằng chứng cần thu thập:** Danh mục nhãn được thống nhất; tập ghi chú đã ẩn thông tin cá nhân hoặc dữ liệu mô phỏng; nhãn tham chiếu và quy tắc giải quyết bất đồng; thời gian xử lý thủ công và kết quả thử nghiệm. Hiện chưa có các kết quả này; dữ liệu dùng để sửa prompt phải tách khỏi tập đánh giá cuối.

## Đề xuất bài toán để phân tích sâu

- **Đề xuất ban đầu:** Hỗ trợ điều phối viên xử lý sự cố pin của tài xế Xanh SM.
- **Lý do sơ bộ:** Phạm vi phù hợp với starter code `starter-code/prompt_prototype.py`, có thể thử nghiệm việc tạo bản nháp và tuân thủ ranh giới vận hành.
- **Cần xác minh trước khi chốt:** Quy trình thực tế, dữ liệu đầu vào sẵn có, baseline thời gian xử lý và nhu cầu của người sử dụng.
