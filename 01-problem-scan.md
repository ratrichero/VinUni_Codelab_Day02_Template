# 01 — Problem Scan & Quick Cards (Vin Smart Future)

**Học viên:** Thuý — branch `thuytt_2960`

---

# 🔍 Phase 1 — SCAN

**4 Lenses:** Lặp lại · Tốn thời gian · AI có thể tốt hơn · Pain từ người khác

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Lặp lại | **Tìm đồ thất lạc trên xe:** Khách gọi tổng đài 1900 2088 báo quên đồ, phải mô tả đồ vật và nhớ lại thông tin chuyến (giờ đi, tài xế, lộ trình). Nhân viên tổng đài tra chuyến thủ công, gọi tài xế xác nhận rồi hẹn trả đồ. Việc này lặp lại hằng ngày trên toàn quốc. |
| 2 | **Vinhomes** | Tốn thời gian | **Chuyển phản ánh cư dân đến đúng bộ phận:** Cư dân gửi phản ánh qua app Vinhomes Resident (mất nước, hỏng đèn, thang máy, tiếng ồn…) dưới dạng chữ tự do. Ban quản lý phải đọc từng phản ánh rồi chuyển tay cho kỹ thuật / an ninh / vệ sinh. Trợ lý ảo hiện có (ra mắt 2022) chỉ tra cứu thông tin, chưa phân loại phản ánh. |
| 3 | **Vinmec** | Pain từ người khác | **Khách tự chọn sai chuyên khoa khi đặt lịch:** Đặt lịch online bắt buộc khách tự chọn chuyên khoa, nhưng nhiều người không biết triệu chứng của mình thuộc khoa nào. Nhân viên phải gọi lại xác nhận và đổi lịch; khách chọn sai thì đến nơi phải chuyển khoa, chờ thêm. |
| 4 | **VinFast** | Lặp lại | **Tổng hợp phản hồi sau sửa chữa:** VinFast gọi điện cho *mọi* khách trong 2 ngày sau khi xe rời xưởng để ghi nhận ý kiến, và phải có phương án cho khiếu nại trong ngày T+1. Nhân viên ghi chép, tóm tắt và phân loại từng cuộc gọi bằng tay nên dễ sót khiếu nại gấp. |
| 5 | **VinFast** | AI có thể tốt hơn | **Đoán lỗi xe từ mô tả của khách khi đặt lịch sửa:** Khách gõ mô tả tự do trên app (VD: *"đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"*). Cố vấn dịch vụ phải gọi hỏi lại nhiều lần mới đoán được lỗi, chọn sửa tại xưởng hay sửa lưu động (Mobile Service) và chuẩn bị phụ tùng. |
| 6 | **Vinpearl** | Pain từ người khác | **Theo dõi review khách trên nhiều nền tảng:** Vinpearl có hàng chục khách sạn/resort, mỗi nơi có hàng trăm review trên Booking, Tripadvisor, Google (VD: Meliá Vinpearl Tây Ninh có 614 review trên Booking). Quản lý đọc tay nên dễ bỏ sót phàn nàn nghiêm trọng như *"phòng bẩn"* hay *"nhân viên thái độ tệ"*. |

> 📎 **Nguồn tham khảo** (thông tin công khai, tra cứu ngày 11/09/2026):
> - #1: [Tổng đài Xanh SM — FPT Shop](https://fptshop.com.vn/tin-tuc/danh-gia/tong-dai-xanh-sm-182717), [Tổng đài Xanh SM — tìm đồ thất lạc](https://www.innhanh.pro.vn/tong-dai-xanh-sm.html)
> - #2: [Ra mắt trợ lý ảo trên Vinhomes Resident — Dân trí (07/2022)](https://dantri.com.vn/cong-nghe/ra-mat-tro-ly-ao-tren-ung-dung-vinhomes-resident-va-vinhomes-online-20220727223736536.htm), [App Vinhomes Resident](https://vinhomecitys.com/app-vinhomes-resident-ung-dung-tien-ich-cu-dan-vinhomes/)
> - #3: [Hướng dẫn đặt lịch khám tại Vinmec](https://www.vinmec.com/vie/bai-viet/huong-dan-dat-lich-kham-tai-vinmec-vi), [Đăng ký khám — Vinmec](https://www.vinmec.com/vie/dang-ky-kham/)
> - #4, #5: [Dịch vụ sửa chữa — VinFast](https://vinfastauto.com/vn_en/dich-vu-sua-chua), [Đặt lịch sửa chữa trên ứng dụng VinFast](https://vinfastauto.com/vn_vi/node/9354)
> - #6: [Review Meliá Vinpearl Tây Ninh — Booking.com](https://www.booking.com/reviews/vn/hotel/vinpearl-tay-ninh.vi.html), [Hệ thống Vinpearl toàn quốc — VIDI](https://vidi.vn/he-thong-vinpearl-toan-quoc/)

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn top 3 từ danh sách SCAN: **#1 (Xanh SM – Đồ thất lạc), #2 (Vinhomes – Phản ánh cư dân), #3 (Vinmec – Chọn sai chuyên khoa) ⭐**

> ⚠️ Các con số có dấu `*` là **ước tính** (chưa có số liệu công bố chính thức), cần khảo sát thực tế để xác lập baseline.

## Card #1 — Xanh SM: Tìm đồ thất lạc trên xe

```text
┌──────────────────────────────────────────────────────────────────────
│ QUICK PROBLEM CARD #1
│
│ Bài toán (1 câu): Khách quên đồ trên xe Xanh SM phải gọi tổng đài và
│ tự nhớ lại thông tin chuyến, để nhân viên tra chuyến và liên hệ tài xế
│ hoàn toàn thủ công.
│ Công ty thành viên: [x] Xanh SM
│
│ Ai đang đau (Actor)?
│   - Nhân viên tổng đài 1900 2088: xử lý tay từng ca
│   - Khách mất đồ: lo lắng, phải chờ, tốn cước gọi 1.000đ/phút
│   - Tài xế: bị gọi điện giữa lúc đang chạy xe
│
│ Workflow thủ công hiện tại (4 bước):
│   1. Khách gọi 1900 2088, mô tả đồ vật + giờ đi, điểm đón/trả
│   → 2. Nhân viên tra lịch sử chuyến để tìm đúng chuyến và tài xế
│   → 3. Nhân viên gọi tài xế hỏi có thấy đồ trên xe không
│   → 4. Gọi lại khách hẹn thời gian, địa điểm nhận lại đồ
│
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–3: khách nhớ mơ hồ nên tra
│ chuyến lâu; tài xế đang chạy khó liên lạc.        (⏱ ~10 phút/ca*)
│
│ AI có thể nhảy vào hỗ trợ ở bước nào?
│   - Bước 1–2: Khách báo mất đồ ngay trong app, gắn sẵn với chuyến vừa
│     đi → không cần tra chuyến nữa (phần này chỉ cần Rule).
│     (Cần kiểm tra app hiện đã có tính năng này chưa.)
│   - LLM đọc mô tả tự do của khách ("túi vải xám, để ở ghế sau") và soạn
│     sẵn tin nhắn nháp gửi tài xế; nhân viên duyệt rồi mới gửi.
│
│ Đo thành công bằng gì (Metric có số)?
│   Thời gian từ lúc khách báo mất đồ → tài xế nhận được thông báo:
│   từ ~15 phút* xuống dưới 2 phút.
│
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent
│   → Rule là chính (gắn chuyến, gửi thông báo), LLM chỉ là phụ.
│     Tự nhận xét: phần lớn bài toán này Rule làm tốt hơn AI.
└──────────────────────────────────────────────────────────────────────
```

## Card #2 — Vinhomes: Chuyển phản ánh cư dân đến đúng bộ phận

```text
┌──────────────────────────────────────────────────────────────────────
│ QUICK PROBLEM CARD #2
│
│ Bài toán (1 câu): Phản ánh của cư dân gửi qua app Vinhomes Resident
│ phải được ban quản lý đọc và chuyển tay đến đúng bộ phận, nên chậm và
│ dễ chuyển nhầm, nhất là buổi tối và cuối tuần.
│ Công ty thành viên: [x] Vinhomes
│
│ Ai đang đau (Actor)?
│   - Nhân viên lễ tân/CSKH ban quản lý: đọc và chuyển từng phản ánh
│   - Cư dân: chờ lâu, phải nhắn nhắc lại nhiều lần
│   - Tổ kỹ thuật: nhận việc không đúng chuyên môn, phải chuyển lại
│
│ Workflow thủ công hiện tại (4 bước):
│   1. Cư dân gửi phản ánh bằng chữ tự do (có thể kèm ảnh) trên app
│   → 2. Nhân viên BQL đọc, tự xác định loại sự cố và mức độ gấp
│   → 3. Chuyển phiếu cho bộ phận: điện nước / thang máy / an ninh /
│        vệ sinh
│   → 4. Theo dõi, cập nhật trạng thái và trả lời cư dân
│
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–3: đọc hiểu và chọn bộ phận;
│ chuyển nhầm thì phiếu bị đẩy qua lại giữa các tổ. (⏱ ~5 phút/phản ánh*)
│
│ AI có thể nhảy vào hỗ trợ ở bước nào?
│   - Bước 2–3: LLM đọc phản ánh → gợi ý loại sự cố, bộ phận phụ trách và
│     mức độ gấp; nhân viên chỉ cần bấm duyệt.
│   - Ca khẩn cấp (kẹt thang máy, chập điện, rò gas, ngập nước): Rule bắt
│     từ khóa → báo ngay an ninh/kỹ thuật trực, không chờ duyệt.
│
│ Đo thành công bằng gì (Metric có số)?
│   - ≥ 90% phản ánh được chuyển đúng bộ phận ngay lần đầu
│   - Thời gian từ lúc cư dân gửi → phiếu đến đúng tổ: từ ~2 giờ* xuống
│     dưới 10 phút
│
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent
│   → LLM phân loại phản ánh; Rule xử lý ca khẩn cấp.
└──────────────────────────────────────────────────────────────────────
```

## Card #3 — Vinmec: Khách tự chọn sai chuyên khoa khi đặt lịch ⭐

```text
┌──────────────────────────────────────────────────────────────────────
│ QUICK PROBLEM CARD #3   ⭐ (đề xuất cho Deep-Dive nhóm)
│
│ Bài toán (1 câu): Khi đặt lịch khám online, khách phải tự chọn chuyên
│ khoa nhưng nhiều người không biết triệu chứng của mình thuộc khoa nào,
│ dẫn đến đặt sai khoa, nhân viên phải gọi lại sửa và khách phải chờ thêm.
│ Công ty thành viên: [x] Vinmec
│
│ Ai đang đau (Actor)?
│   - Nhân viên CSKH/tổng đài đặt lịch: gọi lại xác nhận, đổi lịch
│   - Khách hàng/bệnh nhân: đến sai khoa, phải chuyển khoa, chờ lâu
│   - Bác sĩ/điều dưỡng tiếp đón: khám xong mới phát hiện sai khoa
│
│ Workflow thủ công hiện tại (4 bước):
│   1. Khách đặt lịch trên web/app MyVinmec: chọn cơ sở → chuyên khoa
│      → giờ khám
│   → 2. Nhân viên CSKH xem từng lịch mới, gọi lại khách để xác nhận
│   → 3. Nếu nghi chọn sai khoa: hỏi thêm triệu chứng, tự đoán khoa
│        đúng, đổi lịch
│   → 4. Ngày khám: nếu vẫn sai khoa → bác sĩ chuyển khoa, khách đăng
│        ký lại và chờ thêm
│
│ Bước nào tốn thời gian/lỗi nhất? Bước 3: nhân viên không phải bác sĩ
│ nhưng phải hỏi triệu chứng và đoán khoa.        (⏱ ~5–7 phút/lịch*)
│ Nếu lọt đến bước 4, khách mất thêm ~30–60 phút* chờ khám lại.
│
│ AI có thể nhảy vào hỗ trợ ở bước nào?
│   - Ngay bước 1: khách gõ triệu chứng bằng lời thường ("ho 2 tuần,
│     sốt về chiều") → LLM gợi ý 1–2 chuyên khoa phù hợp kèm lý do ngắn,
│     khách xác nhận rồi mới đặt.
│   - Rule: danh sách triệu chứng nguy hiểm (đau ngực, khó thở, yếu liệt
│     nửa người, co giật…) → KHÔNG đặt lịch thường, hiện ngay cảnh báo
│     gọi 115 / đến khoa Cấp cứu.
│   - Ca AI không chắc → chuyển nhân viên CSKH gọi lại như cũ.
│
│ Ranh giới (vì là y tế): AI chỉ GỢI Ý CHUYÊN KHOA. Tuyệt đối không
│ chẩn đoán bệnh, không kê thuốc, không khuyên "không cần đi khám".
│
│ Đo thành công bằng gì (Metric có số)?
│   - Tỉ lệ lịch đặt sai chuyên khoa: từ ~15%* xuống dưới 5%
│   - Số cuộc gọi lại để đổi khoa giảm ≥ 50%
│   - 100% ca có triệu chứng nguy hiểm được cảnh báo cấp cứu (không sót)
│
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent
│   → LLM gợi ý chuyên khoa; Rule bắt triệu chứng nguy hiểm.
└──────────────────────────────────────────────────────────────────────
```

---

## 💡 Đề xuất cá nhân cho buổi họp nhóm

**Đề xuất chọn Card #3 (Vinmec) để Deep-Dive**, vì:
* **Đúng thế mạnh của LLM:** hiểu triệu chứng khách mô tả bằng lời thường là việc ngôn ngữ, Rule khó làm được.
* **Ranh giới an toàn rõ ràng và quan trọng:** có đủ chất liệu cho Operational Boundary, Human-in-the-loop và Fallback.
* **Đo được:** tỉ lệ đặt sai khoa và số cuộc gọi đổi lịch là số liệu bệnh viện có sẵn.
* **Rủi ro cần nói thật:** y tế là mảng nhạy cảm. Bảng "triệu chứng → chuyên khoa" phải được bác sĩ duyệt, và nếu chưa có dữ liệu lịch sử đặt sai khoa thì kết quả có thể là **NOT YET**.

**Lý do không ưu tiên các thẻ còn lại:**
* **Card #1 (Xanh SM):** phần lớn giải quyết được bằng Rule (gắn báo mất đồ vào chuyến trong app), AI chỉ là phụ. Đây là ví dụ tốt cho kết luận "Rule-based tốt hơn".
* **Card #2 (Vinhomes):** khả thi và rủi ro thấp, giữ làm **phương án dự phòng**. Nhưng Vinhomes đã có trợ lý ảo ViVi, cần tìm hiểu thêm xem hệ thống hiện tại đã phân loại phản ánh chưa.
