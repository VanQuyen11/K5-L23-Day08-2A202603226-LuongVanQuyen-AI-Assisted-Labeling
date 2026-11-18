# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 8 ảnh (3 ca khó + 5 ngẫu nhiên), 190 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 7/190 = 3.7% | 190 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 5/8 = 62.5% | 8 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 441/524 = 84.2% / 21/524 = 4.0% / 52/524 = 9.9% | 524 box máy |
| Máy: added | 24/486 = 4.9% | 486 đối tượng sau duyệt |
| Bắt lỗi bẫy | 11/12 = 91.7% | 12 bẫy |

## Top 3 loại lỗi
| Loại lỗi | Số lỗi | Ảnh ví dụ |
|---|---|---|
| thua | 4 | img_010.jpg, img_024.jpg |
| sai_vi_tri_kich_thuoc | 1 | img_003.jpg |
| bo_sot | 1 | img_017.jpg |

**Lỗi hệ thống**: 'thua' lặp lại -> sửa quy tắc / recipe, không chỉ sửa từng ảnh.

## Giới hạn thống kê
- 5 ảnh lỗi trong 8 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **86%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**REWORK**

- ảnh có lỗi 62.5% > 2%
- lỗi hệ thống: 'thua' chiếm 4/7 lỗi

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
