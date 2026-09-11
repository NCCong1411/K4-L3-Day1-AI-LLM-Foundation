# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Ở temperature 0.0, 0.5 và 1.0, mô hình đều chọn sự thật về hang Sơn Đoòng và chỉ thay đổi cách diễn đạt hoặc một vài chi tiết. Khi tăng lên 1.5, mô hình chuyển sang sự thật khác về cà phê Việt Nam, cho thấy temperature cao làm kết quả đa dạng và khó dự đoán hơn. Độ trễ trong bốn lần chạy không tăng/giảm theo temperature một cách cố định vì còn phụ thuộc vào mạng và tải hệ thống.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ chọn temperature 0.2 cho chatbot hỗ trợ khách hàng. Mức thấp giúp câu trả lời nhất quán, bám sát chính sách và giảm khả năng trả lời sáng tạo nhưng không chính xác; đồng thời vẫn đủ tự nhiên khi giao tiếp.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Tổng số output token mỗi ngày là 10.000 × 3 × 350 = 10.500.000 token, tương đương 10.500 đơn vị 1.000 token. Theo bảng giá của bài, GPT-4o tốn 10.500 × $0.010 = $105/ngày, còn GPT-4o-mini tốn 10.500 × $0.0006 = $6.30/ngày; vì vậy GPT-4o đắt hơn khoảng 16,7 lần. GPT-4o đáng dùng cho tác vụ cần suy luận và độ chính xác cao như phân tích hợp đồng hoặc báo cáo quan trọng; GPT-4o-mini phù hợp cho FAQ, phân loại ticket và các tác vụ đơn giản số lượng lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Persona giáo viên dùng từ đơn giản và ví dụ “chuỗi hộp”, “sợi xích” để trẻ em dễ hiểu. Persona chuyên gia dài hơn và dùng thuật ngữ như sổ cái phân tán, mã băm, nút mạng, đồng thuận. System prompt quyết định giọng điệu, mức độ chi tiết và cách model điều chỉnh câu trả lời cho từng đối tượng.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Hai con số chênh lệch: 2.11% với đoạn văn 95 từ tiếng việt.
Tiếng Việt thường tốn nhiều token hơn tiếng Anh cùng độ dài vì có nhiều ký tự có dấu như "ă, ơ, ư, ệ," khiến tokenizer có thể tách thành nhiều phần nhỏ. Ngoài ra, tiếng Việt thường viết cách giữa các âm tiết, nên một từ có nhiều âm tiết có thể được xử lý thành nhiều token hơn.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> > Streaming quan trọng khi phản hồi dài hoặc cần tạo cảm giác chatbot đang trả lời ngay, ví dụ trợ lý hội thoại, hỗ trợ khách hàng, viết nội dung hay sinh mã nguồn. Người dùng thấy token đầu tiên sớm nên thời gian chờ cảm nhận được giảm, dù tổng thời gian xử lý có thể không đổi. Non-streaming phù hợp hơn khi cần nhận toàn bộ kết quả rồi mới xử lý, kiểm tra định dạng, lưu dữ liệu hoặc trả về JSON có cấu trúc.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff tăng thời gian chờ sau mỗi lần lỗi, giúp API có thời gian hồi phục và giảm số request gửi lại khi hệ thống đang quá tải. So với delay cố định, nó tránh việc client retry liên tục với cùng tần suất. Nếu hàng nghìn client đều chờ đúng 1 giây rồi retry, họ có thể gửi request lại cùng lúc, tạo “retry storm” và làm API càng quá tải hơn.
---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Tôi chọn persona là trợ giảng hỗ trợ người mới học AI. System prompt: “Bạn là trợ giảng thân thiện của khóa học AI, giải thích các khái niệm bằng tiếng Việt rõ ràng, ngắn gọn và kèm ví dụ đơn giản khi cần.” Cụm “bằng tiếng Việt” giúp người học dễ tiếp thu. Cụm “ngắn gọn” giúp câu trả lời tập trung, không quá dài.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là trợ lý chỉ lưu 3 lượt hội thoại gần nhất, nên dễ quên ngữ cảnh hoặc yêu cầu từ các lượt trước. Cải thiện: thêm bộ nhớ tóm tắt. Khi lịch sử vượt 3 lượt, tóm tắt các tin nhắn cũ thành một đoạn ngắn, lưu đoạn đó và gửi kèm 3 lượt gần nhất trong các lần gọi API tiếp theo.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
