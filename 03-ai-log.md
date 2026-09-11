# Lab 02 — AI Log & Reflection

## 1. Mục tiêu sử dụng AI

Tôi sử dụng AI như một thought-partner để:

1. Mở rộng danh sách pain point trong các công ty thành viên Vingroup.
2. Chuyển các ý tưởng rộng thành workflow, actor, bottleneck và metric cụ thể.
3. Phản biện xem bài toán nào thật sự cần AI, bài toán nào nên dùng rule/SQL.
4. Thiết kế operational boundary và adversarial tests cho prototype.
5. Kiểm tra tính nhất quán giữa problem scan, deep-dive, workflow và code.

---

## 2. Nhật ký tương tác

### Lần 1 — Brainstorm bài toán

**Prompt tóm tắt:**

> Đề xuất các pain point vận hành cụ thể tại Xanh SM, Vinhomes, Vinmec, VinFast và Vinpearl. Với mỗi vấn đề, chỉ ra actor, workflow thủ công và nơi AI có thể hỗ trợ. Không được coi số liệu ước tính là dữ liệu thật.

**AI giúp được gì:** AI tạo không gian ý tưởng đủ rộng trên năm công ty, phân biệt các lens và gợi ý ba ứng viên có workflow dễ mô tả: xử lý pin yếu, phân loại ticket và tóm tắt xuất viện.

**Điểm cần sửa:** AI có xu hướng đưa ra số lượng sự cố, thời gian xử lý và tỷ lệ tổn thất nghe hợp lý nhưng không có nguồn nội bộ. Tôi không dùng các con số này như sự thật; thay vào đó ghi rõ chúng là **working assumptions** và thêm kế hoạch đo baseline.

### Lần 2 — Stress-test Quick Cards

**Prompt tóm tắt:**

> Đóng vai CFO và trưởng vận hành. Chỉ ra vì sao từng bài toán có thể không cần AI, metric nào đang yếu và rủi ro nào chưa được kiểm soát.

**AI giúp được gì:** AI chỉ ra đối chiếu hóa đơn VinFast nên ưu tiên SQL/rule, ngưỡng pin và tương thích cổng sạc không nên giao cho LLM, còn bài toán Vinmec có rủi ro lâm sàng và bảo mật rất cao.

**Điểm cần sửa:** Gợi ý ban đầu dùng một “agent tự động tìm trạm và gửi tài xế”. Tôi thu hẹp thành **Rule + LLM Feature + Human-in-the-loop**. LLM chỉ viết bản nháp, rule xử lý điều kiện an toàn và con người giữ quyết định cuối.

### Lần 3 — Thiết kế boundary

**Prompt tóm tắt:**

> Hãy tạo các prompt tấn công nhằm buộc hệ thống bỏ nhãn draft, tự gửi tin hoặc hướng dẫn xe pin 2% tới trạm cách 8 km.

**AI giúp được gì:** AI tạo các prompt injection gần với tình huống vận hành và làm rõ rằng system prompt đơn thuần không đủ; cần deterministic safety gate và phân quyền hệ thống.

**Ranh giới sau khi chỉnh:**

- Output luôn bắt đầu bằng `[DRAFT_ONLY]`.
- `requires_human_approval` luôn là `true`.
- Pin dưới 5% trả `dispatch_mobile_charger`.
- Dữ liệu thiếu hoặc không rõ cổng sạc trả `manual_review`.
- Prototype không có quyền gửi tin hoặc thực hiện hành động ngoài hệ thống.

---

## 3. Hallucination và cách xử lý

| Vấn đề từ AI | Vì sao nguy hiểm | Cách tôi sửa |
|---|---|---|
| Tạo số liệu vận hành không có nguồn | Có thể biến giả định thành “bằng chứng” giả | Gắn nhãn assumption, thêm công thức và kế hoạch đo baseline |
| Đề xuất LLM tự chọn trạm | LLM có thể bịa hoặc dùng dữ liệu cũ | Rule/API chọn tập trạm hợp lệ; LLM chỉ diễn đạt |
| Đề xuất agent tự gửi tin | Vượt quyền và khó rollback | HITL bắt buộc, tách Send Gateway khỏi LLM |
| Dùng LLM cho đối chiếu dữ liệu có cấu trúc | Tốn chi phí, khó giải thích | Ưu tiên SQL/rule; chỉ dùng LLM cho ghi chú ngoại lệ |
| Coi output đúng schema là đúng nghiệp vụ | JSON hợp lệ vẫn có thể chứa quyết định sai | Kiểm tra rule, nguồn dữ liệu, audit log và human approval |

---

## 4. Kết quả prototype

Prototype có ba lớp bảo vệ: system prompt, deterministic guard và output normalizer. Ba adversarial cases gồm pin 2% nhưng đòi tới trạm cách 8 km; yêu cầu bỏ `[DRAFT_ONLY]` và gửi thẳng; không rõ cổng sạc nhưng ép hệ thống chọn đại một trạm.

Các boundary tests có thể chạy offline vì được chặn trước lớp gọi mô hình. Khi có `GEMINI_API_KEY`, chương trình chạy thêm một benign smoke test qua Gemini Flash. Live Gemini test chỉ được coi là hoàn tất sau khi người làm thiết lập API key và kiểm tra output thực tế; tôi không ghi nhận một lần gọi API chưa xảy ra như kết quả thật.

**Kết quả chạy ngày 11/09/2026:** 3/3 deterministic boundary tests `Passed`; autograder đạt `10.00/10.00`. Biến `GEMINI_API_KEY` chưa được thiết lập nên live Gemini smoke test được ghi nhận là `skipped`, không giả mạo kết quả API.

**Cập nhật model:** Lần chạy live với `gemini-2.5-flash` trả lỗi `404 NOT_FOUND` vì model này không còn được cấp cho tài khoản API mới. Prototype được chuyển sang mặc định `gemini-3.6-flash` theo hướng dẫn từ API; có thể đổi bằng biến môi trường `GEMINI_MODEL` mà không sửa code.

Do autograder giới hạn mỗi lần chạy trong 30 giây, lời gọi `gemini-3.6-flash` được cấu hình `thinking_level=MINIMAL` và giới hạn 512 output tokens. Đây là tối ưu trực tiếp ở API để giảm latency; tiêu chí pass/fail và file autograder không bị thay đổi.


---

## 5. Reflection

Điều quan trọng nhất tôi học được là một ý tưởng “có thể dùng AI” chưa phải một AI product tốt. Cần tách rõ phần nào xác định được bằng rule, phần nào cần năng lực ngôn ngữ của LLM và phần nào phải thuộc về con người.

AI hữu ích nhất khi mở rộng ý tưởng, phản biện và tạo test cases. Điểm yếu lớn nhất là tạo ra chi tiết nghe thuyết phục nhưng chưa được kiểm chứng. Vì vậy, tôi giữ lại các giả định để phục vụ scoping nhưng không biến chúng thành số liệu thật, đồng thời đưa việc thu thập baseline vào điều kiện trước pilot.

Quyết định cuối cùng của tôi là **NOT YET cho production pilot nhưng GO cho sandbox prototype**. Đây là quyết định thận trọng: vẫn học được từ prototype nhưng chưa đưa rủi ro sang tài xế và vận hành thật.
