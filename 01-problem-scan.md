# 01 — Problem Scan & Quick Cards (Bùi Đức Tuấn – 2A202603902)

> Phase 1 + Phase 2 trong [01-worksheet.md](01-worksheet.md). Cả 5 bài toán quét tại **VinWonders** qua đủ 4 lenses. Số liệu có ghi *(ước tính)* là ước lượng, chưa có dữ liệu nội bộ.

## Phase 1 — SCAN

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | VinWonders | Tốn thời gian | Giờ cao điểm cuối tuần, du khách phải chờ ~45–60 phút ở các trò chơi hot trong khi trò chơi khác gần đó chỉ chờ ~5 phút, vì khách không biết trò nào đang vắng *(ước tính)*. |
| 2 | VinWonders | Lặp lại | Khách mất thời gian ở cổng: xếp hàng mua vé, khai báo, đồng ý điều khoản, rồi xếp hàng thuê tủ đồ; ~20–30 phút/khách, lặp lại với hàng nghìn khách mỗi sáng cuối tuần *(ước tính)*. |
| 3 | VinWonders | AI-upgrade | Khách khó tìm được địa điểm ăn uống hợp khẩu vị trong khu: phải đi xem từng quầy hoặc đọc review rời rạc, mất ~15–20 phút mỗi bữa *(ước tính)*. |
| 4 | VinWonders | Tốn thời gian | Đánh giá của khách sau trải nghiệm nằm rải rác trên nhiều nền tảng (Google Maps, Facebook, TikTok, OTA); bộ phận CSKH/marketing phải đọc thủ công để tìm điểm cần cải thiện, ~5–8 giờ/tuần *(ước tính)*. |
| 5 | VinWonders | Pain từ người khác | Nhân viên không thể đi tuần liên tục toàn khu nên không biết khách nào đang bối rối, lạc đường hay mất đồ; khách phải tự đi tìm nhân viên ~10 phút mới được hỗ trợ *(ước tính)*. |

## Phase 2 — 3 Quick Problem Cards

Chọn **#1, #2, #5**: cả 3 có bottleneck là một bước cụ thể, đo được bằng phút, và xảy ra hằng ngày.

### Card #1 — Chờ trò chơi quá lâu, phân bố khách không đều
| Trường | Nội dung |
|---|---|
| Bài toán (1 câu) | Giờ cao điểm, khách dồn vào vài trò chơi hot và chờ rất lâu trong khi trò khác vắng, vì không ai biết lưu lượng thực tế từng trò. |
| Công ty thành viên | VinWonders |
| Actor | Du khách (chờ đợi); nhân viên vận hành trò chơi (quá tải ở trò hot, rảnh ở trò vắng) |
| Workflow thủ công | 1. Khách xem bản đồ, chọn trò (2') → 2. Đi bộ tới trò (5–10') → 3. Thấy hàng dài, đứng chờ (45–60') 🔴 → 4. Chơi (5'); hoặc bỏ hàng sang trò khác, mất thêm ~10' *(ước tính)* |
| Bước tốn nhất (⏱) | Bước 3: chờ trong hàng, ~45–60 phút/lượt ở trò hot *(ước tính)* |
| AI nhảy vào bước nào | Trước bước 1. **Rule:** dùng dữ liệu quét vé/vòng đeo tay sẵn có ở cổng hàng chờ để tính thời gian chờ hiện tại (số người trong hàng × thời gian 1 lượt ÷ công suất). **AI:** dự báo thời gian chờ 30–60 phút tới từ lịch sử, giờ trong ngày, thời tiết, lượng khách vào cổng, và gợi ý thứ tự chơi (ví dụ: "chơi trò B bây giờ, quay lại trò A lúc 14h") trên app/bảng điện tử. |
| Metric có số | Thời gian chờ trung bình ở trò hot từ ~50' → dưới 30'; sai số dự báo dưới ±5'; số lượt chơi/khách/ngày tăng ~20%; chi tiêu F&B/khách tăng nhờ thời gian không phải xếp hàng *(mục tiêu, ước tính)* |
| Quick Architecture | [ ] No AI [x] **Rule** (đếm hàng chờ từ dữ liệu quét vé) [ ] LLM [ ] Agent — [x] **AI khác: mô hình dự báo** (thời gian chờ + gợi ý thứ tự chơi) |

### Card #2 — Check-in ở cổng mất thời gian
| Trường | Nội dung |
|---|---|
| Bài toán (1 câu) | Khách phải làm nhiều thủ tục tại cổng (mua vé, khai báo, đồng ý điều khoản, thuê tủ đồ) nên mất 20–30 phút trước khi được vào khu *(ước tính)*. |
| Công ty thành viên | VinWonders |
| Actor | Du khách tại cổng; nhân viên bán vé / soát vé / quầy tủ đồ |
| Workflow thủ công | 1. Xếp hàng mua vé tại quầy (10–15') 🔴 → 2. Khai báo thông tin, đồng ý điều khoản (3') → 3. Soát vé vào cổng (2') → 4. Xếp hàng thuê tủ đồ (5–10') 🔴 *(ước tính)* |
| Bước tốn nhất (⏱) | Bước 1 và 4: hai lần xếp hàng, tổng ~15–25 phút/khách *(ước tính)* |
| AI nhảy vào bước nào | Không cần AI: cho khách tự check-in online trước (mua vé, khai báo, đồng ý điều khoản, thuê tủ đồ), đến cổng chỉ quét QR. |
| Metric có số | Thời gian từ lúc đến cổng tới lúc vào khu từ ~25' → dưới 5' với khách đã check-in online; ≥ 50% khách dùng check-in online *(mục tiêu)* |
| Quick Architecture | [ ] No AI [x] **Rule** (form online + mã QR) [ ] LLM [ ] Agent |

### Card #5 — Khách cần hỗ trợ nhưng nhân viên không biết
| Trường | Nội dung |
|---|---|
| Bài toán (1 câu) | Khách bối rối, lạc đường hoặc mất đồ phải tự đi tìm nhân viên, vì nhân viên không thể đi tuần liên tục toàn khu. |
| Công ty thành viên | VinWonders |
| Actor | Nhân viên hỗ trợ / an ninh (đi tuần tốn sức); du khách cần giúp đỡ |
| Workflow thủ công | 1. Khách gặp vấn đề, tự đi tìm nhân viên hoặc quầy thông tin (~10') 🔴 → 2. Nhân viên hỏi, gọi bộ đàm cho khu liên quan (3–5') → 3. Xử lý: chỉ đường / tìm đồ (5–15') *(ước tính)* |
| Bước tốn nhất (⏱) | Bước 1: thời gian từ lúc khách cần giúp tới lúc gặp được nhân viên, ~10 phút/lượt *(ước tính)* |
| AI nhảy vào bước nào | Bước 1: camera nhận diện khách có dấu hiệu bối rối, lạc đường, hoặc ra hiệu với camera, rồi gửi thông báo cho nhân viên gần nhất; nhân viên xác nhận và tới hỗ trợ. |
| Metric có số | Thời gian từ lúc khách cần hỗ trợ tới khi có nhân viên từ ~10' → dưới 3' *(mục tiêu)* |
| Quick Architecture | [ ] No AI [ ] Rule [ ] LLM [ ] Agent — [x] **AI khác: Computer Vision** (nhận diện hành vi / cử chỉ) + thông báo cho nhân viên |

## Stress-test bằng AI (prompt "CFO khắt khe" trong worksheet)
Đã stress-test **Card #1** bằng Gemini (`gemini-3.5-flash-lite`).

| Điểm yếu AI chỉ ra | Đồng ý? | Đã sửa gì |
|---|---|---|
| Logic: chỉ hiển thị thời gian chờ chưa chắc khách đổi trò, khách vẫn chờ trò biểu tượng | Đồng ý | Thêm gợi ý thứ tự chơi: khách vẫn được chơi trò hot nhưng vào giờ ít chờ hơn |
| Metric: phút chờ không có giá trị tài chính, thiếu throughput và chi tiêu F&B | Đồng ý | Thêm metric số lượt chơi/khách/ngày và chi tiêu F&B/khách |
| Rule vs AI: dùng camera AI để đếm người là over-engineering; dữ liệu quét vé/vòng tay sẵn có ở cổng hàng chờ đếm được rẻ và chính xác hơn | Đồng ý (đã kiểm tra: VinWonders có quét vé/vòng tay) | Bỏ camera; phần đếm dùng Rule trên dữ liệu quét vé, AI chỉ dùng cho dự báo và gợi ý |
