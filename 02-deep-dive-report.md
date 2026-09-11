# 02 — Deep-Dive Report: Xanh SM Xử lý sự cố sạc pin
**Nhóm:** <Tên nhóm> | **Thành viên:** <Danh sách>

---

## 3.1. Current-State Workflow
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ Bước 1 │ │ Bước 2 │ │ Bước 3 │ │ Bước 4 │ │ Bước 5 │
│ Nhận call│──▶│ Tra GPS │──▶│ Tra trạm │──▶│ Soạn tin │──▶│ Gọi cứu │
│ │ │ │ │ sạc trống│ │ chỉ dẫn │ │ hộ │
│ 2 phút │ │ 2 phút │ │ 5 phút🔴 │ │ 5 phút🔴 │ │ 1 phút │
└──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘
## 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor** | Điều phối viên (Dispatcher) — Trung tâm Điều vận Xanh SM |
| **2. Current Workflow** | Tài xế gọi tổng đài → Dispatch tra GPS → Tra dashboard trạm sạc VinFast → Soạn SMS chỉ đường → Gọi cứu hộ nếu pin < 5%. 5 bước, thủ công, 15 phút/lượt. |
| **3. Bottleneck** | Bước 3 & 4 (10 phút): Tra cứu trụ sạc trống phù hợp dòng xe + soạn tin nhắn chỉ đường tiếng Việt. |
| **4. Business Impact** | ~80 sự cố/ngày tại Hà Nội. Lãng phí 20 giờ/ngày. Rò rỉ ~15% doanh thu do xe không đón khách. |
| **5. Success Metric** | (1) Giảm 15 phút → < 3 phút (Efficiency). (2) Tỉ lệ chỉ dẫn đúng địa điểm + đúng cổng sạc đạt 98% (Quality). |
| **6. Operational Boundary** | AI ĐƯỢC: truy xuất API GPS, API trạm sạc, draft tin. AI KHÔNG ĐƯỢC: tự gửi tin (bắt buộc HITL); đề xuất trạm > 5km khi pin < 5%. |

## 3.3. Future-State Flow & AI Fit

**AI Fit:** LLM Feature (không cần Agent tự trị vì rủi ro cao)
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ Bước 1 │ │ Bước 2 │ │ Bước 3 │ │ Bước 4 │
│ Nhận call│──▶│ 🔵 Auto │──▶│ 🔵 AI │──▶│ 🟢 HITL │
│ │ │ pull GPS │ │ draft SMS│ │ duyệt & │
│ │ │ + trạm │ │ │ │ gửi │
└──────────┘ └──────────┘ └──────────┘ └──────────┘
│🔵 = AI Step | 🟢 = Human Step | ↩️ = Fallback

## Phase 5 — EVALUATE

### AI Readiness Checklist
1. [x] Có dữ liệu mẫu/logs sạch để test (log cuộc gọi + GPS).
2. [x] Rủi ro trong tầm kiểm soát (HITL bắt buộc + Fallback thủ công).
3. [x] Stakeholders sẵn sàng đổi quy trình (dispatch team đồng thuận).

### Quyết định cuối cùng: **GO**

**Justification:**
- Bài toán cụ thể, metric rõ (15 phút → < 3 phút).
- AI Fit đơn giản (LLM Feature), không cần Agent phức tạp.
- Boundary được enforce qua SYSTEM_PROMPT (đã test với 5 adversarial cases).
- Rủi ro kiểm soát qua HITL + Fallback.
- ROI cao: tiết kiệm 20 giờ/ngày cho dispatch team.