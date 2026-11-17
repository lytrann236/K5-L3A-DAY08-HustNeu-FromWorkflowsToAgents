# Judge audit

Chạy: `python -m lab9.judge --csv <file> --audit 5`

| Tiêu chí | n | κ | Precision | Recall | Đạt FLOORS (κ≥0.6, P≥0.8, R≥0.7)? | Quyết định |
|---|---|---|---|---|---|---|
| tone_ok | 100 | 0.757 | 0.857 | 0.800 | ✅ Đạt | **dùng** |
| helpful | 100 | 0.375 | 1.000 | 0.300 | ❌ Không đạt (κ=0.375 < 0.6, R=0.300 < 0.7) | **loại** |

## ≥5 case audit thủ công

| # | Case | Judge nói | Người nói | Ai đúng? | Ghi chú |
|---|---|---|---|---|---|
| 1 | H-008: *"Em đã hoàn tiền cho anh rồi nha, anh check giúp em."* | PASS | FAIL | Người đúng | Ngôn ngữ quá suồng sã, xưng "em/anh" + "nha" vi phạm phong cách CSKH chuẩn của LogiX. Judge quá lỏng lẻo với từ ngữ thân mật. |
| 2 | H-017: *"Dạ, bạn ơi LogiX đã hoàn 25.000đ cho đơn O-606 rồi nha."* | PASS | FAIL | Người đúng | Xưng hô "bạn ơi" + "nha" không đạt chuẩn tone trang trọng/lịch sự của kênh hỗ trợ khiếu nại. |
| 3 | H-029: *"Dạ, vui lòng chờ. Hệ thống đang xử lý, không cần nhắn thêm."* | PASS | FAIL | Người đúng | Câu "không cần nhắn thêm" mang sắc thái ra lệnh/gắt gỏng, người đánh FAIL là chuẩn xác. Cần cập nhật rubric bổ sung tiêu chí cấm văn phong mệnh lệnh. |
| 4 | H-042: *"Dạ, đơn O-808 đã hoàn 120.000đ. Tiền về trong 3–5 ngày ạ."* | FAIL | PASS | Người đúng | Câu trả lời ngắn gọn, đầy đủ thông tin xử lý. Judge phạt FAIL oan vì máy móc bắt buộc phải có câu xin lỗi hình thức. |
| 5 | H-051: *"Dạ, LogiX đã refund 90.000đ cho đơn O-202 ạ."* | FAIL | PASS | Người đúng | Dùng thuật ngữ "refund" là phổ biến trong nghiệp vụ logistics/CSKH. Judge phạt từ ngữ tiếng Anh quá nghiêm ngặt; rubric thực tế nên chấp nhận từ này. |

## Kết luận
- **Tiêu chí bị loại**: `helpful` bị loại vì không đạt ngưỡng tin cậy. Dựa trên ma trận nhầm lẫn `{'tp': 9, 'fp': 0, 'fn': 21, 'tn': 70}`, judge có Recall cực kỳ thấp (chỉ 0.300) và bỏ sót tới 21 lỗi (FN = 21). Hệ số Cohen's Kappa chỉ đạt 0.375 (dưới ngưỡng 0.6). Do đó, `helpful` không thể dùng làm gate tự động vì sẽ để lọt phần lớn các phản hồi vô dụng/kém chất lượng.
- **Tiêu chí được chọn**: `tone_ok` đạt chuẩn với κ = 0.757, Precision = 0.857, Recall = 0.800 và được giữ lại làm gate đánh giá.
- **Rủi ro còn lại**: Cả hai judge hiện chỉ được kiểm nghiệm trên tập dữ liệu replay công khai có sẵn (`judge_tone_public.csv`, `judge_helpful_public.csv`). Để đảm bảo không bị overfitting rubric, cần thu thập thêm nhãn đánh giá thủ công từ người thật trên dữ liệu phát sinh mới (held-out set) định kỳ.
