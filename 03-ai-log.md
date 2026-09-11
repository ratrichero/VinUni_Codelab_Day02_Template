# 03 — AI Log & Reflection

**Học viên:** Alan (Bùi Trọng Trinh)

---

## AI đã giúp gì?

- **Tăng tốc phần khung sườn:** Claude giúp tôi nhanh chóng chuyển từ worksheet/inspiration kit sang bản nháp đầy đủ 6-field Problem Statement, Current-State/Future-State flow, và checklist Evaluate — thay vì phải tự gõ lại cấu trúc từ đầu.
- **Đóng vai phản biện:** Khi để AI tự đề xuất Operational Boundary, tôi yêu cầu nó liệt kê các cách một cư dân "khó tính" có thể lách ranh giới (đòi hoàn tiền, đòi bỏ qua duyệt, giấu tình huống khẩn cấp trong câu nói dài dòng). Ba adversarial test case trong `prompt_prototype.py` đến từ gợi ý này — tự tôi có lẽ chỉ nghĩ ra 1-2 case đơn giản.
- **Chuẩn hóa JSON schema:** AI giúp thiết kế schema output rõ ràng (category/priority/route_to/draft_message/requires_human_review) mà tôi có thể kiểm tra bằng code, thay vì chỉ tin vào system prompt.

## AI trả lời sai / hallucination ở đâu?

- **Số liệu bị "làm tròn cho đẹp":** Ở bản nháp đầu, AI đề xuất con số như "giảm 90% thời gian xử lý" mà không có cơ sở — tôi phải yêu cầu tính lại dựa trên số phút thực tế trong workflow (12 phút → dưới 1 phút) thay vì chọn một con số % nghe ấn tượng.
- **Ranh giới ban đầu chưa đủ chặt về mặt code:** Bản nháp đầu chỉ dựa vào system prompt để cấm AI bỏ qua bước duyệt (`requires_human_review`). Tôi nhận ra prompt không phải là hàng rào tuyệt đối, nên đã yêu cầu thêm một lớp kiểm tra ở tầng code (`code_level_safety_check`) ép giá trị này luôn là `True` bất kể model trả về gì — không phụ thuộc hoàn toàn vào LLM tự giác tuân thủ.
- **Từ khóa khẩn cấp còn mơ hồ:** AI ban đầu chỉ dựa vào model để nhận diện tình huống khẩn cấp (cháy, an ninh). Tôi bổ sung một danh sách từ khóa cứng (`EMERGENCY_KEYWORDS`) kiểm tra song song bằng code, để không phụ thuộc 100% vào khả năng suy luận của LLM trong trường hợp câu văn cố tình đánh lạc hướng (Test 3).

## Tôi đã sửa gì?

1. Thay số liệu ước lượng bằng số liệu tính toán trực tiếp từ bảng thời gian từng bước trong workflow.
2. Thêm guardrail bằng code (không chỉ prompt) cho hai quy tắc quan trọng nhất: bắt buộc duyệt của con người, và escalate case khẩn cấp.
3. Viết thêm câu kiểm tra vi phạm ranh giới tự động trong `run_tests()` để không phải đọc thủ công từng output JSON xem có sai lệch hay không.

**Kết luận:** AI là công cụ brainstorm và tăng tốc rất tốt, nhưng với các ranh giới an toàn "không được vi phạm", tôi không thể chỉ tin vào lời hứa trong system prompt — cần kiểm chứng bằng code và bằng adversarial testing thực tế, đúng như tinh thần "Problem First, AI Second" của inspiration kit.
