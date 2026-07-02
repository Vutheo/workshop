---
title: "Blog 2"
date: 2026-06-16
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Xây dựng ứng dụng B2C an toàn với Amazon Cognito và Amazon Verified Permissions

Trong các ứng dụng B2C hiện đại, việc quản lý người dùng không chỉ dừng lại ở việc đăng nhập mà còn bao gồm kiểm soát quyền truy cập đến từng tài nguyên cụ thể.

Thông thường, hệ thống phải xử lý hai vấn đề chính:

- **Authentication:** Xác thực người dùng là ai  
- **Authorization:** Người dùng được phép làm gì  

AWS đề xuất kết hợp **Amazon Cognito** và **Amazon Verified Permissions** để tách biệt hai phần này, giúp hệ thống rõ ràng và dễ mở rộng hơn.

---

## Vấn đề của cách làm truyền thống

Trong nhiều ứng dụng, logic phân quyền thường được viết trực tiếp trong code:

- Khó mở rộng khi số lượng role tăng
- Khó bảo trì khi rule phức tạp
- Dễ bị phân tán logic authorization

Khi hệ thống phát triển lớn hơn, cách tiếp cận này không còn phù hợp.

---

## Giải pháp của AWS

AWS đưa ra kiến trúc tách biệt:

- **Amazon Cognito:** xử lý đăng nhập và cấp JWT token
- **Amazon Verified Permissions (AVP):** đánh giá quyền truy cập dựa trên policy

Thay vì viết logic trong code, ta định nghĩa policy riêng bằng **Cedar language** và để AVP xử lý quyết định.

---

## Luồng hoạt động hệ thống

**(Chèn hình tại đây: Architecture Diagram)**  
 *Hình 1: Luồng Cognito + Verified Permissions*

Luồng xử lý cơ bản:

1. User đăng nhập qua Amazon Cognito  
2. Cognito trả về JWT token  
3. Application gửi request kèm token  
4. Backend gọi Amazon Verified Permissions  
5. AVP đánh giá policy  
6. Trả về Allow / Deny  

---

## Các mô hình phân quyền hỗ trợ

Giải pháp hỗ trợ nhiều kiểu authorization phổ biến:

- **Resource-based access:** user chỉ truy cập tài nguyên của mình  
- **Role-based access (RBAC):** phân quyền theo vai trò  
- **Hierarchical access:** phân cấp quyền theo tổ chức  
- **Explicit deny:** luôn ưu tiên deny  
- **Admin override:** admin có quyền cao hơn  

---

## Lợi ích chính

Giải pháp này mang lại nhiều lợi ích rõ ràng:

- Tách biệt authentication và authorization  
- Giảm logic phân quyền trong application code  
- Dễ thay đổi policy mà không cần deploy lại hệ thống  
- Tăng khả năng audit và quản lý quyền tập trung  
- Phù hợp hệ thống B2C / SaaS nhiều người dùng  

---

### Amazon Cognito
- Tính theo **Monthly Active Users (MAU)**  
- Chi phí tăng theo số lượng người dùng  

### Amazon Verified Permissions
- Tính theo số lượng request đánh giá policy  
- Hệ thống càng nhiều request → chi phí càng cao  

- Với hệ thống nhỏ: chi phí thấp  
- Với hệ thống lớn: cần tối ưu số lần gọi AVP  

---

### 1. So với phân quyền trong code

| Tiêu chí | Code-based | Cognito + AVP |
|----------|------------|----------------|
| Bảo trì | Khó | Dễ hơn |
| Mở rộng | Kém | Tốt |
| Audit | Khó | Rõ ràng |
| Thay đổi rule | Phải deploy | Không cần deploy |

---

### 2. So với IAM

- IAM phù hợp cho system-to-system  
- AVP phù hợp cho application/B2C authorization  

---

### 3. Cách kiểm thử

Có thể test theo:

- Unit test policy Cedar  
- Test role (Admin/User/Guest)  
- Integration test Cognito → AVP  
- Scenario test theo use case thực tế  

---

## Kết luận

Việc kết hợp **Amazon Cognito** và **Amazon Verified Permissions** giúp xây dựng hệ thống B2C theo hướng hiện đại hơn:

> Tách biệt authentication và authorization, giúp hệ thống dễ mở rộng và dễ quản lý.

Tuy nhiên, cần chú ý tối ưu chi phí khi hệ thống có lượng request lớn.

---

### Nguồn tham khảo
https://aws.amazon.com/blogs/security/building-secure-b2c-applications-with-fine-grained-access-control-using-amazon-cognito-and-amazon-verified-permissions/