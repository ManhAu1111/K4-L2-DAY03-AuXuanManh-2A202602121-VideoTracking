# Báo cáo Ngày 3 — Tracking Annotation

Họ tên học viên: `Âu Xuân Mạnh - 2A202602121`
Ngày: `2026-09-15`


---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT Community 2.74.1 |
| Thời gian gán `clip_02` (warm-up) | 25 phút |
| Thời gian gán `clip_01` | 85 phút |
| Số track đã vẽ trong `clip_01` | 8 |
| Số keyframe trung bình mỗi track | 6 |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. Xe nhú ra từ ranh giới ảnh: Đặt keyframe đầu tiên ngay khi phần kim loại xe nhìn thấy được nhú qua mép ảnh.
2. Xe di chuyển bị xe khác/chướng ngại vật che khuất: Giữ nguyên `track_id` duy nhất và bật thuộc tính `Occluded`.
3. Xe rời khung hình: Bật thuộc tính `Outside` ngay ở frame liền kề tiếp theo để tránh trôi bbox/bbox treo.

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: Đảm bảo cả 8 chiếc xe giữ nguyên `track_id` từ đầu đến cuối hành trình.
- Lượt 2: Đảm bảo điểm bắt đầu và điểm kết thúc (Outside) khớp đúng frame xuất hiện và biến mất.
- Lượt 3: Đảm bảo các frame nội suy ở giữa keyframe không bị trôi bbox (interpolation drift).

Kiểm chéo với: `Peer Reviewer`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `3`. Số lỗi bạn ấy tìm được trong bản của bạn: `3`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`Ca xe vừa đi ra khỏi rìa ảnh: Cần làm rõ thời điểm chính xác bấm Outside (ngay khi vừa khuất 100%).`

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `43b4ad9eb8c7477145df8b20c36f3e74d173c188b0777131dce44951cf958439` |
| Thời điểm khóa | `2026-09-15T09:43:26Z` |
| Số row / frame / track trước khi mở reference | `632 / 190 / 8` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.784 | 0.768 | 0.802 | 0.875 | 0.936 | 0.866 | 0.865 | 68 | 9 | 0 |
| Sau rework | 0.784 | 0.768 | 0.802 | 0.875 | 0.936 | 0.866 | 0.865 | 68 | 9 | 0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| FP (bbox thừa) | 79-100 | 6 | Bật Outside đúng frame xe hoàn toàn khuất |
| FP (bbox thừa) | 62-78 | 5 | Chỉnh điểm bắt đầu keyframe khít hơn |
| Bbox trôi | 83 | 5 | Thêm keyframe bổ sung tại frame 83 để tăng IoU |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `Python 3.13 / ultralytics 8.4.145 / PyTorch 2.11 / lap 0.5.13` |
| weights / hai tracker | `yolo26n.pt / bytetrack.yaml vs botsort-reid.yaml` |
| conf / IoU / imgsz / classes | `0.25 / 0.70 / 960 / 2,5,7` |
| device | `0 (T4 GPU)` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.784 | 0.768 | 0.802 | 0.875 | 0.936 | 0.866 | 0.865 | 68 | 9 | 0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 | 2 |
| ReID vs bạn | 0.767 | 0.712 | 0.826 | 0.913 | 0.872 | 0.748 | 0.907 | 81 | 75 | 3 |

### Thí nghiệm mở rộng (Stretch Experiment - ReID Threshold)
- `appearance_thresh = 0.70`: HOTA 0.763, IDF1 0.900, FP 91, FN 26, IDSW 2
- `appearance_thresh = 0.80`: HOTA 0.763, IDF1 0.900, FP 91, FN 26, IDSW 2
- `appearance_thresh = 0.90`: HOTA 0.763, IDF1 0.899, FP 91, FN 27, IDSW 2
*Nhận xét:* Ngưỡng 0.70 và 0.80 mang lại hiệu năng tối ưu và ổn định nhất. Khi siết ngưỡng quá cao (`0.90`), yêu cầu về độ tương đồng đặc trưng ngoại hình giữa các frame tăng lên, dẫn đến việc mô hình bỏ sót thêm 1 vị trí xe bị đổi góc quay/ánh sáng (FN tăng từ 26 lên 27, IDF1 giảm nhẹ từ 0.900 xuống 0.899).



## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

IDF1 đạt 0.936 cao hơn MOTA (0.866). Điều này cho thấy khả năng duy trì identity qua thời gian rất tốt. Trong trường hợp MOTA cao mà IDF1 thấp, điều đó cho biết model/người gán nhãn tìm thấy vị trí vật thể tốt (ít FP/FN) nhưng bị nhảy ID liên tục (ID switches). MOTA không phạt nặng lỗi ID vì công thức MOTA tính trọng số IDSW rất nhẹ so với tổng số FP + FN.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

BoT-SORT + ReID cho IDF1 (0.860 vs 0.825), AssA (0.768 vs 0.730) cao hơn và giảm IDSW (1 vs 3) so với ByteTrack. Tại đoạn frame 79-100 (khi ID 6 bị xe khác che khuất), BoT-SORT dùng thêm đặc trưng ngoại hình (appearance embeddings) nên kết nối lại đúng ID sau khi xuất hiện lại, trong khi ByteTrack phụ thuộc chuyển động dễ tạo ID mới. Tuy nhiên đây không cô lập hoàn toàn hiệu ứng nguyên nhân của ReID vì hai tracker sử dụng thuật toán gộp và lọc khác nhau.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

DetA giữ mức 0.768 với FP=68 và FN=9. Lỗi còn lại chủ yếu đến từ detector khi xe bị che lấp quá nửa hoặc bị khuất bóng, dẫn đến việc bỏ sót hoặc phát hiện trễ.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Tại frame 133-135 (ID 8 khi chuẩn bị đi ra khỏi mép dưới ảnh), ReID vẫn cố gắn ID cho một phần bóng/vật thể mờ ở rìa ảnh gây ra FP, trong khi người gán nhãn bật Outside đúng thời điểm xe khuất hẳn.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

Tại frame 83 (ID 5), ReID phát hiện bounding box ôm khít hơn ở các frame giữa 2 keyframe của người gán nhãn, nhắc nhở cần thêm keyframe dày hơn tại đoạn này để tránh trôi bbox.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

- Thêm quy định rõ ràng về mốc pixel tối thiểu khi xe xuất hiện từ rìa ảnh.
- Tăng mật độ keyframe ở những khoảng xe di chuyển không đều (3-5 frame / keyframe).
- Kiểm tra lại toàn bộ endpoint (nút Outside) ở lượt tua 2 trước khi export.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)
