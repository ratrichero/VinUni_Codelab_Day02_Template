# Lab 02 — Deep-Dive Report

## Xanh SM: Dispatcher Co-pilot cho sự cố pin yếu ngoài thực địa

**Phạm vi quyết định:** Prototype hỗ trợ điều phối viên, không phải hệ thống tự trị.  
**Trạng thái số liệu:** Baseline và volume trong báo cáo là giả định có ghi nhãn, cần kiểm chứng bằng log thật.

---

## 1. Executive Summary

Khi tài xế Xanh SM báo xe sắp hết pin, điều phối viên phải thu thập thông tin, tra vị trí, xác minh trạm sạc và soạn hướng dẫn. Quy trình thủ công được giả định mất khoảng 15 phút/lượt, trong đó 10 phút nằm ở bước tìm trạm và viết hướng dẫn.

Giải pháp đề xuất là mô hình **hybrid Rule + LLM Feature**:

- Rule/state machine kiểm tra mức pin, khoảng cách, loại cổng sạc và dữ liệu bắt buộc.
- LLM chỉ tóm tắt tình huống và tạo nội dung hướng dẫn dạng `[DRAFT_ONLY]`.
- Điều phối viên bắt buộc kiểm tra và duyệt trước khi gửi.
- Khi pin dưới 5%, hệ thống không hướng dẫn xe tiếp tục di chuyển tới trạm xa; thay vào đó đề xuất `dispatch_mobile_charger`.

---

## 2. Assumptions và câu hỏi cần xác minh

| Giả định làm việc | Giá trị dùng trong lab | Cách xác minh trước pilot |
|---|---:|---|
| Số sự cố pin cần điều phối | 30 lượt/ngày | Truy vấn log tổng đài và hệ thống điều vận trong 4 tuần |
| Thời gian xử lý hiện tại | 15 phút/lượt | Time study tối thiểu 100 sự cố |
| Thời gian bước tra cứu + soạn tin | 10 phút/lượt | Timestamp theo từng bước trên dashboard |
| Ngưỡng pin nguy cấp | Dưới 5% | Xác nhận với đội an toàn/vận hành và tài liệu từng dòng xe |
| Bán kính an toàn | Không đề xuất trạm xa hơn 5 km khi pin nguy cấp | Xác nhận bằng chính sách vận hành và thử nghiệm thực địa |

Các giá trị này phục vụ scoping, **không được coi là dữ liệu vận hành thật** cho đến khi được chủ quy trình xác nhận.

---

## 3. Current-State Workflow Mapping

| Bước | Actor / Hệ thống | Input | Hoạt động | Output | Thời gian giả định | Điểm chú ý |
|---:|---|---|---|---|---:|---|
| 1 | Tài xế | Cảnh báo pin/tình trạng xe | Gọi hoặc gửi yêu cầu hỗ trợ | Yêu cầu sự cố | 1 phút | Bắt đầu quy trình |
| 2 | Điều phối viên | Yêu cầu từ tài xế | Xác nhận biển số, mức pin, loại xe, tình trạng an toàn | Hồ sơ sự cố sơ bộ | 2 phút | 🔄 Handoff: tài xế → điều phối |
| 3 | Điều phối viên + bản đồ | Biển số/vị trí | Tra cứu GPS hiện tại | Tọa độ đã xác nhận | 2 phút | Có thể thiếu/mất tín hiệu |
| 4 | Điều phối viên + dashboard trạm | GPS, loại xe, cổng sạc | Tìm trạm phù hợp và kiểm tra khoảng cách/tình trạng | Danh sách trạm ứng viên | 5 phút | 🔴 Bottleneck; 🔄 handoff giữa hai hệ thống |
| 5 | Điều phối viên | Dữ liệu xe và trạm | Soạn hướng dẫn cho tài xế | Tin nhắn nháp | 5 phút | 🔴 Bottleneck; dễ thiếu dữ kiện |
| 6 | Điều phối viên | Tin nhắn nháp | Kiểm tra rồi gửi, hoặc gọi đội cứu hộ | Hướng dẫn/lệnh hỗ trợ | 1 phút | Quyết định cuối của con người |

**Tổng thời gian baseline giả định:** 15 phút/lượt.

Sơ đồ trực quan được lưu tại `04-workflow-diagram.png`.

---

## 4. Problem Statement — 6 fields

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Điều phối viên tại trung tâm vận hành Xanh SM. Tài xế là người nhận hướng dẫn và chịu rủi ro nếu chỉ dẫn sai. |
| **2. Current Workflow** | Điều phối viên nhận báo cáo, xác nhận thông tin xe, tra GPS, mở dashboard trạm sạc, chọn trạm tương thích, viết hướng dẫn và gửi sau khi tự kiểm tra. Khi không an toàn, họ liên hệ đội hỗ trợ/cứu hộ. |
| **3. Bottleneck** | Tra cứu trạm phù hợp và soạn tin ở bước 4–5, giả định chiếm 10/15 phút. Việc chuyển qua nhiều màn hình làm tăng thời gian và nguy cơ dùng dữ liệu cũ hoặc thiếu thông tin. |
| **4. Business Impact** | Với giả định 30 sự cố/ngày, quy trình hiện tại tiêu tốn khoảng 7,5 giờ công/ngày. Nếu đạt mục tiêu 3 phút/lượt, có thể tiết kiệm tối đa khoảng 6 giờ công/ngày, đồng thời giảm thời gian xe không thể nhận chuyến. Đây là ước tính cần kiểm chứng. |
| **5. Success Metric** | (a) Median handling time < **3 phút**; (b) ≥ **95%** đề xuất đúng loại cổng và trạm khả dụng trong bộ dữ liệu kiểm thử; (c) **100%** nội dung có `[DRAFT_ONLY]` và được người duyệt; (d) **0** trường hợp đề xuất trạm >5 km khi pin <5%; (e) tỷ lệ fallback/manual review ≤ **15%** sau giai đoạn hiệu chỉnh. |
| **6. Operational Boundary** | AI được đọc dữ liệu tối thiểu cần thiết và tạo bản nháp. AI không được tự gửi tin, không tự điều xe, không tự thay đổi chuyến, không bịa trạng thái trạm, không bỏ qua kiểm tra tương thích. Pin <5% phải trả hành động `dispatch_mobile_charger`. Dữ liệu thiếu/mâu thuẫn phải chuyển `manual_review`. |

### Problem statement một câu

> Điều phối viên Xanh SM cần một co-pilot tạo hướng dẫn nháp từ dữ liệu xe và trạm đã được kiểm tra, nhằm giảm median handling time từ baseline giả định 15 phút xuống dưới 3 phút, trong khi mọi hành động vẫn cần con người duyệt và các trường hợp pin dưới 5% được chuyển sang hỗ trợ sạc di động.

---

## 5. Scope

### In scope

- Nhận input dạng text/JSON chứa mức pin, vị trí, loại xe và trạm ứng viên.
- Kiểm tra dữ liệu bắt buộc, ngưỡng pin và khoảng cách bằng rule.
- Tạo tóm tắt sự cố và nội dung hướng dẫn dạng nháp.
- Giải thích ngắn gọn lý do fallback hoặc gọi sạc di động.
- Ghi log input, output, phiên bản prompt và quyết định của người duyệt.

### Out of scope

- Tự gửi tin cho tài xế hoặc tự gọi cứu hộ.
- Tự thay đổi lịch/chuyến đang chạy.
- Tự suy đoán trạng thái trạm khi API không trả dữ liệu.
- Ra quyết định an toàn thay điều phối viên.
- Dùng dữ liệu cá nhân ngoài mục đích xử lý sự cố.

---

## 6. AI-Fit Analysis

| Phương án | Phù hợp với | Điểm mạnh | Hạn chế | Quyết định |
|---|---|---|---|---|
| **No AI / quy trình hiện tại** | Volume thấp, ít thay đổi | Dễ kiểm soát | Chậm và phụ thuộc thao tác thủ công | Không chọn làm trạng thái tương lai |
| **Rule / State Machine** | Ngưỡng pin, khoảng cách, cổng sạc, dữ liệu thiếu | Xác định, dễ test và audit | Không viết hướng dẫn tự nhiên tốt; khó xử lý mô tả tự do | **Bắt buộc dùng cho safety gate** |
| **LLM Feature** | Tóm tắt và soạn bản nháp tiếng Việt | Linh hoạt với ngôn ngữ; giảm thời gian soạn | Có thể bịa hoặc làm sai chỉ dẫn | **Chọn sau safety gate, có HITL** |
| **Agentic Loop** | Chuỗi hành động tự trị qua nhiều hệ thống | Có thể tự động hóa sâu | Rủi ro quá cao, khó audit, vượt nhu cầu | Không chọn trong scope này |

**Kết luận:** Không dùng LLM để quyết định điều xe. Kiến trúc phù hợp là **Rule + LLM Feature + Human-in-the-loop**.

---

## 7. Future-State Flow

1. Tài xế gửi báo cáo sự cố.
2. Hệ thống tự lấy mức pin, GPS, loại xe và danh sách trạm tương thích.
3. **Rule safety gate** kiểm tra dữ liệu:
   - Thiếu/mâu thuẫn → `manual_review`.
   - Pin <5% → `dispatch_mobile_charger`.
   - Trạm không tương thích/không xác nhận được → `manual_review`.
4. Nếu dữ liệu hợp lệ, **LLM** tạo tóm tắt và hướng dẫn `[DRAFT_ONLY]`.
5. **Điều phối viên** so sánh với dữ liệu nguồn, sửa nếu cần và nhấn duyệt.
6. Hệ thống chỉ gửi sau hành động duyệt của điều phối viên.
7. Toàn bộ quyết định và chỉnh sửa được ghi log để đánh giá.

### Human-in-the-loop

- Mọi output đều có `requires_human_approval: true`.
- Nút gửi bị khóa cho tới khi điều phối viên xác nhận.
- Giao diện phải hiển thị mức pin, nguồn GPS, timestamp trạm và lý do đề xuất ngay cạnh bản nháp.

### Fallback

| Trigger | Hành vi an toàn |
|---|---|
| Thiếu API/trạm/GPS hoặc dữ liệu quá cũ | Không tạo chỉ dẫn; trả `manual_review` |
| Pin dưới 5% | Trả `dispatch_mobile_charger`; không đề xuất tiếp tục chạy |
| Không xác minh được cổng sạc | Chuyển người kiểm tra; không đoán |
| LLM timeout hoặc output sai schema | Bỏ output và quay về quy trình thủ công |
| Điều phối viên từ chối bản nháp | Ghi lý do, xử lý thủ công và đưa mẫu vào tập đánh giá |

---

## 8. Dữ liệu và kiến trúc tối thiểu

### Input bắt buộc

- Mã sự cố và timestamp.
- Mức pin hiện tại.
- GPS và độ mới của dữ liệu.
- Dòng xe/loại cổng sạc.
- Danh sách trạm ứng viên đã lọc, khoảng cách và trạng thái cập nhật.

### Output dự kiến

```json
{
  "status": "draft_only",
  "action": "draft_guidance",
  "summary": "Tóm tắt tình huống",
  "message": "Nội dung hướng dẫn nháp",
  "reason": "Lý do đề xuất",
  "requires_human_approval": true
}
```

### Kiến trúc logic

```text
Operational APIs → Validation/Rules → LLM Draft → Human Review → Send Gateway
                              ↘ Manual Review / Mobile Charger
```

LLM không có quyền truy cập trực tiếp vào Send Gateway.

---

## 9. Rủi ro và biện pháp kiểm soát

| Rủi ro | Mức độ | Kiểm soát |
|---|---|---|
| Gợi ý trạm không tương thích hoặc đã hết chỗ | Cao | Chỉ truyền trạm đã được API/rule xác minh; hiển thị timestamp; HITL |
| Xe cạn pin trên đường tới trạm | Rất cao | Rule pin <5%; giới hạn khoảng cách; ưu tiên mobile charger |
| LLM bịa địa chỉ hoặc khoảng cách | Cao | Không cho LLM tự sinh dữ kiện; đối chiếu ID trạm; schema validation |
| Tự động gửi tin khi chưa duyệt | Cao | `[DRAFT_ONLY]`, `requires_human_approval`, tách quyền Send Gateway |
| Lộ dữ liệu vị trí/tài xế | Cao | Tối thiểu hóa dữ liệu, RBAC, encryption, retention và audit log |
| Prompt injection từ nội dung người dùng | Trung bình–cao | System prompt, input separation, deterministic guard và adversarial tests |

---

## 10. Evaluation Plan

### Offline evaluation

- Thu thập tối thiểu 100–200 sự cố đã ẩn danh và được chuyên gia gắn nhãn.
- Kiểm tra độ chính xác trạm/cổng, tuân thủ boundary, schema và hallucination.
- Chạy tập adversarial: yêu cầu bỏ draft, ép tự gửi, pin nguy cấp, dữ liệu thiếu và prompt injection.

### Shadow mode

- Chạy song song 1–2 tuần nhưng không cho hệ thống gửi tin.
- So sánh đề xuất AI với quyết định thật của điều phối viên.
- Đo median/p95 latency, tỷ lệ chấp nhận, tỷ lệ sửa lớn và lý do từ chối.

### Pilot có kiểm soát

- Mở cho nhóm điều phối nhỏ trong một khu vực.
- Có kill switch, monitoring và rollback về quy trình cũ.
- Dừng pilot ngay khi xảy ra vi phạm boundary nghiêm trọng.

---

## 11. AI Readiness Checklist và quyết định

| Tiêu chí | Trạng thái | Bằng chứng / khoảng trống |
|---|---|---|
| Có dữ liệu mẫu/log sạch để test | ☐ Chưa xác nhận | Cần log đã ẩn danh và nhãn quyết định đúng |
| Rủi ro AI sai kiểm soát được | ☑ Có ở scope prototype | Rule, HITL, fallback và tách quyền gửi |
| Stakeholder sẵn sàng thay đổi workflow | ☐ Chưa xác nhận | Cần phỏng vấn điều phối viên và owner của Send Gateway |

### Quyết định: **NOT YET cho production pilot; GO cho sandbox prototype**

Nhóm có thể tiếp tục prototype prompt trong môi trường sandbox vì scope nhỏ và không cho AI thực hiện hành động. Tuy nhiên, chưa nên pilot trên vận hành thật cho tới khi có baseline, dữ liệu đã gắn nhãn, xác nhận ngưỡng an toàn và phê duyệt từ chủ quy trình. Khi bốn điều kiện này hoàn tất, dự án có thể chuyển sang shadow mode trước khi cân nhắc production.

