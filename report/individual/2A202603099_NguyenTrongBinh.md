# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Trọng Bình
- MSSV: 2A202603099
- Email: [trongbinhdev@gmail.com](mailto:trongbinhdev@gmail.com)
- Lớp: L2A
- Link repo bài làm: [GitHub Repository](https://github.com/NguyenTrongBinh2004/K5-L2-DAY02-NguyenTrongBinh-2A202603099-ObjectDetection-)
- Link Colab đã chạy: [Google Colab - day2_annotation_to_yolo.ipynb](https://colab.research.google.com/github/NguyenTrongBinh2004/K5-L2-DAY02-NguyenTrongBinh-2A202603099-ObjectDetection-/blob/main/notebooks/day2_annotation_to_yolo.ipynb)


## 2. Schema và mini guideline

Tham khảo file [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

### 2.1. Schema dự án

Schema gồm 4 lớp:

| Class ID | Tên lớp |
|---|---|
| 0 | car |
| 1 | truck |
| 2 | bus |
| 3 | van |

Trong quá trình xuất nhãn từ CVAT, tôi phát hiện thứ tự lớp trong file `data.yaml` ban đầu không khớp với schema dự án. Thứ tự ban đầu là `car`, `truck`, `van`, `bus`.

Tôi đã sử dụng script `tools/hoan_doi_class_id.py` để hoán đổi Class ID 2 và 3 trong 50 file nhãn, giữ nguyên tọa độ Bounding Box nhằm thống nhất dữ liệu với schema dự án.

### 2.2. Ba trường hợp mơ hồ

Các trường hợp được ghi nhận trong `GUIDELINE_MINI.md`:

1. **drive_003 – Xe bị cắt ở mép phải ảnh:** Chỉ gán nhãn khi có đủ căn cứ nhận diện. Bounding Box giới hạn phần xe nhìn thấy và kéo đến mép ảnh.
2. **drive_004 – Xe nhỏ và bị mờ:** Chỉ gán nhãn khi có đủ bằng chứng nhận diện. Nếu không đủ căn cứ thì bỏ qua, tránh tự suy đoán đối tượng.
3. **drive_005 – Nhiều xe ở gần nhau và bị che khuất:** Tạo Bounding Box riêng cho từng xe nhận diện được, không gộp nhiều xe thành một box.

### 2.3. Cách áp dụng schema và nội dung cần làm rõ

Khi phân biệt `truck`, `bus` và `van`, tôi áp dụng định nghĩa lớp trong guideline, chú ý cấu trúc xe và mục đích sử dụng. Với các đối tượng bị mờ, che khuất hoặc không đủ bằng chứng nhận diện, cần rà soát thay vì tự suy đoán class.

Các trường hợp khó phân biệt giữa `truck`, `bus` và `van` cần được giảng viên/Coach làm rõ thêm nếu guideline chưa đưa ra tiêu chí nhận diện đủ cụ thể.

## 3. Annotation và self-QC

### 3.1. Thống kê bộ nhãn train

- Số file nhãn train: 50 file `.txt`.
- Tổng số Bounding Box: 980.
- Thư mục nhãn: `dataset/labels/train/`.
- Thư mục ảnh: `dataset/images/train/`.

Phân bố Bounding Box theo lớp:

| Class | Số Bounding Box |
|---|---:|
| car | 792 |
| truck | 24 |
| bus | 94 |
| van | 70 |
| **Tổng** | **980** |

### 3.2. Kết quả checker và lỗi đã xử lý

Đã chạy lệnh kiểm tra:

```cmd
python tools\check_yolo_labels.py --images dataset\images\train --labels dataset\labels\train
```

Kết quả:

```text
ĐẠT cấu trúc: 50/50 file nhãn hợp lệ.
Chờ ground truth để chấm chất lượng.
```

Kết quả xác nhận 50/50 file nhãn đáp ứng kiểm tra cấu trúc YOLO. Đây là kiểm tra định dạng và tính hợp lệ của file, không đồng nghĩa với việc toàn bộ Bounding Box đã chính xác về vị trí và class.

Lỗi đã phát hiện và xử lý:

- Thứ tự Class ID trong dữ liệu xuất từ CVAT không khớp schema dự án.
- Sử dụng script hoán đổi Class ID 2 và 3 trong 50 file nhãn.
- Giữ nguyên tọa độ Bounding Box trong quá trình xử lý.
- Sao lưu nhãn CVAT ban đầu tại `outputs/backup_cvat_goc`.

### 3.3. Danh sách ảnh đã rà bằng mắt

Tôi đã rà soát trực quan ít nhất 10 ảnh bằng CVAT.

Tuy nhiên, danh sách tên ảnh cụ thể đã rà chưa được lưu lại, vì vậy tôi không ghi tên ảnh khi chưa xác minh chính xác.

Một số trường hợp cần chú ý trong quá trình gán nhãn đã được ghi nhận tại `GUIDELINE_MINI.md`:

- `drive_003`: Xe bị cắt ở mép ảnh.
- `drive_004`: Xe nhỏ và bị mờ.
- `drive_005`: Xe bị che khuất hoặc nằm gần nhau.

### 3.4. Lỗi phát hiện và cách xử lý

Vấn đề cụ thể đã xác định được là thứ tự Class ID không khớp schema dự án.

Cách xử lý là hoán đổi Class ID 2 và 3 trên 50 file nhãn, giữ nguyên tọa độ Bounding Box.

Đối với các lỗi bỏ sót đối tượng hoặc sai lệch Bounding Box, chưa có danh sách lỗi cụ thể được ghi nhận theo từng ảnh trong quá trình rà soát. Vì vậy, tôi không khẳng định đã sửa toàn bộ lỗi FP/FN ở bước này.

### 3.5. Commit khóa nhãn ban đầu

- Commit: `81375bf`
- Nội dung: `day2: lock initial train annotations`.

Commit này dùng để lưu lại bộ nhãn ban đầu trước các bước chỉnh sửa tiếp theo.

## 4. Gold QC và rework

### 4.1. Kết quả đánh giá trước và sau chỉnh sửa

| Chỉ số | Trước sửa | Sau sửa |
|---|---:|---:|
| TP | 469 | 467 |
| FP | 581 | 513 |
| FN | 184 | 186 |
| Precision | 0.4467 | 0.4765 |
| Recall | 0.7182 | 0.7152 |
| F1 | 0.5508 | 0.5720 |
| mean_iou_of_matches | 0.8383 | 0.8383 |
| Student box count | 1050 | 980 |
| passed | false | false |

Kết quả đánh giá sau chỉnh sửa được lưu tại `outputs/annotation_evaluation.json`.

### 4.2. Cấu hình đánh giá

- IoU match threshold: 0.50.
- Ngưỡng F1 yêu cầu: 0.80.
- Ngưỡng mean IoU yêu cầu: 0.70.

Lệnh đánh giá:

```cmd
python tools/evaluate_annotations.py --student-labels dataset/labels/train --gold-labels gold_train_labels/gold_train_labels --iou-threshold 0.50 --min-f1 0.80 --min-mean-iou 0.70 --output outputs/annotation_evaluation.json
```

### 4.3. Nhận xét kết quả

Kết quả sau chỉnh sửa:

- TP = 467.
- FP = 513.
- FN = 186.
- Precision = 0.4765.
- Recall = 0.7152.
- F1 = 0.5720.
- Mean IoU = 0.8383.

So với kết quả trước chỉnh sửa:

- FP giảm 68.
- TP giảm 2.
- FN tăng 2.
- Precision tăng từ 0.4467 lên 0.4765.
- Recall giảm từ 0.7182 xuống 0.7152.
- F1 tăng từ 0.5508 lên 0.5720.
- Mean IoU giữ nguyên ở mức 0.8383.
- Số lượng Student Box giảm từ 1050 xuống 980.

F1 sau chỉnh sửa vẫn thấp hơn ngưỡng yêu cầu 0.80, trong khi mean IoU cao hơn ngưỡng yêu cầu 0.70. Vì vậy, kết quả Gold QC vẫn là **CHƯA ĐẠT** (`passed = false`).

### 4.4. Rework và quyết định xử lý

Trong quá trình xử lý, tôi đã chỉnh sửa Class ID 2 và 3 để thống nhất dữ liệu với schema dự án. Tuy nhiên, việc hoán đổi Class ID không đồng nghĩa với việc đã sửa toàn bộ lỗi FP/FN hoặc thực hiện rework trực quan theo từng ảnh.

Theo trao đổi với Key Coach/giảng viên, tôi được phép bỏ qua Gold QC gate để tiếp tục bước huấn luyện. Việc này chỉ cho phép tiếp tục quy trình, không đồng nghĩa với việc bộ nhãn đã vượt qua Gold QC.

**Chưa thực hiện một đợt rework trực quan có hệ thống sau kết quả Gold QC.**

Do chưa đối chiếu và ghi nhận từng trường hợp FP/FN theo ảnh, nhãn Gold và Bounding Box tương ứng, tôi không suy đoán nguyên nhân cụ thể của các lỗi chỉ từ số liệu tổng hợp.

## 5. Kết quả YOLO

### 5.1. Runtime, GPU và phiên bản thư viện

| Thành phần | Giá trị |
|---|---|
| Model | YOLO26n |
| Runtime | Google Colab |
| GPU | NVIDIA Tesla T4 |
| Python | 3.13.16 |
| PyTorch | 2.14.1+cu130 |
| Ultralytics | 8.4.208 |
| Epochs | 50 |
| Image size | 640 |
| Batch | -1 (AutoBatch tự chọn batch size 44) |
| Device | CUDA:0 |
| Seed | 20260909 |

Không thay đổi các tham số huấn luyện nêu trên so với cấu hình notebook.

Đường dẫn checkpoint tốt nhất trong phiên Colab:

```text
/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt
```

Checkpoint `best.pt` và `last.pt` không được đưa vào GitHub. Tôi chỉ sao chép các log, biểu đồ và hình ảnh cần thiết cho báo cáo.

### 5.2. Kết quả đánh giá trên 10 ảnh Eval

| Chỉ số trên 10 ảnh Eval | Giá trị |
|---|---:|
| Precision | 0.4105 |
| Recall | 0.2303 |
| mAP50 | 0.2723 |
| mAP50–95 | 0.2017 |

Kết quả theo từng lớp:

| Class | Precision | Recall | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|
| car | 0.833 | 0.638 | 0.739 | 0.523 |
| truck | 0.675 | 0.200 | 0.239 | 0.206 |
| bus | 0.000 | 0.000 | 0.0079 | 0.0039 |
| van | 0.134 | 0.0833 | 0.103 | 0.0741 |

Kết quả cho thấy mô hình nhận diện lớp `car` tốt hơn các lớp còn lại. Hiệu quả nhận diện `bus` và `van` còn thấp. Không nên kết luận mô hình có độ chính xác cao dựa trên kết quả hiện tại.

### 5.3. Ảnh dự đoán và tài liệu huấn luyện

Các thư mục kết quả đã được giải nén và sao chép vào `report/individual/assets/`:

- Ảnh dự đoán Eval: [assets/eval_predictions/](assets/eval_predictions/).
- Log và biểu đồ huấn luyện: [assets/yolo26_runs/](assets/yolo26_runs/).
- Chỉ số đánh giá dạng JSON: `outputs/model_eval_metrics.json`.

Có 10 ảnh dự đoán từ `drive_051.jpg` đến `drive_060.jpg`.

Một số ví dụ cụ thể:

- `drive_052.jpg`: Mô hình dự đoán 11 đối tượng `car` và 1 đối tượng `bus`.
- `drive_059.jpg`: Mô hình dự đoán 6 đối tượng `car`, 1 đối tượng `truck` và 1 đối tượng `van`.

Đây là số lượng đối tượng được mô hình dự đoán, không phải số lượng đối tượng được xác nhận dự đoán đúng. Cần đối chiếu với nhãn chuẩn để xác định kết quả chính xác.

### 5.4. Trả lời bốn câu hỏi cuối notebook

**Câu 1. Lớp nào có recall thấp nhất? Ảnh nào minh họa?**

Lớp `bus` có recall thấp nhất, bằng 0.000. Tiếp theo là `van` với recall 0.0833.

Có thể sử dụng `drive_052.jpg` làm ảnh minh họa vì kết quả dự đoán của mô hình có xuất hiện một đối tượng `bus`. Tuy nhiên, cần đối chiếu với nhãn chuẩn của ảnh để xác định mô hình dự đoán đúng hay sai. Không thể dùng riêng ảnh dự đoán này để chứng minh recall của lớp `bus`.

**Câu 2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên Bounding Box?**

Cần kiểm tra độ chính xác định vị của Bounding Box:

- Box có ôm sát thân xe không?
- Box có quá rộng hoặc quá hẹp không?
- Box có bao gồm bóng hoặc vật thể nền không?
- Box có xử lý đúng xe bị che khuất hoặc bị cắt ở mép ảnh không?

mAP50–95 đánh giá ở nhiều ngưỡng IoU nghiêm ngặt hơn mAP50, nên nhạy hơn với sai lệch vị trí và kích thước Bounding Box.

**Câu 3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**

Các cặp lớp cần chú ý khi rà soát là `truck` và `van`, hoặc `bus` và `van`, vì hình dáng của chúng có thể gần giống nhau trong ảnh nhỏ, mờ hoặc bị che khuất.

Guideline đã đề cập đến việc phân biệt các lớp dựa trên cấu trúc xe và mục đích sử dụng. Tuy nhiên, các chỉ số tổng hợp hiện tại chưa đủ để kết luận lỗi class cụ thể nào bắt nguồn từ guideline. Cần kiểm tra từng ảnh, nhãn chuẩn và dự đoán tương ứng để có kết luận đáng tin cậy.

**Câu 4. Vì sao không dùng 10 ảnh Eval để train? Vai trò validation của chúng trong notebook là gì?**

Không dùng 10 ảnh Eval để train nhằm tránh việc các ảnh này tham gia trực tiếp vào quá trình cập nhật trọng số của mô hình.

Trong notebook này, 10 ảnh Eval được sử dụng làm validation (`split='val'`) trong quá trình huấn luyện và đánh giá checkpoint để lựa chọn `best.pt`.

Vì vậy, đây không phải một tập test độc lập hoàn toàn chưa được sử dụng trong quá trình phát triển mô hình. Kết quả trên tập này cần được hiểu là kết quả validation.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Xuất nhãn CVAT | Thứ tự class trong `data.yaml` không khớp schema dự án | Kiểm tra schema và hoán đổi Class ID 2, 3 bằng script | Đã xử lý 50 file; giữ nguyên tọa độ Bounding Box |
| Kiểm tra nhãn | Cần xác nhận định dạng YOLO | Chạy `tools/check_yolo_labels.py` | Đạt 50/50 file hợp lệ về cấu trúc |
| Gold QC | F1 sau chỉnh sửa đạt 0.5720, thấp hơn ngưỡng 0.80 | Đọc kết quả TP, FP, FN và mean IoU | Chưa đạt Gold QC; được Key Coach cho phép bỏ qua gate |
| Huấn luyện | Cần môi trường GPU để huấn luyện YOLO | Chạy notebook trên Google Colab | Hoàn thành 50 epochs trên Tesla T4 |
| Đánh giá mô hình | Recall của `bus` và `van` thấp | Đánh giá theo từng lớp và tạo ảnh dự đoán | Đã có metrics và 10 ảnh dự đoán; hiệu quả nhận diện hai lớp còn thấp |

## 7. Tự đánh giá theo rubric

Đối chiếu với [RUBRIC.md](../../RUBRIC.md):

| Hạng mục | Kết quả tự đánh giá |
|---|---|
| Gán nhãn 50 ảnh train | Đã có 50 file nhãn |
| Schema và Class ID | Đã kiểm tra, hoán đổi ID 2 và 3 để khớp schema |
| Mini guideline | Đã có định nghĩa lớp và 3 trường hợp mơ hồ |
| Kiểm tra cấu trúc nhãn | Đạt 50/50 file hợp lệ |
| Rà soát trực quan | Đã rà ít nhất 10 ảnh trong CVAT; chưa lưu danh sách tên ảnh cụ thể |
| Gold QC | Chưa đạt ngưỡng F1 yêu cầu |
| Rework sau Gold QC | Chưa thực hiện rework trực quan có hệ thống theo từng lỗi FP/FN |
| Huấn luyện YOLO | Đã hoàn thành 50 epochs |
| Đánh giá Eval | Đã có chỉ số và ảnh dự đoán cho 10 ảnh |
| Tài liệu minh họa | Đã sao chép log, biểu đồ và ảnh dự đoán vào `assets/` |
| Báo cáo cá nhân | Đã cập nhật số liệu và nội dung theo kết quả thực tế |

Các hạn chế còn lại:

- Gold QC chưa đạt ngưỡng F1 yêu cầu.
- Chưa thực hiện rework trực quan có hệ thống sau Gold QC.
- Chưa lưu danh sách cụ thể các ảnh đã rà bằng mắt.
- Kết quả nhận diện `bus` và `van` còn thấp.
- 10 ảnh Eval đã được sử dụng làm validation, vì vậy chưa có kết quả đánh giá trên một tập test độc lập.

Việc được phép bỏ qua Gold QC gate không thay thế yêu cầu ghi nhận trung thực kết quả đánh giá.

## 8. Công cụ AI và tài liệu tham khảo

### 8.1. Công cụ đã sử dụng

| Công cụ | Mục đích sử dụng | Phần đã tự kiểm tra |
|---|---|---|
| CVAT | Gán nhãn Bounding Box và rà soát trực quan | Kiểm tra các box và class trên ảnh |
| Python | Kiểm tra, xử lý Class ID và chạy script đánh giá nhãn | Kiểm tra số lượng file, số box và kết quả script |
| VS Code | Chỉnh sửa mã nguồn, notebook và báo cáo | Kiểm tra nội dung file và cấu trúc thư mục |
| CMD | Chạy lệnh kiểm tra và thao tác Git | Kiểm tra kết quả lệnh thực tế |
| Git/GitHub | Quản lý phiên bản và lưu trữ bài làm | Kiểm tra commit và repository |
| Google Colab | Huấn luyện và đánh giá YOLO26n trên GPU Tesla T4 | Kiểm tra log huấn luyện, metrics và ảnh dự đoán |
| ChatGPT | Hỗ trợ giải thích quy trình, phân tích kết quả, hướng dẫn lệnh và rà soát nội dung báo cáo | Đối chiếu lệnh, số liệu và kết quả thực tế trên máy tính và Google Colab |

### 8.2. Tài liệu tham khảo

Các tài liệu trong repository:

- `SUBMISSION.md`
- `RUBRIC.md`
- `GUIDELINE_MINI.md`
- `notebooks/day2_annotation_to_yolo.ipynb`
- `outputs/annotation_evaluation.json`
- `outputs/model_eval_metrics.json`

Tài liệu bên ngoài:

- Ultralytics YOLO Documentation: [https://docs.ultralytics.com/](https://docs.ultralytics.com/)

Không đính kèm nhãn Gold train, mật khẩu hoặc token trong báo cáo và repository công khai.