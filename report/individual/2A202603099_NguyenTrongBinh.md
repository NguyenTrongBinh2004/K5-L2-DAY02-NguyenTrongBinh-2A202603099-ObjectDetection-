# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Trọng Bình
- MSSV: 2A202603099
- Email: trongbinhdev@gmail.com
- Lớp: L2A
- Link repo bài làm: [GitHub Repository](https://github.com/NguyenTrongBinh2004/K5-L2-DAY02-NguyenTrongBinh-2A202603099-ObjectDetection-)
- Link Colab đã chạy: [Google Colab - day2_annotation_to_yolo.ipynb](https://colab.research.google.com/github/NguyenTrongBinh2004/K5-L2-DAY02-NguyenTrongBinh-2A202603099-ObjectDetection-/blob/main/notebooks/day2_annotation_to_yolo.ipynb)

## 2. Schema và mini guideline

### 2.1. Schema

Schema của dự án gồm 4 lớp:

| Class ID | Tên lớp |
|---|---|
| 0 | car |
| 1 | truck |
| 2 | bus |
| 3 | van |

Trong quá trình xuất nhãn từ CVAT, phát hiện thứ tự lớp trong file xuất ban đầu không khớp với schema của dự án. Thứ tự ban đầu là `car`, `truck`, `van`, `bus`.

Đã sử dụng script để hoán đổi Class ID 2 và 3 trong 50 file nhãn, giữ nguyên tọa độ Bounding Box nhằm thống nhất với schema dự án.

### 2.2. Mini guideline

Các quy tắc gán nhãn và trường hợp mơ hồ được ghi trong file [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

Ba trường hợp mơ hồ đã ghi nhận:

1. `drive_003`: Xe bị cắt ở mép phải ảnh. Chỉ gán nhãn khi có đủ căn cứ nhận diện; Bounding Box giới hạn phần nhìn thấy và kéo đến mép ảnh.
2. `drive_004`: Xe nhỏ và bị mờ. Chỉ gán nhãn khi có đủ bằng chứng nhận diện; nếu không đủ căn cứ thì bỏ qua.
3. `drive_005`: Nhiều xe ở gần nhau và bị che khuất. Tạo Bounding Box riêng cho từng xe nhận diện được, không gộp nhiều xe thành một box.

Khi phân biệt `truck`, `bus`, `van`, áp dụng định nghĩa lớp trong guideline, chú ý cấu trúc xe và mục đích sử dụng. Các trường hợp không đủ bằng chứng nhận diện cần được rà soát thay vì tự suy đoán class.

## 3. Annotation và self-QC

### 3.1. Thống kê bộ nhãn train sau khi chỉnh sửa

- Số file nhãn train: 50 file `.txt`.
- Tổng số Bounding Box: 980.
- Phân bố class:

| Class | Số Bounding Box |
|---|---:|
| car | 792 |
| truck | 24 |
| bus | 94 |
| van | 70 |
| **Tổng** | **980** |

Đã kiểm tra lại số lượng file và Bounding Box bằng Python. Kết quả xác nhận có 50 file nhãn và tổng cộng 980 Bounding Box.

### 3.2. Kết quả kiểm tra cấu trúc

Đã chạy lệnh:

```cmd
python tools\check_yolo_labels.py --images dataset\images\train --labels dataset\labels\train
ĐẠT cấu trúc: 50/50 file nhãn hợp lệ.
Chờ ground truth để chấm chất lượng.