# 01 — Problem Scan: Vin Smart Future (Vingroup)

**Học viên:** Alan (Bùi Trọng Trinh)
**Vai trò:** AI Product Engineer tại Vin Smart Future

---

## Phase 1 — SCAN: Danh sách bài toán (4 Lenses)

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|----------------------|
| 1 | **Vinhomes** | Lặp lại | Phân loại thủ công phản ánh cư dân (mất điện, ồn, an ninh...) gửi qua App Vinhomes Resident và điều hướng tới đúng Ban quản lý tòa nhà. |
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên tra cứu thủ công trạm sạc trống + soạn tin hướng dẫn khi tài xế báo sự cố hết pin thực địa (15 phút/lượt). |
| 3 | **VinFast** | AI-upgrade | Khách hàng mô tả lỗi xe bằng tiếng Việt tự nhiên (VD: "xe kêu cụp cụp ở bánh trước"), cần phân loại mã lỗi kỹ thuật ban đầu trước khi chuyển kỹ thuật viên. |
| 4 | **Vinmec** | Pain từ người khác | Bác sĩ mất 20-30 phút/bệnh nhân để soạn tóm tắt hồ sơ xuất viện, gây quá tải và chậm trễ cho bệnh nhân chờ xuất viện. |
| 5 | **Vinpearl** | Pain từ người khác | Review khẩn cấp (phòng bẩn, thái độ nhân viên) trên Booking/Agoda/Google Map bị trộn lẫn với review thường, Manager không phát hiện kịp để xử lý. |

---

## Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### Card #1 — Vinhomes: Phân loại & Điều hướng phản ánh cư dân

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│ Bài toán: Phân loại phản ánh cư dân App Resident theo loại   │
│ sự cố + mức ưu tiên, điều hướng đúng Ban quản lý tòa nhà.    │
│ Công ty thành viên: [x] Vinhomes                             │
│                                                               │
│ Ai đang đau? Nhân viên trực tổng đài (quá tải), cư dân       │
│ (chờ phản hồi lâu), Ban quản lý (nhận sai loại việc)         │
│                                                               │
│ Workflow thủ công hiện tại (5 bước):                         │
│  1. Cư dân gửi phản ánh ─> 2. NV đọc & phân loại thủ công    │
│  ─> 3. Xác định mức ưu tiên ─> 4. Chuyển Ban QL tòa nhà      │
│  ─> 5. Soạn tin xác nhận gửi cư dân                          │
│                                                               │
│ Bước nào tốn nhất? Bước 2-3 (5 min sẽ+ 3 min = 8 phút/lượt)  │
│ AI có thể hỗ trợ ở bước nào? Bước 2, 3, 5                    │
│                                                               │
│ Đo thành công bằng gì? Giảm thời gian phân loại+route từ     │
│ 12 phút ──> dưới 1 phút; độ chính xác phân loại ≥ 90%.       │
│                                                               │
│ Quick Architecture: [x] LLM Feature                          │
└─────────────────────────────────────────────────────────────┘
```

### Card #2 — VinFast: Chẩn đoán sơ bộ lỗi xe từ mô tả tiếng Việt

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│ Bài toán: Khách mô tả triệu chứng xe bằng ngôn ngữ tự nhiên, │
│ hệ thống gợi ý mã lỗi/nhóm hệ thống nghi ngờ trước khi        │
│ chuyển kỹ thuật viên.                                        │
│ Công ty thành viên: [x] VinFast                              │
│                                                               │
│ Ai đang đau? Tổng đài CSKH (thiếu chuyên môn kỹ thuật),      │
│ kỹ thuật viên (nhận thông tin mơ hồ, mất thời gian hỏi lại)  │
│                                                               │
│ Workflow thủ công hiện tại: 1. Khách mô tả qua hotline/app   │
│ ─> 2. NV CSKH ghi chú lại ─> 3. Chuyển kỹ thuật viên          │
│ ─> 4. KTV gọi lại hỏi thêm chi tiết ─> 5. Xếp lịch xưởng      │
│                                                               │
│ Bước nào tốn nhất? Bước 2 & 4 (thông tin mơ hồ, hỏi lại nhiều)│
│ AI có thể hỗ trợ ở bước nào? Bước 2 (gợi ý mã lỗi + câu hỏi   │
│ làm rõ có cấu trúc)                                          │
│                                                               │
│ Đo thành công bằng gì? Giảm số lần gọi lại làm rõ từ 2 lần   │
│ ──> 0-1 lần; độ chính xác gợi ý nhóm lỗi ≥ 80%.               │
│                                                               │
│ Quick Architecture: [x] LLM Feature                          │
└─────────────────────────────────────────────────────────────┘
```

### Card #3 — Xanh SM: Xử lý sự cố sạc pin thực địa

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│ Bài toán: Tài xế báo hết pin/sự cố sạc giữa đường, cần        │
│ điều phối viên tìm trạm sạc phù hợp và hướng dẫn nhanh.       │
│ Công ty thành viên: [x] Xanh SM (GSM)                         │
│                                                               │
│ Ai đang đau? Tài xế (chờ đợi giữa đường), điều phối viên      │
│ (quá tải giờ cao điểm)                                       │
│                                                               │
│ Workflow thủ công hiện tại: 5 bước, tổng 15 phút/lượt        │
│ (xem chi tiết 02-deliverable-example.md)                     │
│                                                               │
│ Bước nào tốn nhất? Tra cứu trạm sạc + soạn tin (10 phút)     │
│ AI có thể hỗ trợ ở bước nào? Tra cứu vị trí/trạm sạc + draft  │
│                                                               │
│ Đo thành công bằng gì? Giảm 15 phút ──> dưới 3 phút.          │
│                                                               │
│ Quick Architecture: [x] LLM Feature                          │
└─────────────────────────────────────────────────────────────┘
```

---

## Quyết định lựa chọn để Deep-Dive

**Chọn: Card #1 — Vinhomes: Phân loại & Điều hướng phản ánh cư dân.**

**Lý do chọn:**
- Tần suất cao (~300 phản ánh/ngày/khu đô thị lớn) → tác động lớn nếu tối ưu.
- Bài toán phân loại + route là use-case LLM classification kinh điển, rủi ro kỹ thuật thấp, rất phù hợp để dựng prototype trong 30 phút của Phase 4.
- Có ranh giới an toàn rõ ràng để stress-test (case khẩn cấp an ninh/cháy nổ, tranh chấp phí quản lý).

**Lý do loại các thẻ khác:**
- **Card #2 (VinFast chẩn đoán lỗi xe):** Rủi ro an toàn cao hơn (gợi ý sai mã lỗi có thể ảnh hưởng quyết định sửa chữa xe điện); cần dữ liệu lịch sử lỗi thực tế mà nhóm chưa có, phù hợp làm "Not Yet" hơn.
- **Card #3 (Xanh SM sạc pin):** Đã được dùng làm worked example (02-deliverable-example.md), nhóm chọn bài toán khác để tránh trùng lặp.
