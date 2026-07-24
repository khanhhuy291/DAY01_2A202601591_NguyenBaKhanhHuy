# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> *Câu trả lời của bạn*
Khi temperature = 0.0, mô hình trả lời dập khuôn, chính xác và bám sát vào các sự thật trong câu. Khi tăng lên 0.5 và 1.0 câu trả lời trở nên tự nhiên và đa dạng hơn về chủ đề. Nhưng ở mức 1.5 câu trả lời bắt đầu xuất hiện sự lộn xộn, từ vựng ghép nối thiếu logic hoặc sinh ra các thông tin sai lệch (hallucination).

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> *Câu trả lời của bạn*
Nên đặt temperature ở mức thấp, khoảng 0.1 đến 0.2.Do chatbot hỗ trợ khách hàng cần ưu tiên tính chính xác và tuân thủ theo tài liệu có sẵn, tránh tuyệt đối việc AI bịa ra các thông tin không có thật gây thiệt hại lớn.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> *Câu trả lời của bạn*
Khi chạy test ở Part 1 với câu:"Hãy kể cho tôi một sự thật thú vị về Việt Nam.". Thì GPT-4o hết $0.001097 đắt hơn GPT-4o-mini hết $0.002338 chỉ gấp đôi nhưng Theo bảng giá hiện hành, GPT-4o thường đắt hơn GPT-4o-mini khoảng 30 lần cho input và 25 lần cho output
GPT-4o xứng đáng với chi phí khi dùng để suy luận logic phức tạp, viết code chuyên sâu hoặc xử lý ngữ cảnh đa phương tiện.Còn GPT-4o-mini hoàn toàn phù hợp và tối ưu chi phí cho các tác vụ đơn giản như phân loại văn bản, tóm tắt đoạn chat, hoặc chatbot giao tiếp thông thường.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> *Câu trả lời của bạn*
Phản hồi của "giáo viên" rất ngắn gọn, dùng ví dụ gần gũi như "cuốn sổ cái chung của cả lớp không ai được tẩy xóa" và hoàn toàn không có từ ngữ chuyên ngành. Phản hồi của "chuyên gia" lại dài hơn hẳn, chứa đầy thuật ngữ như "sổ cái phân tán", "hàm băm mã hóa", "cơ chế đồng thuận". Điều này cho thấy System prompt có sức mạnh định hình hoàn toàn vai trò, giọng điệu, tệp từ vựng và đối tượng mục tiêu của mô hình.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> *Câu trả lời của bạn*
Số token thực tế đo bằng tiktoken cao hơn khoảng 50-80% so với ước lượng số từ / 0.75 . Tiếng Việt tốn nhiều token hơn vì các tokenizer (như BPE) được huấn luyện chủ yếu trên bộ dữ liệu tiếng Anh; do đó, nhiều từ tiếng Việt không nằm trong từ điển và bị tokenizer cắt vụn thành nhiều sub-word hoặc từng ký tự rời rạc để mã hóa.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> *Câu trả lời của bạn*
Streaming đặc biệt quan trọng trong các ứng dụng có giao diện người dùng trực tiếp (như chatbot web/app) vì nó giảm thiểu Time to First Token (TTFT), giúp người dùng đọc kết quả ngay lập tức thay vì nhìn màn hình chờ trống không, tạo cảm giác phản hồi nhanh. Ngược lại, non-streaming phù hợp hơn cho các luồng xử lý ngầm (background jobs) như sinh báo cáo tự động, phân tích log, hoặc khi hệ thống cần parse toàn bộ kết quả đầu ra dưới dạng chuẩn (như JSON) trước khi gọi tiếp một API khác.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> *Câu trả lời của bạn*
Exponential backoff giúp giãn cách thời gian giữa các lần thử lại (1s, 2s, 4s...), nhờ đó giảm áp lực ngay lập tức lên server đang bị lỗi. Nếu hàng nghìn client cùng retry với một delay cố định 1 giây, chúng sẽ tạo ra hiện tượng "Thundering Herd" (cơn lốc request), khiến server vừa mới kịp hồi phục lại lập tức bị đánh sập bởi lượng truy cập ồ ạt ập đến cùng một lúc.
---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> *Câu trả lời của bạn*
Tôi chọn persona là một "Trợ lý Kỹ sư Backend và AI". System prompt: "Bạn là một Senior Software Engineer chuyên về Python, FastAPI và các mô hình Deep Learning. Hãy trả lời ngắn gọn, cung cấp code snippet có gán type hint rõ ràng và ưu tiên kiến trúc microservices. Bỏ qua các phần giải thích thừa." Yêu cầu "trả lời ngắn gọn và bỏ qua giải thích thừa" giúp tiết kiệm token và tăng tốc độ đọc hiểu; việc "ưu tiên microservices và type hint" định hướng mô hình sinh ra mã nguồn có thể áp dụng trực tiếp vào các dự án hệ thống phân tán.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> *Câu trả lời của bạn*
Hạn chế lớn nhất là trợ lý hiện không có bộ nhớ dài hạn, không thể nhớ được các file code cũ, cấu hình database hay bối cảnh dự án qua các phiên làm việc khác nhau. Để cải thiện, tôi sẽ tích hợp hệ thống Retrieval-Augmented Generation (RAG). Cách triển khai là dùng vector database (như pgvector) để lưu trữ tài liệu và source code; khi người dùng hỏi, hệ thống sẽ retrieve các đoạn code liên quan nhất để đưa vào ngữ cảnh trước khi gọi LLM.
---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
