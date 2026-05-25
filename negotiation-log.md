# negotiation-log.md

# Negotiation Log — Camera Stream ↔ AI Vision

## Pair Information

- Cặp đàm phán: pair-01-camera-ai-vision
- Product: Smart Campus
- Provider Team: Nhóm 10 — AI Vision Service
- Consumer Team: Nhóm 7 — Camera Stream Service
- Contract Version: v1.0
- Ngày: 20/05/2026 (Cập nhật đối soát: 22/05/2026)

---

## Issue 1

### Context
Field naming mismatch.

### Problem
Consumer dùng camId còn provider dùng camera_id.

### Decision
Thống nhất camera_id (snake_case) và bắt buộc trong request.

### Rationale
Đồng bộ convention và tránh lệch schema giữa hai bên.

---

## Issue 2

### Context
Image upload format.

### Problem
Multipart upload phức tạp khi test mock server.

### Decision
Chỉ dùng image_url trong JSON payload.

### Rationale
Đơn giản hóa mock/test và phù hợp thực tế streaming.

---

## Issue 3

### Context
Kiến trúc xử lý và thời gian phản hồi.

### Problem
AI detect có thể block camera service.

### Decision
Dùng cơ chế asynchronous/non-blocking, trả 202 Accepted và xử lý ngầm.

### Rationale
Giải phóng luồng camera, giảm nghẽn thiết bị biên.

---

## Issue 4

### Context
Retry và duplicate request.

### Problem
Retry gây trùng xử lý và khó truy vết log.

### Decision
Bổ sung correlationId trong request để idempotency.

### Rationale
Giảm xử lý trùng, dễ trace log đa hệ.

---

## Issue 5

### Context
Giới hạn dung lượng ảnh.

### Problem
Ảnh lớn ảnh hưởng performance và dễ overload.

### Decision
Giới hạn dung lượng ảnh tối đa 5MB.

### Rationale
Bảo vệ hệ thống AI Vision và giảm timeout.

---

## Issue 6

### Context
Chuẩn hóa lỗi.

### Problem
Hai nhóm dùng format lỗi khác nhau.

### Decision
Dùng Problem Details với `application/problem+json` cho toàn bộ lỗi 4xx/5xx.

### Rationale
Chuẩn hóa parsing, tuân thủ RFC 7807/9457.

---

## Issue 7

### Context
Thông tin ngữ cảnh phục vụ phân tích.

### Problem
Thiếu camera_id và timestamp trong request.

### Decision
Bổ sung camera_id và timestamp bắt buộc trong AnalyzeRequest.

### Rationale
Hỗ trợ phân tích theo không gian và thời gian.

---

## Issue 8

### Context
Detection response structure.

### Problem
Analytics cần tracking detection.

### Decision
Return detection_id và confidence trong response detection.

### Rationale
Hỗ trợ analytics và logging.

---

## Issue 9

### Context
Model information endpoint.

### Problem
Monitoring cần model metadata.

### Decision
Provide GET /vision/models/info.

### Rationale
Phục vụ monitoring và debug.

---

## Final Sign-off

| Team | Role | Status |
|---|---|---|
| Nhóm 7 | Consumer | Approved |
| Nhóm 10 | Provider | Approved |

Provider sign-off:  ___________________  
Consumer sign-off:  ___________________  
Witness (GV/TA):    ___________________  
Date:               22/05/2026  

---

## Ghi chú warning nếu Spectral còn cảnh báo

| Warning | Lý do chấp nhận tạm thời | Kế hoạch sửa |
|---|---|---|
| Không có warning | Hệ thống đã đồng bộ cấu hình OpenAPI và Hợp đồng | - |
