# API Versioning Strategy

API sử dụng semantic versioning.

## Quy tắc version

- v1.0.0: phiên bản đầu tiên
- Tăng minor (v1.1.0): thêm field, không phá vỡ client cũ
- Tăng major (v2.0.0): thay đổi breaking

## Cách áp dụng

- Version được đặt trong URL:
  /v1/vision/detect

## Nguyên tắc

- Không thay đổi schema cũ nếu chưa tăng version
- Luôn đảm bảo backward compatibility khi có thể.
