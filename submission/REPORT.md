# Báo cáo Day 08 — 2A202603226 - Lương Văn Quyến

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao |
|---|---|---|---|
| person / motorbike / car | YOLO26m, r05 | 0.3 | Quét ngưỡng chưa đạt recall mục tiêu 90%, giữ ngưỡng mặc định |
| bicycle | YOLO26m, r05 | 0.2 | Recall 100%, precision 83% trên 5 đối tượng gold |
| bus / truck | YOLO26m, r05 | 0.3 | Gold chỉ có 1 bus và 0 truck, chưa đủ mẫu chọn ngưỡng riêng |
| three_wheeler | YOLOE-26s-seg, r06 | 0.15 | Recall 100%, precision 78% trên 7 đối tượng gold |

- Prompt cho three_wheeler: `tuk-tuk`, `cyclo`, `cargo tricycle`, lấy từ taxonomy.json.
- Lệnh gộp dưới đây được dựng lại từ cấu hình và đã đối chiếu khớp kết quả merged đã lưu:

```powershell
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "bicycle=0.2,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
```

- SAM sử dụng: SAM 2.1 base, `sam2.1_b.pt`. Lượt r04 chạy trên CPU, trung bình 3.96 giây/ảnh. Phiên bản này chạy được trong môi trường thực hành; chưa có phép so sánh để kết luận tốt hơn các phiên bản khác.
- Lỗi không sửa được bằng đổi ngưỡng: YOLO26 với ánh xạ COCO hiện tại không có three_wheeler. Tôi dùng YOLOE để tìm lớp này và ưu tiên kết quả YOLOE khi gộp.
- Ví dụ điểm thấp nhưng khớp gold: img_012.jpg có box three_wheeler của YOLOE với confidence khoảng 0.194, IoU khoảng 0.822 với box cùng lớp trong gold.
- Giới hạn: chọn và đánh giá ngưỡng trên cùng 4 ảnh gold nên số đo có tính lạc quan. Tour YOLO26 dùng cỡ s, còn nguồn pre-label toàn bộ ảnh dùng cỡ m.

## 2. AI error profile

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 441 | 84,2% | 524 box máy |
| edited | 21 | 4,0% | 524 box máy |
| deleted | 52 | 9,9% | 524 box máy |
| added | 24 | 4,9% | 486 đối tượng sau duyệt |

- Nguồn: `qc/review_stats.json`. Các đối tượng quanh bẫy được script loại khỏi bảng đánh giá chất lượng máy.
- Các số trên phản ánh lượt so sánh batch_reviewed với pre-label đã lưu; không phải số đo lại trên bộ release sau sửa QC.
- So với gold: precision 54/68 = 79,4%; recall 54/71 = 76,1%, trên 4 ảnh gold với 71 đối tượng được tính.
- Three_wheeler có 21 box bị xóa và 7 box chỉnh sửa, cần chú ý box thừa và vị trí box. Chưa đủ bằng chứng để kết luận nguyên nhân của từng box bị xóa.

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Lượt đầu bắt được 7/12 = 58,3%; sau sửa đạt 11/12 = 91,7%. File review_stats còn ghi img_023.jpg, loại loose, là bẫy lọt.
- Lượt đầu accepted đạt 88,0% nhưng chỉ bắt được 7/12 bẫy. Việc giữ lại nhiều box không đồng nghĩa đã phát hiện đủ lỗi.
- Trong lượt sửa, tôi đã xem danh sách bẫy lọt và được ChatGPT hỗ trợ đọc code, xác định vùng tham chiếu. Kết quả lượt sửa không phải phép đánh giá mù độc lập.
- Chưa có nhật ký thao tác đủ chi tiết để xác định chắc chắn bước checklist bị bỏ qua ở lượt đầu.

## 4. Máy có đáng dùng không?

- Theo time_log.csv: gán tay 30/4 = 7,5 phút/ảnh; review ban đầu 30/24 = 1,25 phút/ảnh. Chưa tính đủ thời gian sửa bổ sung, nên chưa xác định được mức tiết kiệm thời gian cuối cùng. Việc gán gold trước cũng giúp tôi quen cảnh.
- Kết luận: AI giúp giảm công sức tạo nhãn ban đầu vì tôi giữ lại được nhiều box. Tuy nhiên vẫn phải kiểm tra kỹ từng ảnh; lượt đầu chỉ bắt được 7/12 bẫy.
- Ba quy tắc giảm thiên kiến tôi sẽ áp dụng:
  1. Ẩn nhãn và quét toàn bộ ảnh trước khi xem box máy.
  2. Kiểm bỏ sót, thừa, sai lớp và box không sát; không bỏ qua box có confidence cao.
  3. Ghi riêng thời gian review và sửa bổ sung, kiểm kết quả sau mỗi lần export.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM | Số click | Mask gồm người lái / hàng hóa? | Lỗi quan sát | File |
|---|---|---|---|---|---|
| img_013 – tuk-tuk | SAM 2.1 base | 2 dương + 1 âm, có box | Loại được phần lớn người lái; không thử riêng hàng hóa | Lấy thừa bánh xe máy bên phải và còn dính một phần người lái | masks/tuktuk_img013_v2.jpg |
| img_013 – người lái | SAM 2.1 base | 2 dương + 1 âm, có box | Người lái riêng | Biên tay, dép và vùng bị che chưa thật sát | masks/driver_img013.jpg |
| img_013 – người áo sọc | SAM 2.1 base | 2 dương + 1 âm, có box | Người áo sọc riêng | Thiếu một mảng nhỏ trên áo gần vai | masks/passenger_img013.jpg |

- Ba vật được thử trên cùng ảnh. Số click là số điểm trong từng lệnh tạo kết quả được chọn, không phải tổng số điểm của mọi lần thử.
- Với tuk-tuk, v1 chỉ chọn logo. V2 dùng thêm box và điểm trên mái, thân xe nên chọn được phần lớn xe. V3 thêm điểm âm nhưng lấy thừa nhiều phần người lái, vì vậy giữ v2 làm kết quả chính.
- Theo guideline mục 4.2, mask cần bám phần nhìn thấy và tách riêng vật. SAM còn lỗi ở vùng bị che và các vật sát nhau, cần kiểm tra thủ công.
- Phần thử nghiệm dùng sam2_points.py offline; chưa thử Interactor SAM trong CVAT.

## 6. Calibration (Part 6)

- Có 3 quyết định trong `qc/calibration_decisions.csv`: CAL-01 từ loi_may, CAL-02 từ cau_hoi_mo và CAL-03 từ bay_lot.
- Quyết định quan trọng nhất là CAL-02: làm rõ kính chắn gió và khoảng hở trong ảnh img_013.jpg.
- Quy tắc đã bổ sung vào guideline v1.1: kính thuộc mask xe; khoảng hở không có kính không thuộc mask xe. Người được gán riêng theo phần nhìn thấy và quy tắc phân chia vùng chạm nhau.
- CAL-01 là lỗi SAM lấy thừa bánh xe máy bên cạnh; quy tắc tách vật đã có nên cần sửa mask, không đổi guideline.
- CAL-03 yêu cầu kiểm box xe nhỏ sát mép ảnh theo quy tắc hiện có.
- Nguyên nhân được phân biệt giữa quy tắc cần làm rõ và lỗi thực hiện; không đổi guideline để hợp thức hóa lỗi.

## 7. QC và quyết định (Part 6)

- Vòng QC đầu ghi nhận 7/190 đối tượng lỗi và 5/8 ảnh lỗi, dẫn đến quyết định Rework.
- Các lỗi vòng đầu gồm: thừa 4 lỗi tại img_010.jpg và img_024.jpg; sai vị trí/kích thước 1 lỗi tại img_003.jpg; bỏ sót 1 lỗi tại img_017.jpg; sai lớp 1 lỗi tại img_018.jpg.
- Sau sửa, mẫu mới gồm 6 ảnh: 1 ảnh can_review và 5 ảnh ngẫu nhiên, seed 42; tổng cộng 94 đối tượng.
- Người kiểm: Luong Van Quyen, có ChatGPT hỗ trợ xem ảnh và soạn phiếu. Hạn chế: tôi vừa là người duyệt vừa là người kiểm.
- Vòng sau: lỗi theo đối tượng 0/94 = 0%; ảnh có lỗi 0/6 = 0%. Phiếu không ghi nhận loại lỗi.
- Mẫu hai vòng khác nhau nên không dùng chênh lệch phần trăm để khẳng định mức cải thiện của toàn bộ lô.
- Cận trên 95% Wilson của tỉ lệ ảnh lỗi vòng sau khoảng 39%. Mẫu nhỏ chưa chứng minh toàn bộ lô có tỉ lệ ảnh lỗi dưới 2%.
- Bắt bẫy sau sửa đạt 11/12 = 91,7%; lượt này đã biết vị trí bẫy nên không phải đánh giá mù độc lập.
- Script tính số liệu theo phiếu, kiểm định dạng và số lượng; không tự xác nhận box sát vật, lớp đúng thực tế hoặc phát hiện mọi vật bỏ sót.
- Nguồn: `qc/qc_report_auto.md`, `qc/qc_review.csv` và `qc/review_stats.json`.

**Quyết định: Release**, ngày 2026-11-18.

Lý do: sau sửa và kiểm lại, mẫu QC không ghi nhận lỗi; không còn vấn đề guideline chưa chốt. Quyết định giới hạn theo quy trình và mẫu kiểm của bài, không khẳng định toàn bộ lô hoàn toàn không có lỗi.

## 8. Ghi chú cá nhân

- Dùng AI: ChatGPT hỗ trợ giải thích quy trình, đọc code và JSON, xem mask, gợi ý lỗi QC và soạn báo cáo. Tôi đối chiếu nhận xét với ảnh và đầu ra script, xác nhận quyết định cuối.
- Tình trạng gold trước khi dùng AI: chưa được xác nhận trong nhật ký hiện có; tôi cần bổ sung đúng việc có xem pre-label hoặc hỏi AI trước khi gán xong gold hay không.
- Môi trường: Windows, CVAT Docker 2.81.0. Các log model đã lưu ghi device cpu.
- Sử dụng cache: repo có cache và log model, nhưng chưa ghi rõ có dùng cache làm nguồn kết quả hay không; tôi cần bổ sung theo quá trình thực tế.
- Vướng mắc: cần chọn đúng đối tượng và export lại sau sửa. Phiếu QC trống từng tạo số 0 lỗi dù các ảnh chưa được kiểm.
- Lần sau: ghi đủ thời gian các lượt sửa, lưu nhận xét ngay sau mỗi lượt thử, kiểm ảnh trước khi tin vào confidence hoặc gợi ý quyết định của script.