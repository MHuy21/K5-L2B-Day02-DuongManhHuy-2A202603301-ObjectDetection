# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp     | Định nghĩa của bạn                                                                                            | Ví dụ/điểm phân biệt                                                                                                                                                                                                      |
| ------- | ------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `car`   | Xe con 4-5 chỗ dùng để chở người (sedan, hatchback, SUV, taxi) và cả xe bán tải cỡ nhỏ có cabin giống xe con. | Xe sedan đen ở bên trái ảnh (`drive_003`), xe SUV đi song song xe sedan bên trái ảnh (`drive_003`), xe taxi đi phía trên xe sedan màu đen phía bên trái ảnh (`drive_003`), xe bán tải có thùng phía sau (ảnh `drive_004`) |
| `truck` | Xe tải chở hàng có thùng/thành thùng rõ ràng, tách biệt khỏi cabin lái, hoặc xe đầu kéo/rơ-moóc.              | Xe tải ben màu cam gỉ chở đất đá ngay giữa ảnh, thùng xe cao và tách rời rõ khỏi cabin lái (`drive_008`).                                                                                                                 |
| `bus`   | Xe buýt/minibus chở khách công cộng, thân dài, nhiều cửa lên xuống, kể cả xe buýt khớp nối hai toa.           | Xe buýt thân dài màu xanh dương-trắng-vàng bên phải ảnh, có bảng số tuyến điện tử phía sau xe (`drive_008`).                                                                                                              |
| `van`   | Xe van/minivan thân dạng hộp, nhỏ hơn bus, dùng chở hàng hoặc chở người, không có thùng tách rời như truck.   | Xe van nhỏ dạng hộp màu trắng/bạc, nằm bên trái xe bus màu vằng-trắng-xanh dương (`drive_008`).                                                                                                                           |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: mép box chỉ bao đúng phần thân xe nhìn thấy được trong ảnh (thân, kính, bánh nếu thấy), không lấn thêm mặt đường, bóng xe hay xe/vật thể bên cạnh.
- Vật thể bị che khuất: nếu một phần xe bị xe khác/cột/cây che nhưng phần còn lại đủ để nhận diện đúng lớp, vẫn vẽ box quanh đúng phần nhìn thấy được, không vẽ thêm phần bị che.
- Vật thể bị cắt bởi rìa ảnh: nếu xe nằm sát mép ảnh và bị cắt khung, box phải chạm đúng mép ảnh tại điểm xe bị cắt, không tự đoán phần nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: nếu xe quá nhỏ hoặc bị che gần hết tới mức không chắc chắn thuộc lớp nào, không tự đoán — ghi lại vào bảng ca mơ hồ bên dưới và hỏi giảng viên.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh         | Tình huống                                                                                                                                       | Quyết định cuối                         | Lý do theo schema                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `drive_025` | Xe SUV màu đen ở nửa phải ảnh, làn đường phía xa, bị hàng rào cây xanh trên dải phân cách che mất phần thân dưới; chỉ thấy nóc và kính phía trên, hơi nhòe do chuyển động. | `car`, vẽ box chỉ bao phần nhìn thấy phía trên hàng rào | Phần nóc và kính còn thấy đủ để nhận ra xe con/SUV nên vẫn phải gán nhãn; theo quy tắc vật bị che khuất, box chỉ ôm phần nhìn thấy, không đoán phần thân bị hàng rào che. |
| `drive_027` | Xe van nhỏ dạng hộp (mini cargo van) màu bạc, góc dưới-phải ảnh, không rõ đang chở hàng hay chở người.                                           | `van`                                   | Thân xe dạng hộp nhỏ hơn bus và không có thùng tách rời như truck, nên xếp vào `van` (van chở hàng hoặc chở người).                                      |
| `drive_048` | Xe buýt khớp nối: hai toa nối với nhau bằng khớp xếp kiểu harmonica (BRT), dễ nhầm là hai xe riêng.                                              | `bus` (một box duy nhất cho toàn bộ xe) | Khớp nối chỉ là cấu trúc cơ học của một phương tiện duy nhất, không phải hai xe riêng biệt, nên chỉ vẽ một box bao trọn cả hai toa.                      |
| `drive_014` | Xe cứu hộ màu trắng có cần cẩu phía sau, nằm giữa-dưới ảnh, cạnh hàng rào: cabin nhỏ giống xe con/van nhưng phía sau gắn thiết bị cẩu.           | `truck`                                 | Phía sau cabin là thiết bị chuyên dụng tách biệt khỏi cabin lái, đúng đặc điểm xe tải chuyên dụng; không phải xe chở người nên không là `car` hay `van`. |
| `drive_011` | Xe ba bánh chở thùng hàng màu vàng do người lái ngồi phía trước, gần các xe con màu bạc bên phải ảnh: có thùng chở hàng nên dễ nhầm với `truck`. | Không gán nhãn                          | Đây là xe máy ba bánh (mô tô chở hàng), không thuộc bốn lớp của schema                                                                                   |
