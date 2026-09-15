# Mini annotation guideline — Ngày 3 (tracking)

> Tài liệu quy chuẩn gán nhãn dữ liệu Video Tracking (Multi-Object Tracking - MOT).
> Định hướng người gán nhãn xử lý nhất quán các tình huống giao nhau, che khuất, rời khung hình và thiết lập keyframe.

Nhóm / tên: `Âu Xuân Mạnh - 2A202602121 (K4-L2 AI In Action)`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm: `Chỉ gán xe bốn bánh thật đang lưu thông hoặc đỗ trên đường. Không gán hình vẽ xe trên biển quảng cáo.`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | Giữ nguyên ID nếu thời gian che **dưới 25 frame** (2 giây @ 12.5 fps) | Bảo toàn identity khi xe bị chướng ngại vật/cây/xe khác che tạm thời |
| Xe bị che lâu hơn ngưỡng trên | Tạo track ID mới khi xuất hiện lại | Quá 25 frame khả năng mất dấu identity cao, tránh gán nhầm xe |
| Xe rời khung hình rồi quay lại | Mặc định gán: **track ID mới** | Đảm bảo tính nhất quán theo thời gian của từng hành trình |
| Hai xe cắt nhau / chồng lên nhau | Duy trì track ID của xe phía trước và xe phía sau theo đúng bounding box nhìn thấy được | Tránh bị nhảy ID (ID switch) khi hai xe crossing |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa ảnh, không đoán/vẽ phần ngoài phạm vi khung hình |
| Xe bị xe khác che một phần | bbox chỉ ôm phần **nhìn thấy được** (visible part), bật thuộc tính `Occluded` |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | Bắt đầu track từ frame đầu tiên xác định rõ là xe bốn bánh (ngưỡng tối thiểu 10x10 px) |
| Xe đang đỗ, không di chuyển | Giữ nguyên bbox qua các keyframe, chỉ điều chỉnh khi góc quay/zoom của camera thay đổi |
| Keyframe đặt dày ở đâu | Đặt dày ở các đoạn xe thay đổi vận tốc, chuyển hướng, hoặc cắt nhau (mỗi 3-5 frame) |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01` / frame 62-78 / ID 5
- Tình huống: Xe vừa xuất hiện ở ranh giới rìa ảnh, kích thước nhỏ và bị che lấp một phần bởi rìa.
- Quyết định: Đặt keyframe đầu tiên ngay tại frame 62 khi phần kim loại xe nhìn thấy được nhú qua mép ảnh; thu gọn bbox ôm sát phần nhìn thấy.
- Lý do: Đảm bảo độ chính xác IoU và tránh tạo bbox ảo ngoài vùng hiển thị của ảnh.

### Ca 2
- Clip / frame / ID: `clip_01` / frame 79-100 / ID 6
- Tình huống: Xe 6 di chuyển cắt qua phía sau xe khác đang dừng đỗ.
- Quyết định: Duy trì duy nhất `track_id` 6 xuyên suốt, bật thuộc tính `Occluded` ở các frame bị che lấp.
- Lý do: Thời gian che lấp ngắn (22 frames, dưới 2 giây), quỹ đạo xe di chuyển thẳng không đổi hướng.

### Ca 3
- Clip / frame / ID: `clip_01` / frame 133-135 / ID 8
- Tình huống: Xe 8 đi ra khỏi mép dưới khung hình.
- Quyết định: Bật thuộc tính `Outside` ngay tại frame 136 khi xe hoàn toàn khuất khỏi khung hình.
- Lý do: Tránh để lại "bbox treo" kéo dài ở các frame sau gây ra lỗi gán thừa False Positive (FP).

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- `Cần bật thuộc tính Outside chính xác tại frame kế tiếp ngay khi xe vừa khuất hẳn 100% để tránh lỗi FP bbox treo.`
- `Tăng mật độ keyframe (khoảng 3-5 frame / keyframe) tại các mốc xe di chuyển thay đổi vận tốc để tránh hiện tượng trôi bbox (interpolation drift) giữa 2 keyframe.`
