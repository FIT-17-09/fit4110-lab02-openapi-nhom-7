# negotiation-log.md

# Negotiation Log — Camera Stream ↔ AI Vision

## Pair Information

- Provider Team: Nhóm 7 — Camera Stream Service
- Consumer Team: Nhóm 10 — AI Vision Service
- Contract Version: v1.0

---

## Issue 1

### Context
Field naming mismatch.

### Problem
Consumer dùng camId còn provider dùng camera_id.

### Decision
Use camera_id.

### Rationale
Unified snake_case convention.

---

## Issue 2

### Context
Image upload format.

### Problem
Multipart upload phức tạp khi test mock server.

### Decision
Use image_url JSON field.

### Rationale
Simpler than multipart upload.

---

## Issue 3

### Context
Detection response structure.

### Problem
Analytics cần tracking detection.

### Decision
Return detection_id and confidence.

### Rationale
Support analytics and logging.

---

## Issue 4

### Context
Timeout handling.

### Problem
AI detect có thể block camera service.

### Decision
Timeout after 3 seconds.

### Rationale
Avoid blocking camera service.

---

## Issue 5

### Context
Error response format.

### Problem
Hai nhóm dùng format lỗi khác nhau.

### Decision
Use Problem Details.

### Rationale
OpenAPI standard.

---

## Issue 6

### Context
Model information endpoint.

### Problem
Monitoring cần model metadata.

### Decision
Provide GET /vision/models/info.

### Rationale
Monitoring and debugging support.

---

## Final Sign-off

| Team | Role | Status |
|---|---|---|
| Nhóm 7 | Provider | Approved |
| Nhóm 10 | Consumer | Approved |