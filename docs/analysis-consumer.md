# docs/analysis-consumer.md

# Consumer Analysis — AI Vision Service

## Overview

AI Vision Service sử dụng frame camera để thực hiện object detection.

## Consumer Requirements

- Response dưới 3 giây
- Frame ổn định
- Metadata đầy đủ
- Detection response thống nhất

## APIs Consumed

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /camera/frame/{cameraId} | Get frame |
| GET | /camera/status/{cameraId} | Check camera |
| POST | /vision/detect | Detect object |

## Required Fields

- camera_id
- image_url
- detection_id
- confidence

## Error Expectations

- Problem Details format
- JSON response
- Retry support

## Performance

- Max latency 3 giây
- Max frame size 5MB

## Security

- Bearer token
- HTTPS transport