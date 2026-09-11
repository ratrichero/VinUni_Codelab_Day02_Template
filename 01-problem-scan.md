# Lab 02 — Problem Scan & Quick Assessment

**Bối cảnh:** AI Product Scoping cho Vin Smart Future  
**Branch cá nhân:** `Thang_02638`  
**Ghi chú về số liệu:** Các thời gian và mục tiêu bên dưới là **giả định làm việc cho bài lab**, chưa phải số liệu nội bộ đã được Vingroup xác nhận. Trước khi triển khai thật cần đo baseline từ log vận hành.

---

## Phase 1 — SCAN: Tìm kiếm cơ hội

| # | Công ty thành viên | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | **Xanh SM** | Tốn thời gian / Stakeholder Pain | Khi xe sắp hết pin ngoài thực địa, điều phối viên phải nhận thông tin qua điện thoại, tra vị trí GPS, kiểm tra trạm sạc phù hợp và soạn hướng dẫn thủ công. Tài xế phải chờ trong khi xe tiếp tục tiêu hao pin. |
| 2 | **Vinhomes** | AI-upgrade / Lặp lại | Nhân viên CSKH phải đọc từng phản ánh tự do của cư dân, xác định chủ đề, mức khẩn cấp và chuyển sang kỹ thuật, an ninh, vệ sinh hoặc tài chính. Phản ánh có thể bị chuyển sai hoặc phản hồi chậm. |
| 3 | **Vinmec** | Tốn thời gian / Lặp lại | Bác sĩ phải tổng hợp ghi chú điều trị, kết quả xét nghiệm và toa thuốc để tạo bản tóm tắt xuất viện. Công việc lặp lại nhưng yêu cầu độ chính xác và trách nhiệm chuyên môn cao. |
| 4 | **VinFast** | Lặp lại | Nhân viên tài chính/vận hành phải đối chiếu hóa đơn sạc với log phiên sạc, mã trạm, thời gian và số điện tiêu thụ từ nhiều nguồn. Các trường hợp lệch cần được phát hiện và chuyển người kiểm tra. |
| 5 | **Vinpearl / VinWonders** | AI-upgrade / Stakeholder Pain | Khách thường hỏi về loại vé, giờ hoạt động, điều kiện đổi vé và dịch vụ đi kèm. Câu hỏi tự do hoặc có nhiều điều kiện khiến chatbot theo kịch bản trả lời thiếu chính xác và tổng đài bị quá tải. |

### Sàng lọc ban đầu

| Bài toán | Tác động kỳ vọng | Dữ liệu cần có | Rủi ro nếu AI sai | Hướng giải pháp phù hợp |
|---|---|---|---|---|
| Xanh SM — xe sắp hết pin | Cao, ảnh hưởng vận hành thời gian thực | Mức pin, GPS, loại xe, trạm sạc | Cao nhưng kiểm soát được bằng rule + người duyệt | **Rule + LLM feature** |
| Vinhomes — phân loại khiếu nại | Trung bình–cao | Ticket lịch sử và taxonomy | Trung bình | **Rule + LLM feature** |
| Vinmec — tóm tắt xuất viện | Cao | Hồ sơ đã ẩn danh, biểu mẫu chuẩn | Rất cao | **LLM feature + bác sĩ duyệt** |
| VinFast — đối chiếu hóa đơn | Trung bình–cao | Hóa đơn và log có khóa đối chiếu | Trung bình | **Rule/SQL trước, AI cho ngoại lệ** |
| Vinpearl — hỗ trợ hỏi/đổi vé | Trung bình | Chính sách vé cập nhật và dữ liệu đặt chỗ | Trung bình–cao | **RAG + rule + chuyển nhân viên** |

---

## Phase 2 — QUICK-ASSESS: Top 3 Problem Cards

## Quick Problem Card #1 — Xanh SM xử lý xe sắp hết pin

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Điều phối viên Xanh SM cần xử lý nhanh và an toàn khi tài xế báo xe sắp hết pin ngoài thực địa. |
| **Công ty thành viên** | Xanh SM (GSM) |
| **Actor** | Điều phối viên là người vận hành chính; tài xế là người chịu ảnh hưởng trực tiếp. |
| **Workflow hiện tại** | 1. Tài xế gọi/báo sự cố → 2. Điều phối viên ghi nhận biển số, mức pin và vị trí → 3. Tra GPS và trạm sạc → 4. Soạn hướng dẫn → 5. Gửi tài xế hoặc gọi cứu hộ. |
| **Bottleneck** | Bước 3–4: tra trạm phù hợp và soạn hướng dẫn, giả định mất khoảng **10 phút/lượt**. |
| **AI hỗ trợ** | Tóm tắt tình huống và tạo bản nháp hướng dẫn từ dữ liệu đã được rule kiểm tra. |
| **Success metric** | Giảm tổng thời gian xử lý từ baseline giả định **15 phút xuống dưới 3 phút**; ≥ **95%** đề xuất đúng loại cổng/trạm; **100%** nội dung được người điều phối duyệt trước khi gửi. |
| **Quick Architecture** | **Hybrid Rule + LLM Feature**. Rule xử lý ngưỡng pin/khoảng cách; LLM chỉ soạn bản nháp. |
| **Boundary sơ bộ** | Luôn gắn `[DRAFT_ONLY]`; không tự gửi; pin dưới 5% thì không hướng dẫn đi trạm xa và phải đề xuất `dispatch_mobile_charger`. |

## Quick Problem Card #2 — Vinhomes phân loại khiếu nại cư dân

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Phân loại và chuyển phản ánh của cư dân đến đúng bộ phận nhanh hơn mà không tự đưa ra cam kết pháp lý/tài chính. |
| **Công ty thành viên** | Vinhomes |
| **Actor** | Nhân viên CSKH, ban quản lý tòa nhà và cư dân. |
| **Workflow hiện tại** | 1. Nhận ticket → 2. Đọc nội dung → 3. Xác định chủ đề/mức khẩn → 4. Chuyển bộ phận → 5. Theo dõi phản hồi. |
| **Bottleneck** | Bước 2–4 vì nội dung tự do, có thể chứa nhiều vấn đề; giả định mất **5–8 phút/ticket**. |
| **AI hỗ trợ** | Tóm tắt, gắn nhãn chủ đề, phát hiện mức khẩn và đề xuất hàng đợi xử lý. |
| **Success metric** | ≥ **85%** ticket được phân loại trong **10 giây**; tỷ lệ chuyển sai dưới **5%**; 100% ticket an ninh/an toàn được chuyển người xử lý ngay. |
| **Quick Architecture** | **Rule + LLM Feature**. Rule ưu tiên từ khóa khẩn cấp, LLM hiểu nội dung tự do. |
| **Boundary sơ bộ** | Không tự cam kết hoàn tiền/bồi thường; không kết luận tranh chấp; trường hợp khẩn hoặc độ tin cậy thấp phải chuyển người. |

## Quick Problem Card #3 — Vinmec tạo bản nháp tóm tắt xuất viện

| Trường | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Hỗ trợ bác sĩ tạo bản nháp tóm tắt xuất viện từ dữ liệu hồ sơ đã chọn, giảm thời gian nhập liệu nhưng giữ quyền quyết định cho bác sĩ. |
| **Công ty thành viên** | Vinmec |
| **Actor** | Bác sĩ điều trị; bệnh nhân là người chịu ảnh hưởng. |
| **Workflow hiện tại** | 1. Mở hồ sơ → 2. Đọc ghi chú/xét nghiệm → 3. Chọn thông tin quan trọng → 4. Viết tóm tắt và hướng dẫn → 5. Kiểm tra, ký. |
| **Bottleneck** | Bước 2–4, giả định mất **20–30 phút/hồ sơ**. |
| **AI hỗ trợ** | Tạo bản nháp theo biểu mẫu chuẩn và đánh dấu dữ liệu còn thiếu/mâu thuẫn. |
| **Success metric** | Giảm thời gian soạn xuống dưới **10 phút/hồ sơ**; **100%** bản nháp được bác sĩ kiểm tra và ký; không tự bổ sung dữ kiện ngoài hồ sơ. |
| **Quick Architecture** | **LLM Feature có Human-in-the-loop bắt buộc**. |
| **Boundary sơ bộ** | Không chẩn đoán mới, không kê đơn, không thay đổi liều, không tự gửi cho bệnh nhân; dữ liệu phải được bảo vệ và kiểm soát truy cập. |

---

## Quyết định chọn bài toán để Deep Dive

Chọn **Quick Problem Card #1 — Xanh SM xử lý xe sắp hết pin**.

### Lý do chọn

1. Quy trình có đầu vào, đầu ra và bottleneck tương đối rõ.
2. Có thể chia hợp lý: rule quyết định an toàn, LLM soạn ngôn ngữ, con người phê duyệt.
3. Metric thời gian, độ chính xác và tỷ lệ duyệt có thể đo được.
4. Có thể tạo prototype mà chưa cần cho AI quyền thực hiện hành động ngoài hệ thống.

### Vì sao chưa chọn hai card còn lại

- **Vinhomes:** Cần taxonomy ticket và dữ liệu lịch sử đủ sạch trước khi đánh giá độ chính xác phân loại.
- **Vinmec:** Tác động cao nhưng dữ liệu nhạy cảm và rủi ro lâm sàng lớn; cần quy trình bảo mật, đánh giá y khoa và phê duyệt chặt hơn trước khi thử nghiệm.

