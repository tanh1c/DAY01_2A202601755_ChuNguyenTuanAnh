# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay từng dòng trả lời mẫu bằng câu trả lời
thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Khi temperature tăng từ 0.0 lên 1.5, phản hồi thường chuyển từ ổn định, trực tiếp và dễ lặp lại sang đa dạng, bất ngờ hơn nhưng cũng dễ lan man hoặc kém chính xác. Với cùng prompt, mức 0.0–0.5 phù hợp khi cần tính nhất quán, còn 1.0–1.5 tạo nhiều cách kể sáng tạo hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi chọn temperature khoảng 0.2. Chatbot hỗ trợ cần câu trả lời nhất quán, chính xác và bám chính sách hơn là sáng tạo; vẫn giữ một ít độ linh hoạt để cách diễn đạt không quá máy móc.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Workload tạo 10.000 × 3 × 350 = 10,5 triệu output token/ngày. Theo bảng giá, GPT-4o tốn khoảng 10.500 × 0,010 = 105 USD/ngày, còn GPT-4o-mini khoảng 10.500 × 0,0006 = 6,30 USD/ngày, nên GPT-4o đắt hơn khoảng 16,7 lần nếu chỉ xét output. GPT-4o đáng dùng cho phân tích khó hoặc quyết định có giá trị cao; mini phù hợp với FAQ, phân loại và hội thoại khối lượng lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Persona giáo viên thường dùng câu ngắn, từ phổ thông và ví dụ như một cuốn sổ chung mà nhiều bạn cùng kiểm tra. Persona chuyên gia có xu hướng dài và dày thuật ngữ hơn, chẳng hạn sổ cái phân tán, đồng thuận, hàm băm và tính bất biến. System prompt không đổi câu hỏi nhưng định hướng đối tượng độc giả, độ sâu, giọng điệu và loại ví dụ mà model lựa chọn.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với đoạn tiếng Việt 109 từ về việc dùng AI trong giáo dục, `count_tokens` cho GPT-4o trả 128 token, còn công thức số từ/0,75 ước lượng khoảng 145,3 token. Ước lượng cao hơn kết quả tiktoken khoảng (145,3 − 128) / 128 × 100 = 13,5%. Số token phụ thuộc bộ mã hóa chứ không chỉ số từ; dấu thanh, ký tự Unicode và các âm tiết tiếng Việt có thể bị tách thành nhiều mảnh hơn những cụm tiếng Anh phổ biến trong dữ liệu huấn luyện.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi phản hồi dài hoặc model có độ trễ cao, vì người dùng thấy nội dung xuất hiện ngay và biết hệ thống đang hoạt động. Non-streaming phù hợp khi phản hồi rất ngắn, khi chương trình cần toàn bộ JSON để kiểm tra trước khi xử lý, hoặc khi nội dung phải được kiểm duyệt hoàn chỉnh trước khi hiển thị.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giãn nhanh khoảng cách giữa các lần thử, giảm lưu lượng gửi vào dịch vụ đang quá tải và cho hệ thống thời gian phục hồi. Nếu hàng nghìn client cùng retry với delay cố định, chúng có thể thức dậy đồng thời và tạo các đợt tải lặp lại (thundering herd); trong thực tế nên cộng thêm jitter ngẫu nhiên để phân tán các lần retry.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> System prompt: "Bạn là trợ giảng thân thiện của khóa AI. Hãy trả lời ngắn gọn bằng tiếng Việt, giải thích thuật ngữ bằng ví dụ thực tế và nói rõ khi bạn không chắc chắn." Cụm "ngắn gọn bằng tiếng Việt" giữ phản hồi dễ đọc và phù hợp người học; yêu cầu "nói rõ khi không chắc chắn" hạn chế việc trình bày suy đoán như sự thật.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là chỉ giữ ba lượt gần nhất nên trợ lý nhanh chóng quên mục tiêu và dữ kiện cũ. Có thể cải thiện bằng cách tóm tắt các message sắp bị loại thành một đoạn `conversation_summary`, gửi đoạn này sau system prompt ở các lượt tiếp theo, đồng thời vẫn giữ ba lượt gần nhất để bảo toàn chi tiết mới.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
