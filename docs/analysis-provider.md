# docs/analysis-provider.md

# Provider Analysis — Camera Stream Service

## Overview

Camera Stream Service chịu trách nhiệm tiếp nhận và xử lý luồng camera IP trong hệ thống Smart Campus.

## Responsibilities

- Kết nối camera IP
- Kiểm tra trạng thái camera
- Lấy frame camera
- Chuyển frame sang AI Vision service
- Quản lý metadata camera

## APIs Provided

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /health | Health check |
| POST | /camera/connect | Connect camera |
| GET | /camera/status/{cameraId} | Camera status |
| GET | /camera/frame/{cameraId} | Current frame |
| POST | /vision/detect | Detect object |

## Validation Rules

- camera_id phải unique
- rtsp_url đúng định dạng
- timeout tối đa 3 giây

## Error Handling

| Code | Meaning |
|---|---|
| 400 | Invalid request |
| 404 | Camera not found |
| 500 | Internal server error |

## Security

- JWT Bearer Token
- HTTPS only

## Dependencies

- AI Vision Service
- Authentication Service
- Database Service