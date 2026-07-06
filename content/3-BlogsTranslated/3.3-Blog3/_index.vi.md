---
title: "Blog 3"
date: 2026-06-20
weight: 3
chapter: false
---

# Tạo Mô Hình 3D Từ Ảnh 2D Bằng AI Trên AWS

![Generating 3D Assets with AI](/images/3-Blog/blog3.jpg)

Gần đây mình có đọc được một bài viết khá thú vị trên AWS GameTech về quy trình xây dựng pipeline tạo **mô hình 3D từ ảnh concept 2D** bằng các mô hình AI mã nguồn mở. Là một người yêu thích Game Development và AI, mình thấy workflow này khá thực tế vì cho phép lập trình viên tự sinh asset 3D mà không cần phụ thuộc vào các dịch vụ AI trả phí.

Trong bài viết này mình sẽ tóm tắt lại kiến trúc mà AWS đề xuất, đồng thời chia sẻ một số kinh nghiệm và lưu ý khi triển khai pipeline này trên AWS.

---

## Tổng quan kiến trúc

Để cân bằng giữa hiệu năng xử lý và chi phí sử dụng GPU, AWS đề xuất chia toàn bộ pipeline thành hai giai đoạn xử lý chính.

Ở trung tâm của hệ thống là **Amazon S3**, nơi lưu trữ ảnh concept ban đầu cũng như toàn bộ các file mô hình 3D được tạo ra trong suốt quá trình xử lý.

Pipeline bao gồm hai bước chính:

### Amazon S3

Amazon S3 đóng vai trò là kho lưu trữ trung tâm.

Tại đây sẽ lưu:

- Ảnh concept 2D ban đầu.
- File mô hình 3D định dạng `.glb` sau mỗi bước xử lý.
- Mô hình 3D hoàn chỉnh sau khi được phủ texture.

Việc sử dụng S3 giúp các bước xử lý hoạt động độc lập và dễ dàng mở rộng hệ thống.

---

## Bước 1 - Sinh Mesh 3D

Giai đoạn đầu tiên sử dụng **Amazon EC2 g4dn.2xlarge**, dòng máy được trang bị GPU NVIDIA phù hợp cho các tác vụ AI.

Quy trình xử lý gồm:

1. Tải ảnh concept từ Amazon S3.
2. Sử dụng mô hình **TripoSG** để sinh hình học (geometry) của vật thể.
3. Xuất kết quả thành file `.glb`.
4. Upload file vừa tạo trở lại Amazon S3.

Ở bước này mô hình chỉ mới có phần khung (mesh), chưa có texture.

---

## Bước 2 - Sinh Texture Đa Góc Nhìn

Sau khi có mesh, bước tiếp theo là phủ texture.

Giai đoạn này yêu cầu lượng VRAM lớn hơn rất nhiều nên AWS đề xuất sử dụng **Amazon EC2 g6e.2xlarge**.

Pipeline sử dụng mô hình **MV-Adapter** để:

- sử dụng ảnh concept ban đầu làm ảnh tham chiếu
- sinh texture từ nhiều góc nhìn khác nhau
- phủ texture lên mô hình vừa được tạo

Kết quả cuối cùng là một mô hình 3D hoàn chỉnh với đầy đủ texture.

---

# Triển khai trên AWS

Bước đầu tiên khi triển khai là lựa chọn đúng môi trường.

AWS cung cấp sẵn các **Deep Learning AMI** đã được cài đặt đầy đủ:

- CUDA
- PyTorch
- Driver NVIDIA
- Các thư viện AI phổ biến

Điều này giúp tiết kiệm khá nhiều thời gian so với việc tự cài đặt môi trường từ đầu.

---

## Mẹo tối ưu chi phí

Các dòng EC2 có GPU như **g4dn** hay **g6e** mang lại hiệu năng rất tốt nhưng chi phí thuê theo giờ cũng khá cao.

Nếu bạn là sinh viên hoặc lập trình viên mới, mình khuyến khích nên tham gia các chương trình như:

- Platform First Cloud AI Journey Bootcamp
- AWS Study Group
- Workshop của AWS Việt Nam

Hoàn thành các bài thực hành trong những chương trình này thường sẽ nhận được **AWS Credits**, đủ để thử nghiệm các GPU Instance phục vụ nghiên cứu mà không phải quá lo lắng về chi phí.

---

# Xử lý Mesh trước khi phủ Texture

Trong quá trình thử nghiệm, một vấn đề khá phổ biến là mesh sinh ra từ AI đôi khi xuất hiện lỗi **non-manifold**.

Một số lỗi thường gặp gồm:

- Các mặt bị giao nhau.
- Lưới bị hở.
- Topology không hợp lệ.

Nếu không xử lý trước, quá trình tạo texture rất dễ bị lỗi.

Ví dụ xử lý mesh:

```bash
python fix_manifold.py inputs/raw_model.glb inputs/manifold_model.glb
```

Sau đó tiếp tục thực hiện bước sinh texture:

```bash
python -m scripts.texture_i2tex \
    --image inputs/concept.jpeg \
    --mesh inputs/manifold_model.glb \
    --save_dir outputs \
    --remove_bg
```

Việc sửa mesh trước khi chạy MV-Adapter giúp toàn bộ pipeline hoạt động ổn định hơn.

---

# Đưa Asset AI vào Game Engine

Nhiều bài hướng dẫn thường kết thúc sau khi tạo được file `.glb`.

Tuy nhiên đối với lập trình game thì đây mới chỉ là bước đầu.

Các model do AI tạo ra thường:

- Có số lượng polygon chưa được tối ưu.
- Chưa có skeleton (rig).
- Chưa có animation.

Trước khi đưa vào Unity hoặc Unreal Engine, chúng ta thường cần thêm một vài bước xử lý.

Một workflow phổ biến gồm:

- Tối ưu model bằng **Blender**.
- Sử dụng plugin **Rigify** để tạo hệ thống xương.
- Upload model lên **Mixamo**.
- Tự động sinh rig và animation.

Sau các bước này, mô hình đã có thể được sử dụng trong các game engine hiện đại.

---

# Tổng kết

Việc sử dụng các mô hình AI mã nguồn mở như **TripoSG** và **MV-Adapter** trên AWS giúp lập trình viên chủ động hoàn toàn trong quá trình tạo asset 3D mà không cần phụ thuộc vào các API bên ngoài.

Mặc dù sản phẩm tạo ra từ AI hiện nay vẫn chưa thể thay thế hoàn toàn các 3D Artist chuyên nghiệp, nhưng đây là một công cụ rất hữu ích trong giai đoạn phát triển nguyên mẫu (Prototyping).

Việc tự động hóa quá trình tạo asset giúp nhóm phát triển tiết kiệm rất nhiều thời gian, từ đó có thể tập trung nhiều hơn vào những phần quan trọng như thiết kế gameplay, xây dựng hệ thống và phát triển các tính năng của trò chơi.

---

# Tài liệu tham khảo

- AWS GameTech Blog: https://aws.amazon.com/blogs/gametech/open-source-3d-game-asset-generation-using-aws/