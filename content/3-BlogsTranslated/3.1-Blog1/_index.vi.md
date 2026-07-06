---
title: "Blog 1"
date: 2026-06-05
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Trải nghiệm Amazon Nova Act: Tự động hóa UI bằng AI, không lo fix XPath

![Amazon Nova Act](/images/3-Blog/blog1.jpg)

Hôm nay mình muốn chia sẻ với mọi người về một dịch vụ automation mà mình mới tìm thấy và có dịp trải nghiệm gần đây. Chuyện là mình đang "sấp mặt" với task viết script automation để test UI cho project nhóm. Khổ nỗi là phía Frontend liên tục cập nhật giao diện, khiến các script Selenium của mình cứ chạy được một lúc lại lỗi vì không tìm thấy Selector hoặc XPath.

Trong lúc gần như muốn bỏ cuộc thì mình biết đến **Amazon Nova Act**. Ban đầu mình nghĩ đây cũng chỉ là một lớp AI bọc quanh Selenium. Nhưng sau khi thử áp dụng vào đúng bài toán của mình thì nhận ra nó khác hoàn toàn. Hôm nay mình sẽ chia sẻ trải nghiệm và review chi tiết về dịch vụ automation rất hứa hẹn này.

## Nỗi khổ của Web Automation truyền thống

Trước khi đi vào Nova Act, mình xin giải thích nhanh về Web Automation.

Web Automation là việc viết chương trình để máy tính tự động thao tác trên trình duyệt giống như con người: click chuột, nhập dữ liệu, cuộn trang, chọn menu...

Đây là kỹ thuật được sử dụng rất nhiều trong:

- **Automation Testing**: kiểm thử website tự động trước khi phát hành.
- **Web Scraping**: thu thập dữ liệu từ các website.
- **Business Automation**: tự động hóa các tác vụ văn phòng như nhập dữ liệu, xử lý biểu mẫu,...

Thông thường mình sử dụng các công cụ như:

- Selenium
- Playwright
- Puppeteer

Điểm chung của các công cụ này là phải xác định chính xác vị trí của từng phần tử HTML thông qua:

- XPath
- CSS Selector

Vấn đề là chỉ cần Frontend đổi một class CSS, đổi cấu trúc HTML hoặc di chuyển một nút bấm sang vị trí khác thì toàn bộ script gần như phải sửa lại.

Đó là công việc tốn thời gian nhất khi làm UI Automation.

## Amazon Nova Act giải quyết bài toán này như thế nào?

Amazon Nova Act là một dịch vụ giúp xây dựng các **AI Agent** có khả năng điều khiển trình duyệt web giống như con người.

Điểm đặc biệt là:

**Không cần XPath.**

**Không cần CSS Selector.**

Thay vào đó chỉ cần sử dụng ngôn ngữ tự nhiên.

Ví dụ:

```python
nova.act("Type 'iPhone' into the search bar and then press enter")
```

Nova Act sẽ tự:

- xác định ô tìm kiếm
- nhập dữ liệu
- nhấn Enter

mà không cần biết HTML của website như thế nào.

AWS cho biết Nova Act sử dụng **Amazon Nova 2 Lite**, được huấn luyện bằng **Reinforcement Learning** trên các môi trường mô phỏng gọi là **Web Gyms**.

Thay vì ghi nhớ XPath, mô hình học cách quan sát giao diện và đưa ra hành động giống con người.

Theo AWS công bố, Nova Act đạt độ chính xác hơn **90%** trên các bài đánh giá thực tế.

# Từ Playground đến Production

## Bước 1. Trải nghiệm trên Playground

Đầu tiên mình thử trực tiếp tại:

https://nova.amazon.com/act

Playground hoạt động hoàn toàn theo dạng No-code.

Chỉ cần:

- nhập URL
- nhập yêu cầu bằng tiếng Anh

AI sẽ tự thao tác trên trình duyệt.

Bạn có thể quan sát toàn bộ quá trình:

- cuộn trang
- click
- nhập dữ liệu
- chuyển trang

theo thời gian thực.

## Bước 2. Viết code bằng Python SDK

Sau khi thử trên Playground, mình chuyển sang viết Python.

Cài đặt:

```bash
pip install nova-act
```

AWS cũng cung cấp Extension cho VS Code giúp việc phát triển thuận tiện hơn.

Nova Act hỗ trợ mô hình **Hybrid Automation**.

Bạn vừa có thể:

- dùng AI thực hiện thao tác trên giao diện
- vừa kết hợp với code Python truyền thống

Ví dụ:

```python
for customer in customer_list:
    nova.act(
        f"Fill name {customer['name']} and email {customer['email']} into form register"
    )

    nova.act(
        "Click Submit button and wait page reload"
    )
```

Python xử lý logic.

Nova Act xử lý UI.

Extension còn hỗ trợ **Live Debug** ngay trong VS Code bằng trình duyệt nhúng.

## Bước 3. Deploy lên AWS

Khi hoàn thành chương trình, việc triển khai khá đơn giản.

Nova Act sẽ:

- đóng gói ứng dụng
- tạo container
- đẩy lên Amazon ECR
- chạy trên Bedrock AgentCore

Mỗi workflow sẽ được thực thi trong một Browser Sandbox riêng biệt.

Bạn có thể mở rộng lên hàng trăm phiên chạy đồng thời mà không cần tự quản lý Chrome Headless hay EC2.

# Ba tính năng nổi bật của Nova Act

## Human-in-the-Loop (HITL)

Đây là tính năng mình đánh giá rất cao.

Nếu AI gặp:

- CAPTCHA
- tài khoản bị khóa
- yêu cầu xác minh

thì workflow sẽ không bị dừng.

Nova Act sẽ gửi thông báo thông qua Amazon SNS.

Quản trị viên chỉ cần:

- mở liên kết
- xử lý CAPTCHA
- AI tiếp tục chạy phần còn lại.

## Observability

Nova Act cho phép theo dõi toàn bộ quá trình thực thi.

Bạn có thể xem:

- Video replay
- Screenshot từng bước

để kiểm tra AI có thao tác đúng hay không.

Điều này rất hữu ích khi debug.

## Enterprise Security

Toàn bộ workflow được chạy trong môi trường sandbox của AWS.

Có thể:

- phân quyền IAM
- quản lý phiên đăng nhập
- bảo vệ Cookie
- cô lập dữ liệu giữa các workflow

rất phù hợp với môi trường doanh nghiệp.

# Đánh giá cá nhân

Sau khi trải nghiệm, mình thấy Amazon Nova Act không chỉ là một công cụ AI demo.

Điểm mình thích nhất là:

- Không còn phụ thuộc vào XPath.
- Có thể điều khiển trình duyệt bằng ngôn ngữ tự nhiên.
- Kết hợp được AI và Python trong cùng một workflow.
- Triển khai trực tiếp lên hạ tầng AWS rất thuận tiện.

Nếu bạn đang làm:

- QA Automation
- DevOps
- RPA
- Web Automation
- AI Agent

thì mình nghĩ Nova Act là một dịch vụ rất đáng để trải nghiệm.

# Tài liệu tham khảo

- Playground: https://nova.amazon.com/act
- AWS News Blog: https://aws.amazon.com/blogs/aws/build-reliable-ai-agents-for-ui-workflow-automation-with-amazon-nova-act/
- AWS Documentation: https://docs.aws.amazon.com/nova-act/latest/userguide/what-is-nova-act.html