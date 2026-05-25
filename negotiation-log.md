# Biên bản đàm phán hợp đồng API

- Cặp đàm phán: pair-01-camera-ai-vision
- Product: Smart Campus
- Provider: AI Vision Service
- Consumer: Camera Stream Service
- Phiên: v1.0
- Ngày: 20/05/2026 (Cập nhật đối soát: 22/05/2026)

---

## Issue #1

- Raised by: Consumer
- Endpoint: POST /api/v1/vision/analyze
- Concern: Cách truyền dữ liệu hình ảnh
- Proposal:
  - Consumer: chỉ sử dụng image_url để gửi ảnh
  - Provider: đề xuất hỗ trợ multipart upload
- Resolution: Accepted (image_url)
- Rationale:
  - Camera Stream tối ưu băng thông và hiệu năng
  - image_url phù hợp với hệ thống streaming thực tế
- Impact:
  - Consumer phải đảm bảo URL hợp lệ và truy cập được
  - Provider cần fetch ảnh từ URL

---

## Issue #2

- Raised by: Consumer
- Endpoint: POST /api/v1/vision/analyze
- Concern: Thời gian phản hồi và kiến trúc xử lý
- Proposal:
  - Consumer: yêu cầu phản hồi nhanh realtime để giải phóng luồng camera
  - Provider: đề xuất đẩy tác vụ nặng vào hàng đợi chạy ngầm
- Resolution: Accepted (Asynchronous / Non-blocking)
- Rationale:
  - Camera cần giải phóng luồng ngay lập tức để tránh nghẽn mạch thiết bị biên
  - AI Vision cần thời gian tải ảnh về RAM và suy luận mô hình YOLOv8
- Impact:
  - Provider trả phản hồi tức thời HTTP 202 Accepted với nội dung nhận dạng dạng hàng đợi
  - Quá trình xử lý AI diễn ra ngầm; kết quả được Provider chủ động phát tán qua Webhook (không yêu cầu Consumer polling).

---

## Issue #3

- Raised by: Consumer
- Endpoint: POST /api/v1/vision/analyze
- Concern: Retry gây duplicate request
- Proposal:
  - Consumer: gửi correlationId trong Request Body
  - Provider: dùng để detect duplicate và truy vết log
- Resolution: Accepted
- Rationale:
  - Tránh xử lý trùng khi hệ thống mạng xảy ra retry
  - Đảm bảo tính Idempotency cho hệ thống
- Impact:
  - Provider phải lưu và kiểm tra correlationId trước khi đưa vào hàng đợi
  - Consumer phải generate unique id (UUID/String độc nhất) cho mỗi khung hình

---

## Issue #4

- Raised by: Provider
- Endpoint: POST /api/v1/vision/analyze
- Concern: Kích thước ảnh lớn ảnh hưởng performance
- Proposal:
  - Consumer: không giới hạn
  - Provider: giới hạn dung lượng file ảnh tối đa 5MB
- Resolution: Accepted (max 5MB)
- Rationale:
  - Bảo vệ hệ thống AI Vision khỏi nguy cơ overload và timeout trên môi trường CPU
- Impact:
  - Consumer phải đảm bảo ảnh truyền tải qua URL ≤ 5MB
  - Provider thực hiện validate dung lượng trước khi kích hoạt hàng đợi ngầm

---

## Issue #5

- Raised by: Provider
- Endpoint: All endpoints
- Concern: Bảo mật API
- Proposal:
  - Consumer: không cần auth vì chạy mạng nội bộ
  - Provider: yêu cầu Bearer token qua Gateway tòa nhà
- Resolution: Accepted (bắt buộc auth)
- Rationale:
  - Bảo vệ hệ thống khỏi các truy cập trái phép xuyên suốt các microservices
- Impact:
  - Consumer bắt buộc phải đính kèm cấu hình Authorization header với định dạng `Bearer [Token]` trong mọi request

---

## Issue #6

- Raised by: Consumer
- Endpoint: All endpoints
- Concern: Chuẩn hóa dữ liệu lỗi
- Proposal:
  - Consumer: cần format lỗi cố định, dễ parse tự động
  - Provider: dùng chuẩn Problem Details (RFC 7807 / RFC 9457)
- Resolution: Accepted
- Rationale:
  - Tuân thủ tiêu chuẩn công nghiệp (Industry Standard)
  - Dễ dàng xử lý fallback và debug lỗi hệ thống
- Impact:
  - Tất cả các phản hồi lỗi (400, 401, 500) trả về Content-Type: `application/problem+json`

---

## Issue #7

- Raised by: Consumer
- Endpoint: POST /api/v1/vision/analyze
- Concern: Thiếu thông tin ngữ cảnh phục vụ phân tích dữ liệu không gian và thời gian
- Proposal:
  - Consumer: bổ sung camera_id và timestamp vào gói tin gốc
  - Provider: đồng ý và đưa vào schema bắt buộc
- Resolution: Accepted
- Rationale:
  - Giúp hệ thống AI và cấu trúc dữ liệu Analytics sau này phân tích chính xác vị trí/thời điểm
- Impact:
  - Schema Request Body bổ sung hai trường bắt buộc: camera_id (String) và timestamp (ISO 8601 UTC)

---

# Chốt hợp đồng v1.0

Provider sign-off:  ___________________  
Consumer sign-off:  ___________________  
Witness (GV/TA):    ___________________  
Date:               22/05/2026  

---

## Ghi chú warning nếu Spectral còn cảnh báo

| Warning | Lý do chấp nhận tạm thời | Kế hoạch sửa |
|---|---|---|
| Không có warning | Hệ thống đã đồng bộ cấu hình OpenAPI và Hợp đồng | - |