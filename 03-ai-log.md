# 03 — AI Log & Reflection (Bùi Đức Tuấn – 2A202603902)

## Nhật ký dùng AI (sự kiện thật)

| Việc | Công cụ | AI giúp gì | AI sai / hời hợt ở đâu | Đã sửa gì |
|---|---|---|---|---|
| Đọc đề | Claude Code | Tóm tắt lại đề bài (mini lab + lab chính), các file cần nộp và cách chấm; cho một ví dụ Quick Problem Card để xem format | Không thấy sai sót | Không cần sửa |
| Đánh giá ý tưởng (Phase 1–2) | Claude Code | Xem xét khả năng/tiềm năng của 5 ý tưởng VinWonders: chỉ ra dòng nào thiếu số liệu, dòng nào đang viết giải pháp thay vì vấn đề | Sai sót khi tìm kiếm thông tin: không biết VinWonders đã có lựa chọn quét QR cho vé mua online | Tôi tự verify lại trên trang chính thức của VinWonders *(link: …)* |
| Stress-test Card #1 | Gemini (`gemini-3.5-flash-lite`), prompt "CFO khắt khe" trong worksheet | Chỉ ra 3 điểm yếu: logic hành vi khách, metric thiếu giá trị tài chính, Rule (dữ liệu quét vé) rẻ hơn camera AI | Chỉ đưa ra đánh giá, chưa có nguồn cho các khẳng định về hệ thống hiện có | Không sửa prompt. Tôi phản biện từng điểm, kiểm tra lại thông tin, rồi sửa card #1: bỏ camera, đếm hàng chờ bằng Rule, AI chỉ dự báo + gợi ý thứ tự chơi, thêm metric throughput và F&B |
| Viết `prompt_prototype.py` | Claude Code + Gemini API | Viết `SYSTEM_PROMPT` ranh giới, hàm `evaluate_prompt()`, thêm Test Case 3 (admin giả); 3/3 test pass, autograder 5/5 | Bản đầu crash trên Windows (stdout cp1252 không in được emoji); đọc tên model trước khi nạp `.env`; ranh giới phạm vi quá chặt nên Gemini từ chối nhầm tin chúc khách vô hại | Sửa encoding stdout, đọc model lúc gọi API, nới quy tắc phạm vi cho tin lịch sự ngắn |

Để tránh sai sót lần sau, tôi sẽ thêm vào system prompt: *"Với các quy trình chính thống của một tổ chức, nếu không chắc thì đánh dấu là chưa chắc; nếu khẳng định thì phải cite đúng nguồn."*

## Reflection (tự viết — không dùng AI viết thay)

**AI đã giúp tôi ở đâu?**

AI giúp tôi trong việc tóm tắt lại đề bài, ví dụ về bài. Xem xét đánh giá khả năng/ tiềm năng của ý tưởng tôi.

**AI trả lời sai / hallucination ở đâu, tôi phát hiện bằng cách nào?**

Sau khi tôi review thì AI sai sót ở trong tìm kiếm thông tin và không biết rằng Vinwonder có lựa chọn quét QR của vé mua Online nên tôi đã đi verify lại trên trang chính thức của Vinwonder.

**Tôi đã sửa prompt / ranh giới thế nào để ra kết quả đúng?**

Lần này tôi không sửa prompt vì nó chỉ đưa ra đánh giá, tôi phản biện và xem xét các khía cạnh.

**Lần sau tôi sẽ dùng AI khác đi thế nào?**

Để tránh trường hợp sai sót về sau thì tôi nghĩ sẽ để system prompt rằng đối với các quy trình chính thống của các tổ chức, hãy đánh dấu là chưa chắc và nếu chắc thì phải cite đúng nguồn.
