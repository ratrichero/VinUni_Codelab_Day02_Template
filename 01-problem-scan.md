# 01-problem-scan.md — Quét cơ hội & Đánh giá nhanh bài toán AI (Vin Smart Future)

**Dự án:** Lab 02: AI Product Scoping (Vin Smart Future)  
**Engineer:** Tạ Việt Cường (cuongtv_2560)  
**Vai trò:** AI Product Engineer tại Vin Smart Future (Vingroup)  

---

## 🏛️ Bối cảnh: Vin Smart Future

Vin Smart Future chịu trách nhiệm tìm kiếm các cơ hội chuyển đổi số và ứng dụng Trí tuệ nhân tạo (AI) vào các đơn vị thành viên của Tập đoàn Vingroup (VinFast, Xanh SM, Vinhomes, Vinmec, Vinpearl...) nhằm giải quyết các điểm nghẽn (bottlenecks) vận hành thực tế, cắt giảm thời gian xử lý thủ công và nâng cao chất lượng dịch vụ.

---

# 🔍 Phase 1 — SCAN: Bảng quét cơ hội (4 Lenses)

Sử dụng bộ khung **4 Lenses** (Lặp lại, Tốn thời gian, AI có thể tốt hơn, Pain từ người khác) để rà soát quy trình vận hành tại các đơn vị thành viên Vingroup:

| # | Đơn vị thành viên (Subsidiary) | Tên bài toán / Nghiệp vụ | Ống kính (Lens) | Mô tả ngắn bài toán & Điểm nghẽn thực tế |
|---|---|---|---|---|
| **1** | **Xanh SM (GSM)** | Xử lý sự cố sạc pin thực địa & điều phối cứu hộ | *Tốn thời gian* | Tài xế gọi tổng đài báo cạn pin hoặc gặp trạm sạc quá tải. Điều phối viên phải tra cứu thủ công toạ độ GPS, mở dashboard tìm trạm sạc VinFast trống phù hợp loại cổng xe (VF5/VF8), mất 15-20 phút/lượt. Rủi ro xe cạn kiệt pin giữa đường nếu pin < 5% mà hướng dẫn đi trạm sạc xa. |
| **2** | **VinFast** | Chẩn đoán sơ bộ lỗi kỹ thuật từ mô tả tiếng Việt của khách | *AI có thể tốt hơn* | Khách hàng mô tả lỗi bằng ngôn ngữ đời thường (VD: *"xe qua gờ giảm tốc kêu cụp cụp ở bánh trước"*). CSKH mất 10-15 phút tra sổ tay kỹ thuật để gắn mã lỗi sơ bộ cho xưởng dịch vụ VinFast. |
| **3** | **Vinhomes** | Phân loại & định tuyến tự động phản ánh cư dân trên App Vinhomes Resident | *Lặp lại* | Tiếp nhận hàng nghìn phản ánh/ngày (mất nước, đèn hành lang cháy, tiếng ồn, đăng ký sửa chữa...). Nhân viên CSKH đọc và phân loại thủ công mất 12-24 giờ để chuyển đến đúng Ban quản lý (BQL) từng tòa nhà. |
| **4** | **Vinmec** | Trợ lý soạn nháp bản tóm tắt hồ sơ xuất viện (Discharge Summary) | *Tốn thời gian* | Bác sĩ điều trị mất 20-30 phút/bệnh nhân để tổng hợp các kết quả xét nghiệm, chẩn đoán hình ảnh và diễn tiến bệnh thành văn bản xuất viện tóm tắt bằng ngôn ngữ dễ hiểu cho bệnh nhân. |
| **5** | **Vinpearl** | Giám sát & cảnh báo tức thời phản hồi tiêu cực của khách sạn | *Pain từ người khác* | Khách hàng để lại đánh giá rải rác trên Booking.com, Agoda, Google Maps. Các khiếu nại khẩn cấp (như phòng chưa dọn sạch, máy lạnh hỏng) thường bị phát hiện chậm trễ sau khi khách đã trả phòng và rời đi. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn Top 3 bài toán tiềm năng nhất từ danh sách trên để phân tích sơ bộ:

---

### 🎴 QUICK PROBLEM CARD #1: Xanh SM — Xử lý sự cố sạc pin thực địa & điều phối xe cứu hộ

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán: Hỗ trợ điều phối viên giải quyết sự cố pin xe taxi điện       │
│           Xanh SM trên đường (hết pin, trạm sạc đầy, pin nguy cấp).     │
│ Công ty thành viên: [x] Xanh SM (GSM)                                   │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Điều phối viên (Dispatcher): Quá tải vào giờ cao điểm (15-20 min/xe). │
│ - Tài xế Xanh SM: Đang chở khách hoặc vội nhận chuyến, stress vì hết pin.│
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Tài xế gọi điện thoại khẩn cấp về tổng đài điều vận báo dung lượng pin│
│   ──> 2. Điều phối viên tra cứu vị trí GPS thực địa của xe trên bản đồ   │
│   ──> 3. Mở dashboard trạm sạc VinFast, tìm trạm trống & đúng cổng sạc   │
│   ──> 4. Soạn thảo SMS / tin nhắn App gửi lộ trình cho tài xế           │
│   ──> 5. Nếu pin cạn kiệt (< 5%), gọi điện thoại điều xe sạc cứu hộ     │
│                                                                         │
│ Bước tốn thời gian & rủi ro nhất:                                       │
│   Bước 3 & Bước 4 (mất 10-12 phút tra cứu và soạn thảo tin nhắn).       │
│                                                                         │
│ AI có thể can thiệp vào bước nào?                                       │
│   Bước 3 & 4: Tự động tổng hợp dữ liệu GPS + API trạm sạc trống ->      │
│   Soạn thảo tin nhắn nháp (draft) hoặc kích hoạt gọi cứu hộ tức thì.    │
│                                                                         │
│ Đo lường thành công (Metric có số):                                     │
│   - Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút/lượt.         │
│   - Độ chính xác gợi ý trạm sạc trống phù hợp đạt >= 98%.                │
│   - 100% trường hợp pin < 5% được chặn gợi ý trạm xa, chuyển sang cứu hộ.│
│                                                                         │
│ Quick Architecture: [x] LLM Feature (Kết hợp Rule ranh giới an toàn)    │
└─────────────────────────────────────────────────────────────────────────┘


---

### 🎴 QUICK PROBLEM CARD #2: Vinhomes — Phân loại & định tuyến tự động phản ánh cư dân

┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán: Phân loại và tự động điều phối phản ánh/yêu cầu của cư dân    │
│           trên App Vinhomes Resident về đúng bộ phận xử lý.             │
│ Công ty thành viên: [x] Vinhomes                                        │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Nhân viên CSKH Trung tâm: Quá tải vì phải đọc hàng nghìn ticket/ngày. │
│ - Cư dân Vinhomes: Bực bội vì ticket chờ đợi phân loại mất 12-24 giờ.   │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Cư dân gửi form phản ánh kèm ảnh qua ứng dụng Vinhomes Resident   │
│   ──> 2. Nhân viên CSKH đọc nội dung, xác định tòa nhà & danh mục lỗi   │
│   ──> 3. CSKH gán nhãn thủ công (Kỹ thuật điện/Nước/An ninh/Vệ sinh)    │
│   ──> 4. Forward ticket cho Ban quản lý tòa nhà tương ứng xử lý         │
│                                                                         │
│ Bước tốn thời gian & lỗi nhất:                                          │
│   Bước 2 & 3 (phân loại nhầm ban ngành, mất 5-10 phút/ticket).          │
│                                                                         │
│ AI có thể can thiệp vào bước nào?                                       │
│   Bước 2 & 3: Phân tích nội dung phản ánh tự động gán tag phân loại và  │
│   định tuyến trực tiếp đến BQL tòa nhà phù hợp.                         │
│                                                                         │
│ Đo lường thành công (Metric có số):                                     │
│   - Rút ngắn thời gian chuyển tiếp ticket từ 12 giờ ──> dưới 5 phút.    │
│   - Độ chính xác phân loại danh mục đạt trên 95%.                       │
│                                                                         │
│ Quick Architecture: [x] LLM Feature / Classifier Router                 │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 🎴 QUICK PROBLEM CARD #3: Vinmec — Trợ lý soạn nháp bản tóm tắt xuất viện (Discharge Summary)

┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán: Trợ lý AI tự động trích xuất thông tin bệnh án để soạn thảo   │
│           bản tóm tắt hồ sơ xuất viện dễ hiểu cho bệnh nhân.            │
│ Công ty thành viên: [x] Vinmec                                          │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Bác sĩ điều trị: Quá tải hành chính, mất 25-30 phút gõ tóm tắt/ca.    │
│ - Bệnh nhân: Phải chờ đợi lâu khi làm thủ tục ra viện, nhận bản tóm tắt │
│   chứa nhiều thuật ngữ y khoa khó hiểu.                                 │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Bác sĩ mở bệnh án điện tử (EMR) kiểm tra toàn bộ lịch sử nằm viện │
│   ──> 2. Đọc kết quả cận lâm sàng, chẩn đoán hình ảnh, đơn thuốc        │
│   ──> 3. Tự soạn bản tóm tắt xuất viện bằng văn bản tiếng Việt          │
│   ──> 4. In ấn, ký xác nhận và giải thích cho bệnh nhân khi ra viện     │
│                                                                         │
│ Bước tốn thời gian nhất:                                                │
│   Bước 2 & 3 (tổng hợp dữ liệu rải rác và gõ văn bản: 20 phút).         │
│                                                                         │
│ AI có thể can thiệp vào bước nào?                                       │
│   Bước 2 & 3: Tự động trích xuất các chỉ số quan trọng, draft văn bản   │
│   tóm tắt chuẩn cấu trúc để bác sĩ chỉ việc kiểm tra và bấm duyệt.      │
│                                                                         │
│ Đo lường thành công (Metric có số):                                     │
│   - Giảm thời gian soạn thảo xuất viện từ 25 phút ──> dưới 5 phút.       │
│   - 100% hồ sơ bắt buộc có bác sĩ ký duyệt (Human-in-the-loop).         │
│                                                                         │
│ Quick Architecture: [x] LLM Feature (Strict Human-in-the-loop)          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

# 🎯 Quyết định lựa chọn bài toán cho Deep-Dive

Nhóm quyết định chọn **Bài toán #1: Xanh SM — Xử lý sự cố sạc pin thực địa & điều phối xe cứu hộ** để tiến hành phân tích sâu (Deep-Dive).

### Lý do lựa chọn:
1. **Tính cấp bách & Tác động kinh doanh tức thời:** Sự cố hết pin giữa đường ảnh hưởng trực tiếp đến an toàn giao thông, thời gian chờ đợi của hành khách và doanh thu vận hành của Xanh SM trong ngày.
2. **Quy trình chuẩn hóa:** Quy trình điều vận có đầu vào rõ ràng (vị trí GPS, phần trăm pin, tình trạng trạm sạc), phù hợp để áp dụng giải pháp LLM Feature kết hợp Rule-based.
3. **Độ an toàn & Ranh giới vận hành rõ rệt:** Bài toán có các ranh giới sống còn (pin `< 5%` bắt buộc gọi cứu hộ di động, tin nhắn gửi đi bắt buộc gắn thẻ `[DRAFT_ONLY]` để điều phối viên duyệt), giúp nhóm có thể xây dựng và kiểm thử bản mẫu mã nguồn (prompt prototype) một cách chặt chẽ.
