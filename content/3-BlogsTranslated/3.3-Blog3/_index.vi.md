---
title: "Blog 3"
date: 2026-06-20
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Tạo 3D Asset từ hình ảnh 2D bằng AI trên AWS

Sự phát triển của Generative AI đã mở ra nhiều hướng tiếp cận mới trong lĩnh vực thiết kế đồ họa và phát triển game. Thay vì phải xây dựng mô hình 3D hoàn toàn thủ công, lập trình viên có thể tận dụng các mô hình AI để chuyển đổi một hình ảnh 2D thành 3D Asset chỉ trong vài phút.

Trong bài viết này, chúng ta sẽ tìm hiểu một workflow triển khai trên AWS sử dụng hai mô hình AI mã nguồn mở là **TripoSG** và **MV-Adapter** nhằm tạo ra mô hình 3D có texture từ một ảnh concept ban đầu.

---

# Kiến trúc giải pháp

Giải pháp được chia thành hai giai đoạn xử lý chính nhằm tối ưu hiệu năng GPU cũng như chi phí vận hành.

Amazon S3 đóng vai trò là kho lưu trữ trung tâm, chứa ảnh đầu vào, các mô hình trung gian và kết quả cuối cùng ở định dạng **GLB**.

Quy trình xử lý gồm các bước sau:

1. Tải ảnh 2D lên Amazon S3.
2. Sử dụng Amazon EC2 GPU để sinh mesh 3D bằng TripoSG.
3. Lưu mesh vừa tạo lên Amazon S3.
4. Tiếp tục sử dụng EC2 GPU để tạo texture bằng MV-Adapter.
5. Xuất mô hình 3D hoàn chỉnh và lưu lại trên Amazon S3.

Luồng xử lý này giúp tách riêng các tác vụ có yêu cầu tài nguyên khác nhau, từ đó tối ưu hiệu suất và khả năng mở rộng của hệ thống.

---

# Sinh mesh 3D với TripoSG

Giai đoạn đầu tiên là tạo phần hình học (Geometry) của mô hình.

Để thực hiện bước này, có thể sử dụng các EC2 thuộc dòng **g4dn** được trang bị GPU NVIDIA và cài đặt sẵn môi trường Deep Learning.

Quy trình xử lý gồm:

- tải ảnh từ Amazon S3;
- chạy mô hình TripoSG;
- sinh mesh 3D;
- lưu kết quả dưới định dạng `.glb`;
- tải kết quả trở lại Amazon S3.

Mesh được tạo ra ở bước này đóng vai trò là nền tảng cho quá trình tạo texture ở bước tiếp theo.

---

# Phủ texture với MV-Adapter

Sau khi có mesh 3D, mô hình **MV-Adapter** sẽ sử dụng ảnh gốc làm tham chiếu để tạo texture đa góc nhìn.

Do quá trình này yêu cầu lượng bộ nhớ GPU lớn, nên có thể triển khai trên các EC2 thuộc dòng **g6e**.

Một vấn đề thường gặp là mesh sinh ra từ AI đôi khi xuất hiện lỗi **non-manifold**, khiến quá trình tạo texture bị gián đoạn.

Trước khi chạy MV-Adapter, cần thực hiện bước sửa lỗi mesh.

Ví dụ:

```bash
python fix_manifold.py \
inputs/raw_model.glb \
inputs/manifold_model.glb
```

Sau đó tiến hành tạo texture:

```bash
python -m scripts.texture_i2tex \
--image inputs/concept.jpeg \
--mesh inputs/manifold_model.glb \
--save_dir outputs \
--remove_bg
```

Sau khi hoàn thành, mô hình sẽ có đầy đủ hình học và texture để phục vụ các bước xử lý tiếp theo.

---

# Tối ưu mô hình trước khi sử dụng

Mô hình được tạo bởi AI thường chỉ phù hợp cho mục đích thử nghiệm hoặc tạo nguyên mẫu.

Để sử dụng trong game hoặc ứng dụng thời gian thực, cần thực hiện thêm một số bước tối ưu như:

- giảm số lượng polygon;
- chỉnh sửa topology;
- tối ưu UV Mapping;
- làm sạch mesh;
- bổ sung vật liệu (Material).

Các công cụ như **Blender** có thể hỗ trợ hiệu quả cho quá trình này.

---

# Rigging và Animation

Sau khi tối ưu mô hình, bước tiếp theo là gắn hệ thống xương (Rigging) để mô hình có thể thực hiện các chuyển động.

Một số lựa chọn phổ biến gồm:

- sử dụng **Rigify** trong Blender để tạo bộ xương;
- sử dụng **Mixamo** để tự động rig nhân vật và áp dụng các animation có sẵn.

Nhờ đó, mô hình AI có thể nhanh chóng được đưa vào các Game Engine như Unity hoặc Unreal Engine.

---

# Tối ưu chi phí trên AWS

Các EC2 GPU có chi phí tương đối cao nếu sử dụng trong thời gian dài.

Một số cách giúp giảm chi phí bao gồm:

- sử dụng các Deep Learning AMI đã cài sẵn CUDA và PyTorch;
- chỉ khởi tạo EC2 khi cần xử lý;
- lưu toàn bộ dữ liệu trên Amazon S3;
- tắt EC2 ngay sau khi hoàn thành workflow.

Đối với sinh viên hoặc người mới bắt đầu, có thể tham gia các chương trình AWS Cloud Bootcamp hoặc Workshop để nhận AWS Credits phục vụ quá trình nghiên cứu và thử nghiệm.

---

# Ứng dụng thực tế

Workflow này có thể được áp dụng trong nhiều lĩnh vực như:

- phát triển game;
- thiết kế nhân vật;
- tạo tài sản 3D cho Metaverse;
- AR/VR;
- mô phỏng sản phẩm;
- tạo nguyên mẫu nhanh (Rapid Prototyping).

Việc kết hợp các mô hình AI với hạ tầng GPU của AWS giúp giảm đáng kể thời gian xây dựng asset so với quy trình thiết kế truyền thống.

---

# Kết luận

Việc triển khai TripoSG và MV-Adapter trên AWS mang đến một quy trình hoàn chỉnh để chuyển đổi hình ảnh 2D thành mô hình 3D có texture.

Mặc dù các mô hình AI hiện nay vẫn chưa thể thay thế hoàn toàn quy trình thiết kế của các 3D Artist chuyên nghiệp, nhưng đây là một giải pháp hiệu quả cho giai đoạn tạo nguyên mẫu, giúp rút ngắn thời gian phát triển và giảm đáng kể khối lượng công việc thủ công.

Kết hợp với Amazon EC2 GPU và Amazon S3, workflow này mang lại khả năng mở rộng linh hoạt, phù hợp cho cả quá trình nghiên cứu lẫn triển khai trong các dự án phát triển game và ứng dụng đồ họa hiện đại.