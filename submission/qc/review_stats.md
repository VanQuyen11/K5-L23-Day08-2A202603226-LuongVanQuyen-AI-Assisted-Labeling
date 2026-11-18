# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 524; đối tượng sau duyệt: 486; ngưỡng accepted: IoU >= 0.9
- Đã **loại 18 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 441 | 441/524 = 84.2% | 524 box máy |
| edited (sửa) | 21 | 21/524 = 4.0% | 524 box máy |
| deleted (xoá) | 52 | 52/524 = 9.9% | 524 box máy |
| added (vẽ thêm) | 24 | 24/486 = 4.9% | 486 đối tượng sau duyệt |

Trong số deleted + added, **7 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 17.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 248 | 8 | 14 | 7 |
| bicycle | 8 | 1 | 0 | 1 |
| motorbike | 123 | 5 | 6 | 9 |
| car | 47 | 0 | 6 | 2 |
| bus | 2 | 0 | 1 | 0 |
| truck | 2 | 0 | 4 | 2 |
| three_wheeler | 11 | 7 | 21 | 3 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 71 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 54 | 14 | 17 | 79.4% (KTC 95%: 68–87%) | 76.1% (KTC 95%: 65–84%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 11/12 = 91.7% (KTC 95%: 65–99%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - missing: 3/3
  - wrong_class: 3/3
  - loose: 2/3
  - extra: 3/3
  - lọt: img_023.jpg (loose)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
