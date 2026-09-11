# 02 — Deep-Dive Report: Vinhomes — Phân loại & Điều hướng Phản ánh Cư dân

**Học viên:** Alan (Bùi Trọng Trinh) — AI Product Engineer, Vin Smart Future

---

## 3.1. Current-State Workflow

Xem sơ đồ trực quan: [04-workflow-diagram.png](04-workflow-diagram.png)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │     │ Bước 5       │
│ Nhận phản ánh│     │ Đọc & phân   │     │ Xác định mức │     │ Chuyển tới   │     │ Soạn tin xác │
│ từ App       │ ──→ │ loại thủ công│ ──→ │ ưu tiên      │ ──→ │ Ban QL tòa   │ ──→ │ nhận gửi cư  │
│ Resident     │     │ 🔴           │     │ 🔴           │     │ nhà          │     │ dân          │
│ Ai: Cư dân   │     │ Ai: NV trực  │     │ Ai: NV trực  │     │ Ai: NV trực  │     │ Ai: NV trực  │
│ ⏱ 0 phút     │     │ ⏱ 5 phút     │     │ ⏱ 3 phút     │     │ ⏱ 2 phút     │     │ ⏱ 2 phút     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
🔴 = Bottleneck | ⏱ Tổng thời gian xử lý thủ công: 12 phút/lượt
```

**Handoff:** Bước 4 là điểm chuyển giao thủ công từ Tổng đài sang Ban quản lý từng tòa nhà (qua email/nhóm chat nội bộ) — dễ thất lạc thông tin nếu route sai loại việc.

---

## 3.2. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên trực tổng đài/App Vinhomes Resident, xử lý phản ánh của cư dân tại các khu đô thị lớn. |
| **2. Current Workflow** | Cư dân gửi phản ánh qua App → NV đọc và phân loại thủ công theo loại sự cố (điện, nước, an ninh, tiếng ồn, phí dịch vụ...) → xác định mức ưu tiên → chuyển tới đúng Ban quản lý tòa nhà qua kênh nội bộ → soạn tin xác nhận đã tiếp nhận gửi lại cư dân. 5 bước thủ công, ~12 phút/lượt, ~300 phản ánh/ngày/khu đô thị lớn. |
| **3. Bottleneck** | Bước 2 & 3 (8/12 phút): phân loại đúng danh mục sự cố và xác định đúng mức ưu tiên đòi hỏi đọc hiểu ngữ cảnh tiếng Việt tự do (không theo mẫu cố định), dễ nhầm lẫn khi khối lượng phản ánh tăng đột biến. |
| **4. Business Impact** | ~300 phản ánh/ngày/khu đô thị lớn → ~60 giờ công/ngày cho việc phân loại + route. Vào giờ cao điểm (cuối tuần, sau mưa bão), tồn đọng khiến phản hồi cư dân bị chậm tới 12 giờ, làm tăng khiếu nại lên Ban quản lý cấp cao và ảnh hưởng đánh giá dịch vụ Vinhomes. |
| **5. Success Metric** | 1. Giảm thời gian phân loại + route từ 8 phút xuống dưới 1 phút/case (Efficiency).<br>2. Độ chính xác phân loại danh mục sự cố đạt ≥ 90% so với nhãn do con người gán (Quality).<br>3. 100% case chứa từ khóa khẩn cấp (cháy, an ninh, y tế) được escalate trong vòng dưới 5 phút (Safety). |
| **6. Operational Boundary** | AI được phép: đọc nội dung phản ánh, gán danh mục + mức ưu tiên, route tới đúng Ban quản lý, soạn **draft** tin xác nhận đã tiếp nhận. **CẤM:** AI không được tự động đóng case; không được tự cam kết bồi thường, hoàn phí, hoặc cam kết thời hạn sửa chữa cụ thể; không được tự động phản hồi các case liên quan tranh chấp phí quản lý/pháp lý mà chưa qua duyệt của người phụ trách. Case chứa dấu hiệu khẩn cấp (cháy, an ninh, y tế) phải escalate ngay lập tức cho người trực, không chờ phê duyệt draft. |

---

## 3.3. Future-State Flow & AI Fit

**AI Fit:** `LLM Feature` (phân loại + route có cấu trúc, không cần vòng lặp tự trị đa bước; rủi ro sai lệch được kiểm soát qua HITL + rule-based escalation cứng cho case khẩn cấp).

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ 🔵 Bước 2    │     │ 🟢 Bước 3    │     │ Bước 4       │
│ Nhận phản ánh│ ──→ │ AI phân loại │ ──→ │ NV duyệt draft│ ──→ │ Hệ thống gửi │
│ từ App       │     │ danh mục +   │     │ (route +     │     │ tin xác nhận │
│              │     │ ưu tiên +    │     │ nội dung tin)│     │ + route      │
│              │     │ draft tin    │     │              │     │              │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
       │
       └──▶ Nếu phát hiện từ khóa khẩn cấp (cháy/an ninh/y tế):
             ⚡ Escalate ngay cho người trực + bảo vệ, bỏ qua bước duyệt draft.

↩️ Fallback: Nếu AI không tự tin (confidence thấp / nội dung mơ hồ) hoặc lỗi
   format JSON → route về hàng đợi "Cần NV xử lý thủ công" như quy trình cũ.
```

- 🔵 **AI Step:** Bước 2 — phân loại + soạn draft.
- 🟢 **Human Step (HITL):** Bước 3 — NV duyệt trước khi gửi (bắt buộc với mọi case không khẩn cấp).
- ↩️ **Fallback:** Quay lại quy trình thủ công khi AI không chắc chắn hoặc lỗi kỹ thuật.

---

## Phase 5 — EVALUATE

### AI Readiness Checklist
- [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? — Có, dùng lịch sử phản ánh đã phân loại thủ công làm tập test.
- [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? — Có, mọi phản hồi ra ngoài đều qua duyệt của NV; case khẩn cấp có rule cứng riêng.
- [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? — Cần thêm buổi làm việc với Ban quản lý các tòa nhà để thống nhất luồng route mới.

### Quyết định cuối cùng
**[x] GO (Bắt đầu xây dựng Prototype) — scope hẹp: chỉ 1 khu đô thị thí điểm, giữ HITL bắt buộc.**

**Justification:**
> Bài toán có metric rõ ràng (thời gian xử lý, độ chính xác phân loại), khối lượng đủ lớn để tạo giá trị (300 case/ngày), và kiến trúc LLM Feature đơn giản (không cần Agent) giúp giảm rủi ro. Ranh giới an toàn (cấm tự đóng case, cấm cam kết bồi thường, escalate cứng cho case khẩn cấp) có thể lập trình và kiểm chứng được ngay ở giai đoạn prototype — thể hiện qua kết quả stress-test ở Phase 4. Điểm chưa sẵn sàng duy nhất là sự đồng thuận vận hành với các Ban quản lý tòa nhà, không phải rào cản kỹ thuật, nên khuyến nghị GO với scope thí điểm hẹp trước khi nhân rộng.
