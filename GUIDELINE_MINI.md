# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `AI In Action - Lab Day 3`
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

Bổ sung của nhóm (nếu có): `Chỉ gán xe bốn bánh đang lưu thông hoặc đỗ trên đường.`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | Bảo toàn identity khi xe bị chướng ngại vật/cây/xe khác che tạm thời |
| Xe bị che lâu hơn ngưỡng trên | Tạo track ID mới khi xuất hiện lại | Quá 25 frame khả năng mất dấu identity cao |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | Đảm bảo tính nhất quán theo thời gian của từng hành trình |
| Hai xe cắt nhau / chồng lên nhau | Duy trì track ID của xe phía trước và phía sau theo đúng bounding box nhìn thấy được | Tránh bị nhảy ID (ID switch) khi crossing |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: `kích thước tối thiểu 10x10 px` |
| Xe đang đỗ, không di chuyển | Giữ nguyên bbox qua các keyframe, chỉ điều chỉnh khi góc quay/zoom thay đổi |
| Keyframe đặt dày ở đâu | Đặt dày ở các đoạn xe thay đổi vận tốc, đổi hướng, hoặc cắt nhau (mỗi 3-5 frame) |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01` / frame 62-78 / ID 5
- Tình huống: Xe vừa xuất hiện ở ranh giới rìa ảnh, kích thước nhỏ và bị che một phần.
- Quyết định: Đặt keyframe ngay khi xe nhú ra khỏi rìa và gán nhãn phần nhìn thấy được, dùng Outside khi khuất hẳn.
- Lý do: Đảm bảo độ chính xác IoU và không gán thừa box ngoài phạm vi ảnh.

### Ca 2
- Clip / frame / ID: `clip_01` / frame 79-100 / ID 6
- Tình huống: Xe di chuyển cắt qua phía sau xe khác đang đỗ.
- Quyết định: Giữ nguyên `track_id` 6 xuyên suốt, bật thuộc tính `Occluded` ở các frame bị che lấp.
- Lý do: Thời gian che lấp nhỏ hơn 25 frames (dưới 2 giây), xe không đổi hướng.

### Ca 3
- Clip / frame / ID: `clip_01` / frame 133-135 / ID 8
- Tình huống: Xe đi ra khỏi rìa ảnh phía dưới.
- Quyết định: Bật tính năng `Outside` ngay tại frame 136 khi xe hoàn toàn ra khỏi khung hình.
- Lý do: Tránh để lại "bbox treo" kéo dài gây ra lỗi False Positive (FP) ở các frame sau.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- `Cần bật Outside chính xác ở frame kế tiếp ngay khi xe vừa ra khỏi khung hình để tránh lỗi FP.`
- `Thêm keyframe dày hơn (khoảng 3-5 frame) tại các đoạn xe tăng/giảm tốc để tránh trôi bbox giữa 2 keyframe.`

