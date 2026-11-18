# Báo cáo Day 08 — <2A202603226 - Lương Văn Quyến>

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | <<YOLO26m>> | <<theo lớp, từ threshold_sweep>> | <<>> |
| three_wheeler | <<YOLOE-26s, prompt: ...>> | <<>> | <<>> |

- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): <<>>
- **Phiên bản SAM bạn chọn** (SAM 1 / 2.1 / 3) và lý do: <<chất lượng mask, tốc độ, RAM, quyền truy cập>>
- Lỗi nào **không sửa được bằng đổi ngưỡng** (ví dụ tuk-tuk bị gọi `car`)? Sửa thế nào? <<>>
- Box **điểm cao mà sai** hoặc **điểm thấp mà đúng** (ảnh nào): <<>>
- Giới hạn: chọn ngưỡng trên chính gold (4 ảnh) nên số đo máy trên gold là lạc quan: <<>>

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 441 | 84,2% | 524 box máy |
| edited | 21 | 4,0% | 524 box máy |
| deleted | 52 | 9,9% | 524 box máy |
| added | 24 | 4,9% | 486 đối tượng sau duyệt |

- Nguồn: submission/qc/review_stats.json.
- So với gold: precision = 54/68 = 79,4%; recall = 54/71 = 76,1%, trên 4 ảnh gold.

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Bắt bẫy: lượt đầu 7/12 = 58,3%; sau khi sửa đạt 11/12 = 91,7% (nguồn: `qc/review_stats.json`). Còn lọt `img_023.jpg`, loại `loose`. Nguyên nhân cụ thể và bước checklist đã bỏ qua: .
- Accepted cao mà bắt bẫy thấp nói gì về cách bạn duyệt? <<>>

## 4. Máy có đáng dùng không?

- Thời gian (từ `time_log.csv`): gán tay 30/4 = 7,5 phút/ảnh; duyệt có pre-label 30/24 = 1,25 phút/ảnh.Việc gán gold trước cũng giúp tôi quen cảnh, có thể khiến lượt review nhanh hơn.
- Kết luận: [có ], dựa trên [khoảng 1h30 ].
- **Ba quy tắc giảm thiên kiến của bạn** (chỉ giữ nếu bạn sẽ áp dụng):
  1. Ẩn toàn bộ nhãn và quét hết ảnh trước khi hiện box máy.
  2. Kiểm từng ảnh theo thứ tự: bỏ sót, box thừa, sai lớp, box không sát; không bỏ qua box có confidence cao.
  3. Ghi riêng thời gian review và sửa bổ sung; kiểm tra kết quả sau mỗi lần export thay vì chỉ dựa vào tỉ lệ accepted.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| <<img_0xx>> | <<>> | <<>> | <<>> | <<>> | <<>> |

- Đối chiếu guideline mục 4.2: quy tắc nào giúp, quy tắc nào thiếu? <<>>
- *(Tuỳ chọn)* Interactor SAM trong CVAT: làm được / không (lỗi gì, đã thử gì); khác gì offline: <<>>

## 6. Calibration (Part 6)

- Số quyết định: <<>> (`calibration_decisions.csv`); quan trọng nhất (dẫn `CAL-xx`): <<>>
- Quy tắc mới đã vào `guideline_v1.1_calibrated.md`: <<>>
- Nguyên nhân gốc: <<guideline mơ hồ / chưa nắm quy tắc / máy>>

## 7. QC và quyết định (Part 6)

- Mẫu: <<n>> ảnh (<<m>> ảnh `can_review` + <<k>> ngẫu nhiên, seed 42); người kiểm: bạn (**hạn chế**: bạn vừa là người duyệt).
- Lỗi theo **đối tượng**: <<.../... = ...%>> · theo **ảnh**: <<.../... = ...%>> (ngưỡng Release ≤ 2%)
- Top lỗi (trong: bỏ sót, thừa, sai lớp, sai vị trí/kích thước, sai thuộc tính, sai mask) + một ảnh ví dụ mỗi loại: <<>>
- **Giới hạn mẫu nhỏ** (cận trên 95% trong `qc_report_auto.md`): <<>>
- Script kiểm được / không kiểm được (ví dụ ôm sát, cùng bỏ sót với máy, theo checklist thật): <<>>

**Quyết định: <<Release / Hold / Rework>>**, ngày <<YYYY-MM-DD>>. Lý do (đi theo Hold → Rework → Release): <<>>
Nếu Rework / Hold, việc cần làm tiếp: <<>>

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài** (công cụ nào, việc gì, kiểm tra thế nào; "không dùng" nếu không): <<>>
- Gold có bị nhiễm không (bạn có nhìn pre-label hay hỏi AI trước khi gán xong)? <<không / có, như sau: ...>>
- Máy / GPU / CVAT dùng; vướng mắc cài đặt: <<>>
- Dùng cache `data/cache/` thay vì tự chạy? <<không / có, vì ...>>
- Lỗi / bug script đã gặp (nếu có): <<>>
- Một điều bạn làm khác lần sau: <<>>
