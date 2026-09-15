# Peer review — Day 3

| Trường | Giá trị |
| --- | --- |
| Author | `aumanh` |
| Reviewer | `Peer Reviewer` |
| Pair ID | `PAIR-01` |
| CVAT version | `2.74.1` |
| Thời điểm review | `2026-09-15` |

## Danh sách finding

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | 61 | 62 | 5 | Bbox nhú ra từ rìa | Xe vừa xuất hiện ở rìa ảnh, bbox còn hơi rộng so với phần nhìn thấy | Thu hẹp bbox ôm sát phần kim loại xe nhìn thấy được | fixed |
| 2 | 78 | 79 | 6 | Che khuất (Occlusion) | Xe 6 đi phía sau xe đỗ, cần giữ nguyên ID | Đã xác nhận ID 6 giữ nguyên và bật Occluded | fixed |
| 3 | 134 | 135 | 8 | Endpoint / Outside | Xe 8 đi ra khỏi mép dưới khung hình | Bật Outside ngay tại frame 136 | fixed |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | PASS | Đã có 8 tracks xe bốn bánh hợp lệ |
| Một xe giữ một ID; không reuse ID cho xe khác | PASS | ID 1 đến ID 8 duy nhất |
| Occlusion ngắn giữ ID; crossing không đổi ID | PASS | Frame 79-100 ID 6 giữ nguyên ID |
| Entry/exit đúng; không box treo sau khi xe rời khung | PASS | Frame 135 ID 8 dùng Outside đúng lúc |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | PASS | Tuân thủ rule ôm phần nhìn thấy |
| Frame giữa hai keyframe không bị interpolation drift | PASS | Đã kiểm tra keyframe khoảng cách 3-5 frame |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS | Cột 1 bắt đầu từ 1, cột 2 là ID |
| Mọi finding có cách sửa và closure do tác giả điền | PASS | Cả 3 finding đều có closure fixed |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | PASS | Tracks 1-8 giữ ID nhất quán |
| 2 — endpoint/scope | PASS | Bật Outside đúng frame xuất/nhập |
| 3 — geometry/interpolation | PASS | Bbox ôm khít phần nhìn thấy được |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: `Cần bật Outside ngay frame tiếp theo khi xe rời khung hình để tránh lỗi False Positive.`
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): `N/A (Tất cả finding đều đã fixed)`
3. Một rule cần Lab Coach làm rõ (nếu có): `Không có`
