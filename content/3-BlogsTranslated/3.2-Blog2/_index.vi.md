---
title: "Blog 2"
date: 2026-06-16
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Xây Dựng Ứng Dụng B2C An Toàn Với Amazon Cognito Và Amazon Verified Permissions

![Amazon Cognito & Verified Permissions](/images/3-Blog/blog2.jpg)

Trong quá trình tìm hiểu về bảo mật cho các ứng dụng B2C, mình có đọc được một bài viết khá hay từ AWS Security Blog về cách kết hợp **Amazon Cognito** và **Amazon Verified Permissions** để triển khai cơ chế phân quyền chi tiết (*fine-grained access control*) cho ứng dụng.

Thông thường khi xây dựng một hệ thống, chúng ta sẽ phải giải quyết hai bài toán riêng biệt:

- **Authentication (Xác thực):** Người dùng là ai?
- **Authorization (Phân quyền):** Người dùng được phép thực hiện những hành động nào?

Amazon Cognito từ lâu đã là dịch vụ quen thuộc để quản lý đăng ký, đăng nhập và xác thực người dùng. Tuy nhiên, khi hệ thống ngày càng có nhiều vai trò người dùng, nhiều loại tài nguyên và các quy tắc truy cập phức tạp hơn, việc xử lý toàn bộ logic phân quyền ngay trong source code sẽ khiến ứng dụng khó bảo trì và khó mở rộng.

AWS đã giới thiệu **Amazon Verified Permissions** để giải quyết bài toán này.

---

## Amazon Verified Permissions hoạt động như thế nào?

Amazon Verified Permissions là dịch vụ quản lý quyền truy cập tập trung, sử dụng ngôn ngữ chính sách **Cedar**.

Thay vì viết hàng loạt câu lệnh `if...else` hoặc hard-code các điều kiện phân quyền trong ứng dụng, toàn bộ quy tắc truy cập sẽ được định nghĩa dưới dạng các **Policy** riêng biệt.

Trong kiến trúc được AWS giới thiệu:

1. Người dùng đăng nhập thông qua **Amazon Cognito**.
2. Cognito xác thực danh tính và phát hành **JWT Token**.
3. Khi người dùng gửi yêu cầu đến hệ thống, ứng dụng sẽ chuyển thông tin người dùng cùng hành động cần thực hiện đến **Amazon Verified Permissions**.
4. Verified Permissions đánh giá các **Cedar Policy** và trả về kết quả cho phép hoặc từ chối truy cập.

Việc tách riêng phần xác thực và phân quyền giúp ứng dụng trở nên linh hoạt và dễ quản lý hơn.

---

## Các mô hình phân quyền phổ biến

Bài viết của AWS giới thiệu nhiều mô hình phân quyền thường gặp trong các hệ thống doanh nghiệp.

### Resource Ownership

Người dùng chỉ được thao tác trên dữ liệu do chính mình sở hữu.

Ví dụ:

- Chỉnh sửa hồ sơ cá nhân.
- Cập nhật tài liệu của bản thân.

---

### Role-Based Access Control (RBAC)

Quyền truy cập được xác định dựa trên vai trò.

Ví dụ:

- Student
- Faculty
- Teaching Assistant
- Administrator

Mỗi vai trò sẽ có tập quyền khác nhau.

---

### Hierarchical Permissions

Quyền được kế thừa theo cấu trúc tổ chức.

Ví dụ:

- Trưởng khoa có quyền cao hơn giảng viên.
- Giảng viên có quyền cao hơn trợ giảng.

---

### Administrative Override

Quản trị viên có thể ghi đè các quy tắc phân quyền thông thường để xử lý các trường hợp đặc biệt.

---

### Explicit Deny

Nếu tồn tại một chính sách từ chối (Deny), chính sách đó sẽ luôn được ưu tiên cao nhất.

Điều này giúp hạn chế các rủi ro truy cập ngoài ý muốn.

---

## Ví dụ minh họa

AWS sử dụng ví dụ về một hệ thống quản lý học thuật.

Các vai trò bao gồm:

- Student
- Teaching Assistant
- Faculty
- Department Chair
- Administrator

Mỗi vai trò có phạm vi truy cập khác nhau.

Tuy nhiên, toàn bộ logic phân quyền đều được quản lý tập trung thông qua Amazon Verified Permissions thay vì viết trực tiếp trong ứng dụng.

Điều này giúp việc mở rộng hoặc thay đổi chính sách trở nên đơn giản hơn rất nhiều.

---

## Những điểm mình thấy đáng chú ý

Sau khi đọc bài viết, điều mình ấn tượng nhất là AWS đã tách riêng **Authentication** và **Authorization** thành hai lớp hoàn toàn độc lập.

Đối với các hệ thống có nhiều nhóm người dùng hoặc nhiều quy tắc truy cập khác nhau, cách tiếp cận này mang lại nhiều lợi ích:

- Tách biệt hoàn toàn Authentication và Authorization.
- Giảm đáng kể lượng code phân quyền trong ứng dụng.
- Dễ dàng cập nhật chính sách mà không cần sửa logic nghiệp vụ.
- Tăng khả năng kiểm toán và quản trị quyền truy cập.
- Phù hợp với các hệ thống SaaS hoặc B2C có nhiều người dùng và nhiều cấp quyền khác nhau.

---

## Tổng kết

Theo mình, **Amazon Cognito** và **Amazon Verified Permissions** là hai dịch vụ bổ trợ rất tốt cho nhau trong việc xây dựng hệ thống xác thực và phân quyền hiện đại.

Đối với những ứng dụng B2C cần kiểm soát quyền truy cập chi tiết nhưng vẫn đảm bảo khả năng mở rộng trong tương lai, đây là một kiến trúc rất đáng để tham khảo.

---

# Tài liệu tham khảo

- AWS Security Blog: https://aws.amazon.com/blogs/security/building-secure-b2c-applications-with-fine-grained-access-control-using-amazon-cognito-and-amazon-verified-permissions/