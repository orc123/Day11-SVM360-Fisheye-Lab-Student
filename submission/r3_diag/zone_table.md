# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 9 | 1 | 1 | 3 | 4 | ATTRIBUTE (2) |
| mid | 7 | 4 | 2 | 3 | 4 | MISSING (3) |
| edge | 4 | 2 | 1 | 4 | 3 | BOX_GEOMETRY (1) |

## Nhận xét

- Zone nào người (L) và model (M) gãy nhiều nhất, dẫn số ở bảng trên: **Người (L) gãy nhiều nhất ở `mid`**: thiếu 4/7 box reference và thừa 2, lỗi chính là MISSING (3). **Model (M) gãy nhiều nhất ở `edge`**: thiếu 4/4 box reference (không khớp box nào) và thừa 3. `center` ổn nhất cho cả hai phía: L chỉ thiếu 1/9, lỗi chính là ATTRIBUTE (2 box tick nhầm `truncated`), không có lỗi vị trí hay class.
- Giả thuyết vì sao (méo fisheye, box lỏng, thiếu `ego_body`, ...) và giới hạn của slice ba frame: (1) Phần lớn lỗi `mid` của L **không do méo fisheye** mà do mật độ và che khuất ở 199770: tôi gộp 2 xe máy đậu sát làm 1 box, bỏ sót người đứng trong quán tối, và gán nhầm một xe tải nhỏ ở xa (~48 px) thành ThreeWheeler vì đoán theo bối cảnh thay vì phóng to kiểm hình dáng. (2) Hai MISSING còn lại (167700 R2 và 199770 R4) **không hẳn là lỗi người**: box của tôi bị loại khỏi phạm vi vì teaching reference đặt polygon `ego_body` sai bên (phủ người quàng khăn và xe ba bánh mép phải, trong khi chính reference có box R4 cho xe đó). Đây là lỗi `E0_reference_defect` đã escalate, nên số `mid`/`edge` của 2 frame này chưa đáng tin. (3) Ở `edge`, model gãy vì hai lỗi hệ thống: gọi xe ba bánh là Truck/Car (4/4 xe ba bánh gần) và nhận thân người ego là Pedestrian. Méo ở rìa vòng kính có thể góp phần, nhưng slice không có ca đối chứng để tách riêng yếu tố này. **Giới hạn:** chỉ 3 frame với 20 box reference, một camera, và reference đã bị phát hiện lỗi. Zone `center/mid/edge` chỉ là bán kính trên ảnh, không cho biết vật gần hay xa, nên không suy ra được rủi ro an toàn từ bảng này.
