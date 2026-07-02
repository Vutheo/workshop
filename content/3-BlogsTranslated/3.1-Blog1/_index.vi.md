---
title: "Blog 1"
date: 2026-06-05
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Tự động hóa UI với Amazon Nova Act

Việc tự động hóa các thao tác trên trình duyệt đã trở thành một phần quan trọng trong quá trình phát triển phần mềm. Những công việc như kiểm thử giao diện (UI Testing), thu thập dữ liệu (Web Scraping) hay tự động hóa các quy trình văn phòng đều yêu cầu trình duyệt thực hiện chính xác các thao tác của người dùng.

Trong nhiều năm, các framework như Selenium, Playwright hay Puppeteer đã trở thành lựa chọn phổ biến. Tuy nhiên, các công cụ này phụ thuộc rất nhiều vào XPath hoặc CSS Selector để xác định phần tử trên trang web. Khi giao diện thay đổi, các selector thường không còn hợp lệ, dẫn đến việc phải sửa lại toàn bộ script automation.

Amazon Nova Act được phát triển nhằm giải quyết hạn chế này bằng cách ứng dụng Agentic AI để điều khiển trình duyệt thông qua ngôn ngữ tự nhiên thay vì phụ thuộc vào cấu trúc HTML.

---

# Những hạn chế của Web Automation truyền thống

Các framework automation hiện nay hoạt động dựa trên việc xác định chính xác vị trí của từng thành phần trên giao diện.

Ví dụ:

- Click vào nút đăng nhập.
- Nhập dữ liệu vào ô tìm kiếm.
- Chọn một menu cụ thể.

Để thực hiện được điều này, lập trình viên phải sử dụng:

- XPath
- CSS Selector
- ID
- Name

Nhược điểm của phương pháp này là:

- Chỉ cần Front-end thay đổi cấu trúc HTML hoặc đổi tên class là toàn bộ script có thể bị lỗi.
- Chi phí bảo trì automation ngày càng lớn khi giao diện thay đổi liên tục.
- Việc xây dựng các workflow dài trở nên khó mở rộng.

---

# Amazon Nova Act là gì?

Amazon Nova Act là một dịch vụ AI Agent trên AWS giúp điều khiển trình duyệt web bằng ngôn ngữ tự nhiên.

Thay vì chỉ định chính xác XPath hoặc CSS Selector, người dùng chỉ cần mô tả hành động mong muốn bằng tiếng Anh.

Ví dụ:

```python
nova.act("Type 'iPhone' into the search bar and press Enter")
```

Nova Act sẽ tự động:

- Xác định ô tìm kiếm.
- Nhập dữ liệu.
- Thực hiện thao tác Enter.

Quá trình này không phụ thuộc trực tiếp vào HTML của trang web mà dựa trên khả năng hiểu giao diện của mô hình AI.

---

# Cách Nova Act hoạt động

Nova Act được xây dựng trên nền tảng Amazon Nova 2 Lite và được huấn luyện bằng Reinforcement Learning trong các môi trường mô phỏng trình duyệt (Web Gyms).

Thay vì nhận diện phần tử bằng XPath, AI sẽ phân tích:

- Giao diện trực quan.
- Bố cục của website.
- Ngữ cảnh của các thành phần.
- Nội dung hiển thị trên màn hình.

Nhờ đó, khi giao diện thay đổi nhỏ, workflow vẫn có khả năng tiếp tục hoạt động mà không cần chỉnh sửa code.

---

# Quy trình sử dụng Amazon Nova Act

## Bước 1: Thử nghiệm trên Playground

Amazon cung cấp một Playground cho phép người dùng trải nghiệm Nova Act trực tiếp trên trình duyệt.

Người dùng chỉ cần:

- nhập URL của website;
- mô tả yêu cầu bằng ngôn ngữ tự nhiên.

AI sẽ tự động:

- mở trang web;
- cuộn trang;
- di chuyển chuột;
- click;
- nhập dữ liệu.

Điều này giúp kiểm tra nhanh workflow mà không cần viết code.

---

## Bước 2: Phát triển bằng Python SDK

Sau khi thử nghiệm thành công, có thể sử dụng SDK Python để xây dựng workflow hoàn chỉnh.

Cài đặt:

```bash
pip install nova-act
```

Ví dụ:

```python
for customer in customer_list:
    nova.act(
        f"Fill name {customer['name']} and email {customer['email']} into register form"
    )

    nova.act(
        "Click Submit button and wait page reload"
    )
```

Trong ví dụ trên:

- Python xử lý dữ liệu từ danh sách khách hàng.
- Nova Act chịu trách nhiệm thao tác trên giao diện web.

Điều này cho phép kết hợp AI với toàn bộ logic của chương trình.

---

## Bước 3: Triển khai lên AWS

Sau khi hoàn thành workflow, Nova Act hỗ trợ triển khai trực tiếp lên hạ tầng AWS.

Quá trình triển khai bao gồm:

- đóng gói ứng dụng thành container;
- lưu trữ image trên Amazon ECR;
- chạy workflow trên Bedrock AgentCore;
- khởi tạo browser sandbox riêng cho từng phiên làm việc.

Việc này giúp mở rộng số lượng workflow mà không cần tự quản lý trình duyệt hoặc máy chủ.

---

# Các tính năng nổi bật

## Human-in-the-Loop (HITL)

Trong quá trình chạy automation, AI có thể gặp các tình huống như:

- CAPTCHA
- MFA
- tài khoản bị khóa

Thay vì dừng toàn bộ workflow, Nova Act sẽ gửi thông báo cho quản trị viên thông qua Amazon SNS.

Người dùng chỉ cần xử lý bước xác thực bằng tay, sau đó workflow sẽ tiếp tục thực hiện các bước còn lại.

---

## Observability

Nova Act cung cấp khả năng giám sát toàn bộ quá trình thực thi.

Người dùng có thể xem lại:

- video quá trình automation;
- ảnh chụp từng bước;
- lịch sử workflow.

Điều này giúp việc debug trở nên trực quan hơn so với các framework automation truyền thống.

---

## Enterprise Security

Toàn bộ workflow được thực thi trong môi trường sandbox của AWS.

Ngoài ra, Nova Act còn hỗ trợ:

- phân quyền bằng IAM;
- cô lập phiên làm việc;
- bảo vệ cookie và session;
- hạn chế rò rỉ dữ liệu.

Nhờ đó, giải pháp phù hợp với các môi trường doanh nghiệp yêu cầu mức bảo mật cao.

---

# Khi nào nên sử dụng Nova Act?

Nova Act phù hợp với các bài toán như:

- UI Automation Testing
- Web Automation
- Web Scraping
- Tự động điền biểu mẫu
- Xử lý các tác vụ văn phòng lặp đi lặp lại
- Xây dựng AI Agent thao tác trên trình duyệt

Đặc biệt, Nova Act phát huy hiệu quả trong các dự án có giao diện thường xuyên thay đổi.

---

# Kết luận

Amazon Nova Act mang đến một cách tiếp cận mới cho bài toán tự động hóa giao diện web bằng cách kết hợp AI Agent với trình duyệt.

Thay vì phụ thuộc hoàn toàn vào XPath hoặc CSS Selector, Nova Act cho phép lập trình viên mô tả hành động bằng ngôn ngữ tự nhiên, từ đó giảm đáng kể chi phí bảo trì script khi giao diện thay đổi.

Mặc dù Selenium và Playwright vẫn là những công cụ rất mạnh trong nhiều trường hợp, Nova Act mở ra một hướng tiếp cận mới cho các workflow cần khả năng thích nghi cao và tích hợp sâu với hệ sinh thái AWS. Đây là một lựa chọn đáng cân nhắc đối với các nhóm QA, DevOps và các doanh nghiệp đang xây dựng các hệ thống tự động hóa dựa trên AI.