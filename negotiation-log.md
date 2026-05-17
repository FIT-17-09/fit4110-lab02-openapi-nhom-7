# Biên bản đàm phán hợp đồng API

- Cặp đàm phán: pair-01-camera-ai-vision
- Product: Smart Campus
- Provider: AI Vision Service
- Consumer: Camera Stream Service
- Phiên: v1.0
- Ngày: <dd/mm/yyyy>

---

## Issue #1

- Raised by: Consumer
- Endpoint: POST /vision/detect
- Concern: Chưa rõ nên gửi ảnh dạng image_url hay multipart upload
- Proposal:
  - Consumer: dùng image_url
  - Provider: hỗ trợ cả multipart
- Resolution: Accepted (image_url)
- Rationale: image_url nhẹ hơn, dễ scale và giảm tải hệ thống
- Impact: giảm băng thông, dễ tích hợp

---

## Issue #2

- Raised by: Provider
- Endpoint: POST /vision/detect
- Concern: Trả kết quả đồng bộ hay bất đồng bộ
- Proposal:
  - Consumer: muốn nhận kết quả ngay
  - Provider: đề xuất trả detectionId khi hệ thống quá tải
- Resolution: Modified (hỗ trợ cả 2 bằng oneOf)
- Rationale: đảm bảo linh hoạt và mở rộng hệ thống
- Impact: cần thêm detectionId và endpoint GET

---

## Issue #3

- Raised by: Provider
- Endpoint: POST /vision/detect
- Concern: Kích thước ảnh có thể quá lớn gây chậm hệ thống
- Proposal:
  - Consumer: không giới hạn
  - Provider: giới hạn tối đa 5MB
- Resolution: Accepted (max 5MB)
- Rationale: đảm bảo performance và tránh overload
- Impact: cần validate request phía Consumer

---

## Issue #4

- Raised by: Provider
- Endpoint: All endpoints
- Concern: Bảo mật API
- Proposal:
  - Consumer: không bắt buộc auth
  - Provider: yêu cầu Bearer token
- Resolution: Accepted (bắt buộc auth)
- Rationale: đảm bảo an toàn hệ thống
- Impact: Consumer phải gửi Authorization header

---

## Issue #5

- Raised by: Consumer
- Endpoint: All endpoints
- Concern: Format lỗi không thống nhất
- Proposal:
  - Consumer: muốn dễ parse lỗi
  - Provider: dùng chuẩn Problem Details
- Resolution: Accepted (RFC 7807)
- Rationale: chuẩn industry, dễ mở rộng
- Impact: thêm schema Problem trong OpenAPI

---

## Issue #6

- Raised by: Consumer
- Endpoint: POST /vision/detect
- Concern: Thời gian phản hồi lâu ảnh hưởng realtime
- Proposal:
  - Consumer: < 1 giây
  - Provider: < 2 giây
- Resolution: Modified (< 1.5 giây)
- Rationale: cân bằng giữa hiệu năng và khả năng xử lý
- Impact: cần cơ chế retry và timeout

---

# Chốt hợp đồng v1.0

Provider sign-off:  ___________________  
Consumer sign-off:  ___________________  
Witness (GV/TA):    ___________________  
Date:               ___________________  

---

## Ghi chú warning nếu Spectral còn cảnh báo

| Warning | Lý do chấp nhận tạm thời | Kế hoạch sửa |
|---|---|---|
| Không có warning | - | - |