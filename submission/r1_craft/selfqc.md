# Tự soát

- adasind_167700.jpg L6: truncated khác dự kiến
- adasind_167700.jpg L8: truncated khác dự kiến
- adasind_167700.jpg L10: truncated khác dự kiến

Xử lý cảnh báo tự động: **chưa sửa, giữ lại có chủ đích** (quyết định D02 trong `40_decision_log.csv`, do hết ngân sách thời gian P2). Nguyên nhân đã xác định trên ảnh:
- L6 (Bike người quàng khăn, 756–1080): box kéo tới mép phải ảnh trong khi xe đạp chỉ lộ tới x≈980, phần sau bị chân người che → nên thu cạnh phải và giữ `occluded`.
- L8 (Car – xe van, 572–698) và L10 (Truck xám, 551–623): tick nhầm `truncated`; hai xe nằm gọn trong ảnh, chỉ bị che (`occluded`).

K12 bỏ qua theo `degrade k12`.

## Checklist thủ công
- [x] Phạm vi H=40 và vật cần vẽ — đã soát 3 frame. **Còn thiếu:** 199770 xe ba bánh mui trắng thứ 2 ở xa (x≈278–330, y≈802–860, cao ~58 px). Không box người đi bộ xa giữa đường 199770 (~25 px < H) và dáng người nhỏ cạnh ô tô trắng 145860 (~32 px < H).
- [x] lens_border và ego_body — 2 `lens_border` import còn nguyên ở cả 3 frame. `ego_body` đúng vị trí (góc dưới-trái) nhưng **chưa phủ hết**: 145860 thiếu bàn chân/dép ở đáy vòng kính (x≈255–345, y≈1740–1795); 167700 chỉ phủ tay áo, thiếu thân tối màu phía dưới tới vành kính (x 0–270, y≈1440–1680) và túi/tay nắm góc trên-trái.
- [x] Class sáu nhãn — xe ba bánh đều là ThreeWheeler, xe van chở người là Car, xe tải xa là Truck. Không có lỗi.
- [x] Rider và Bike — 167700: ba người dắt xe tách Pedestrian + Bike; 199770: người áo trắng đứng cạnh xe máy hồng (hai chân chạm đất, không ngồi yên) → Pedestrian + Bike. Không có người ngồi lái trong slice.
- [x] Geometry trên ảnh fisheye gốc — box bám phần thấy trên ảnh gốc; ngoại lệ L6 167700 (xem trên).
- [x] truncated và occluded — đúng cho người quàng khăn 167700 và hai ThreeWheeler mép trái/phải 199770. **Sai:** L6, L8, L10 frame 167700 (xem cảnh báo tự động).
- [x] Vật thiếu hoặc box trùng — không có box trùng. Thiếu: xe ba bánh thứ 2 ở xa 199770 (xem mục 1). Ca mơ hồ: cẳng chân + dép ở mép phải dưới 199770 (x≈950–1060, y≈1300–1560) của người gần như ngoài khung — **không box**, vì chỉ thấy một phần chân, chưa đủ để nhận diện đối tượng; ghi để QA/reference đối chiếu.
- [x] ignore_region có reason — mọi polygon có đúng một `reason` (`lens_border` hoặc `ego_body`); không box nào nằm trong vùng ignore.
- [x] Tên task raw_fisheye và export CVAT 1.1 — task `Day11 · ADASIND · B3-dense · raw_fisheye`, export CVAT for images 1.1.

Các lỗi còn tồn ở trên sẽ được đối chiếu ở P4 và sửa có đối chứng ở P5 (rework), không sửa âm thầm sau khóa.
