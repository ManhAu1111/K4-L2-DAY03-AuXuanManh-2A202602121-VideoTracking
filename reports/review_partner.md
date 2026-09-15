# Peer review — Day 3

| Trường | Giá trị |
| --- | --- |
| Author | `Âu Xuân Mạnh (2A202602121)` |
| Reviewer | `Peer Reviewer` |
| Pair ID | `PAIR-01` |
| CVAT version | `2.74.1` |
| Thời điểm review | `2026-09-15` |

## Danh sách finding

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | 130 | 131 | 5 | Bbox nhú ra từ rìa | Xe 5 vừa xuất hiện ở rìa ảnh từ frame 131 đến 171, cần ôm sát phần kim loại nhìn thấy được | Thu hẹp bbox ôm sát phần kim loại xe nhìn thấy được từ frame 131 | fixed |
| 2 | 184 | 185 | 6, 2 | Che khuất (Occlusion) | Xe 6 che lấp xe 2 từ frame 185 đến 189, cần giữ nguyên ID cho cả 2 xe và bật Occluded | Đã xác nhận ID 6 và ID 2 giữ nguyên, bật thuộc tính Occluded cho xe bị che | fixed |
| 3 | 156 | 157 | 8 | Endpoint / Outside | Xe 8 biến mất ở frame 157 khi ra khỏi mép dưới khung hình | Bật Outside ngay tại frame 158 khi xe khuất hẳn | fixed |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | PASS | Đã có 8 tracks xe bốn bánh hợp lệ |
| Một xe giữ một ID; không reuse ID cho xe khác | PASS | ID 1 đến ID 8 duy nhất |
| Occlusion ngắn giữ ID; crossing không đổi ID | PASS | Frame 185-189 xe 6 che xe 2 giữ nguyên ID |
| Entry/exit đúng; không box treo sau khi xe rời khung | PASS | Frame 157 ID 8 dùng Outside đúng lúc (frame 158) |
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

1. Finding quan trọng nhất và rule dùng để kết luận: `Cần bật Outside ngay frame 158 khi xe 8 biến mất tại frame 157 để tránh lỗi False Positive.`
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): `N/A (Tất cả finding đều đã fixed)`
3. Một rule cần Lab Coach làm rõ (nếu có): `Không có`
