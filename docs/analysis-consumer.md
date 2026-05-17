# Phân tích yêu cầu — vai Consumer

- Cặp đàm phán: pair-01-camera-ai-vision
- Product: Smart Campus
- Consumer service: Camera Stream Service
- Provider service: AI Vision Service
- Người viết: Nguyễn Hữu Hưng
- Ngày: 18/5/2026

---

## 1. Resource Consumer cần nhận/gửi

| Resource | Consumer dùng để làm gì? | Field bắt buộc với Consumer | Field có thể tùy chọn |
|---|---|---|---|
| DetectionRequest | Gửi dữ liệu hình ảnh để AI phân tích | image_url hoặc image_file, camera_id, timestamp | correlation_id |
| DetectionResult | Nhận kết quả phân tích từ AI Vision | detectionId, objects, confidence, riskLevel | processing_time |

---

## 2. API Consumer cần gọi

| Method | Path | Lúc nào gọi? | Kỳ vọng response |
|---|---|---|---|
| POST | `/vision/detect` | Khi phát hiện motion hoặc cần phân tích frame | Trả detectionId + objects + confidence + riskLevel |
| GET | `/vision/detections/{detectionId}` | Khi cần lấy lại kết quả (polling) | Trả chi tiết detection result |
| GET | `/vision/models/info` | Khi cần biết model AI đang sử dụng | Thông tin model, version |
| GET | `/health` | Kiểm tra service còn hoạt động không | Status OK |

---

## 3. Error case Consumer cần xử lý

Tối thiểu 5 case.

| Status | Consumer hiểu là gì? | Consumer sẽ xử lý thế nào? |
|---:|---|---|
| 400 | Request sai schema | Sửa payload/log lỗi |
| 401 | Thiếu token | Refresh/cấu hình token |
| 403 | Không đủ quyền | Báo lỗi quyền truy cập |
| 404 | Không tìm thấy detectionId | Hiển thị trạng thái không tồn tại |
| 409 | Trùng request hoặc conflict | Retry hoặc bỏ qua |
| 422 | Ảnh không hợp lệ (quá lớn/sai format) | Thông báo lỗi cụ thể |
| 500 | Lỗi hệ thống AI Vision | Retry sau |
| 504 | Timeout khi xử lý ảnh | Retry với backoff |

---

## 4. Giả định bổ sung

- AI Vision có thể xử lý ảnh trong thời gian < 500ms
- Camera Service có thể retry khi request thất bại
- DetectionResult có thể được trả ngay hoặc thông qua detectionId (polling)

---

## 5. Câu hỏi cho Provider

1. Nên gửi ảnh dạng image_url hay multipart upload?
2. Có trả kết quả đồng bộ ngay hay chỉ trả detectionId?
3. Giới hạn kích thước ảnh tối đa là bao nhiêu?
4. Có cần authentication (Bearer token) không?
5. Timeout tối đa của API là bao nhiêu?

---

## 6. Rủi ro tích hợp

| Rủi ro | Tác động | Đề xuất xử lý |
|---|---|---|
| Provider đổi kiểu dữ liệu | Consumer parse lỗi | Chốt type/format/pattern |
| Provider thiếu mã lỗi | Consumer khó xử lý lỗi | Chuẩn hóa Problem Details |
| Response trả chậm | Ảnh hưởng realtime | Dùng timeout + retry |
| Trùng request | Xử lý lặp | Dùng correlation_id |
| API thay đổi version | Gây lỗi hệ thống | Áp dụng versioning |