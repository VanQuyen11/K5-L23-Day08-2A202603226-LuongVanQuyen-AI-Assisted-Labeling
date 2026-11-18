# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603226 - Lương Văn Quyến

## 1. Câu hỏi mở đã chốt

| # | Câu hỏi | Quyết định | Ảnh ví dụ | case_id |
|---|---|---|---|---|
| 5 | Kính chắn gió nhìn xuyên qua có thuộc mask xe không? | Kính thuộc mask xe; khoảng hở không có kính không thuộc mask xe. Người được gán riêng theo phần nhìn thấy và quy tắc phân chia vùng chạm nhau. | img_013.jpg | CAL-02 |

## 2. Ca khó mới

### Bổ sung mục 4.2.3: kính và khoảng hở

Khi gán mask xe, phân biệt bề mặt kính chắn gió với khoảng hở không có kính. Giữ vùng kính thuộc xe, loại nền nhìn thấy qua khoảng hở. Khi người và xe chạm hoặc che nhau, áp dụng mục 4.2.4 để phân chia theo vật ở phía trước.

- Ảnh ví dụ: img_013.jpg.
- Nguồn quyết định: CAL-02.
- Cần kiểm tra ảnh gốc; không dùng riêng vùng tô màu của SAM để xác định bộ phận xe.

### Các ca đã đối chiếu, không thay đổi quy tắc

| case_id | Ảnh | Vấn đề | Cách xử lý theo v1.0 |
|---|---|---|---|
| CAL-01 | img_013.jpg | Mask SAM của tuk-tuk lấy thừa bánh xe máy bên cạnh | Áp dụng mục 4.2: tách riêng các vật, loại phần xe máy khỏi mask tuk-tuk. Đây là lỗi mask cần sửa. |
| CAL-03 | img_023.jpg | Box xe ba bánh nhỏ sát mép ảnh còn bị báo loose | Áp dụng mục 4.1: phóng to để kiểm tra, box bao phần xe nhìn thấy và dừng tại mép ảnh. Đây là lỗi cần kiểm tra và sửa, không phải quy tắc mới. |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 | Bản guideline ban đầu | Giảng viên cung cấp |
| v1.1 | Làm rõ sự khác nhau giữa kính chắn gió và khoảng hở khi gán mask xe | CAL-02, img_013.jpg |