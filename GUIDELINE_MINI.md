# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con, bao gồm sedan, SUV, taxi và xe bán tải.| Xe có kiểu dáng xe con hoặc bán tải; không xếp xe tải chở hàng vào lớp này.|
| `truck` |Xe tải chở hàng và xe đầu kéo. | 

Có kết cấu xe tải, thùng hàng hoặc đầu kéo; phân biệt với xe bán tải thuộc car|
| `bus` | 

Xe buýt và minibus chuyên chở hành khách.| Phương tiện có thiết kế phục vụ chở nhiều hành khách; không nhầm với van/minivan.|
| `van` | 

Xe van và minivan dùng để chở hàng hoặc chở người.| hoặc chở người.

Có kiểu dáng van/minivan; phân biệt với xe buýt lớn thuộc bus |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Vẽ bounding box hình chữ nhật bao quanh toàn bộ phần phương tiện nhìn thấy được, sát các cạnh ngoài của vật thể, hạn chế khoảng trống không cần thiết. Không bao gồm bóng đổ hoặc các vật thể xung quanh.
- Vật thể bị che khuất: Vẫn gán nhãn nếu phần phương tiện còn nhìn thấy đủ để nhận diện loại xe. Box chỉ bao quanh phần phương tiện thực sự nhìn thấy, không ước lượng phần bị che.
- Vật thể bị cắt bởi rìa ảnh: Vẫn gán nhãn nếu có đủ đặc điểm để xác định lớp. Box kéo đến rìa ảnh tại vị trí phương tiện bị cắt.
- Vật thể rất nhỏ/mơ hồ: Không gán nhãn nếu phương tiện quá nhỏ hoặc không đủ thông tin hình ảnh để xác định chắc chắn lớp. Không đoán class dựa trên suy luận thiếu căn cứ.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_003` | Phương tiện bị cắt một phần bởi rìa bên phải ảnh.| Gán nhãn nếu nhận diện được class; box bao quanh phần nhìn thấy và kéo đến rìa ảnh| Phương tiện bị cắt khung vẫn được gán nhãn nếu đủ đặc điểm nhận diện.|
| `drive_004` | Phương tiện nhỏ, hình ảnh bị mờ, khó nhận diện loại xe.| Chỉ gán nhãn khi đủ thông tin xác định class; nếu không thì bỏ qua.| Không suy đoán class đối với vật thể quá nhỏ hoặc mơ hồ.|
| `drive_005` | Nhiều phương tiện nằm gần nhau, bị che khuất và chồng lấn.| Gán box riêng cho từng phương tiện nếu nhận diện được; không gộp nhiều xe vào một box.| Mỗi vật thể thuộc schema phải có một bounding box riêng.|
