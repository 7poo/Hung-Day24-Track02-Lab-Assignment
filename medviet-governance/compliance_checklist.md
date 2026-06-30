# NĐ13/2023 Compliance Checklist — MedViet AI Platform

## A. Data Localization
- [ ] Tất cả patient data lưu trên servers đặt tại Việt Nam
- [ ] Backup cũng phải ở trong lãnh thổ VN
- [ ] Log việc transfer data ra ngoài nếu có

## B. Explicit Consent
- [ ] Thu thập consent trước khi dùng data cho AI training
- [ ] Có mechanism để user rút consent (Right to Erasure)
- [ ] Lưu consent record với timestamp

## C. Breach Notification (72h)
- [ ] Có incident response plan
- [ ] Alert tự động khi phát hiện breach
- [ ] Quy trình báo cáo đến cơ quan có thẩm quyền trong 72h

## D. DPO Appointment
- [ ] Đã bổ nhiệm Data Protection Officer
- [ ] DPO có thể liên hệ tại: dpo@medviet.local

## E. Technical Controls (mapping từ requirements)
| NĐ13 Requirement | Technical Control | Status | Owner |
|-----------------|-------------------|--------|-------|
| Data minimization | PII anonymization pipeline (Presidio) | ✅ Done | AI Team |
| Access control | RBAC (Casbin) + ABAC (OPA) | ✅ Done | Platform Team |
| Encryption | AES-256 at rest, TLS 1.3 in transit | 🚧 In Progress | Infra Team |
| Audit logging | CloudTrail + API access logs | ⬜ Todo | Platform Team |
| Breach detection | Anomaly monitoring (Prometheus) | ⬜ Todo | Security Team |

## F. TODO: Điền vào phần còn thiếu
Với mỗi row còn "⬜ Todo", mô tả technical solution cụ thể bạn sẽ implement.

- Audit logging: Bổ sung middleware FastAPI ghi `request_id`, user, role, endpoint, action, status code và timestamp vào log bất biến; đồng bộ log sang SIEM đặt tại VN và cấu hình retention tối thiểu 12 tháng.
- Breach detection: Dùng Prometheus scrape API/error/auth metrics, cấu hình alert khi có spike 401/403, truy cập raw PII bất thường, hoặc export restricted data ra ngoài VN; gửi cảnh báo qua incident channel và kích hoạt runbook 72h.
- Consent management: Tạo bảng `consent_records` gồm patient_id, purpose, granted_at, revoked_at; pipeline training chỉ đọc bản ghi còn hiệu lực.
- Right to erasure: Thêm job xóa hoặc loại khỏi tập training đối với patient_id đã rút consent, kèm audit trail cho thao tác xóa.
- Encryption completion: Dùng `SimpleVault` cho local dev, thay KEK file bằng KMS/HSM ở production, rotate DEK theo batch dữ liệu và không đưa `.vault_key` vào artifact nộp bài.
