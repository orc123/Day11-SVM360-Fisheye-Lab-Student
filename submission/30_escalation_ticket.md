# Escalation ticket

## Ticket 1 — Teaching reference đặt `ego_body` sai bên ở 167700 và 199770

- **Frame:** `adasind_167700.jpg`, `adasind_199770.jpg` (slice B3-dense). Frame `adasind_145860.jpg` cùng slice có ego đúng ở góc dưới-trái, dùng để đối chứng.
- **Ảnh chụp:** `submission/screenshots/escalation_ref_ego_body.png`. Màu đỏ là polygon `ego_body` của reference, màu xanh là `ego_body` của tôi, box vàng là box reference.
- **Hiện tượng:**
  - Ở 167700, reference đặt `ignore_region reason=ego_body` (922–1077, 747–1443) lên **người quàng khăn đang dắt xe đạp** ở mép phải.
  - Ở 199770, reference đặt `ego_body` (924–1080, 813–1552) lên **xe ba bánh mép phải**, trong khi chính reference có box `R4 ThreeWheeler` nằm trong polygon đó, trái R09. Xe này có biển số đã được làm mờ, nên là xe khác chứ không phải xe ego.
  - Ở cả hai frame, thân xe ego thật (người áo caro ở góc dưới-trái) **không có polygon nào**.
- **Expected impact:**
  - Box đúng của annotator bị tính `IGNORE_SCOPE` và reference sinh ra `MISSING` giả: 167700 L1, L6/R2; 199770 L5/R4.
  - Model bị tính `M_only` khi phát hiện người ego (199770 M4).
  - Số `mid`/`edge` trong `zone_table.md`, `local_quality.md` và `delta.md` của 2 frame này bị lệch. Mọi học viên nhận slice B3-dense đều bị ảnh hưởng.
- **Owner:** `qa`
- **Recommendation:**
  1. Ở 167700 và 199770, xóa polygon `ego_body` bên phải.
  2. Vẽ `ego_body` cho người áo caro ở góc dưới-trái, giống 145860.
  3. Ở 167700, thêm box `Pedestrian` `truncated` cho người quàng khăn.
  4. Giữ `R4 ThreeWheeler` ở 199770. Riêng cẳng chân mép phải dưới 199770, phân xử là box `Pedestrian` `truncated` hay `ignore_region` `unreadable`.
  5. Chạy lại compare, local-quality và model cho B3-dense sau khi sửa.
  6. Thêm vào quy trình QA reference một bước kiểm: không có box nào nằm trong `ignore_region` của chính reference.

## Ticket 2 — Xe ba bánh mui trắng ở xa 199770 không có box ở cả L, R và M

- **Frame:** `adasind_199770.jpg`
- **Ảnh chụp:** `submission/screenshots/escalation_199770_far_threewheeler.png` (vùng x 180–360, y 770–900, phóng to 4 lần). Khung đỏ là xe chưa có box, khung vàng là R7 Truck.
- **Hiện tượng:** xe ba bánh mui trắng tròn ở x≈280–322, y≈802–858 (cao ~56 px, trên H=40), nằm ngay phải R7. Bản khóa của tôi, teaching reference và model đều không có box cho xe này. QA mù đã ghi thành `missing@278,802`.
- **Expected impact:** reference thiếu một ThreeWheeler, nên ai vẽ đúng xe này sẽ bị tính `SPURIOUS`. Recall của class ThreeWheeler bị đánh giá sai. Rework ở P5 thêm box này cũng sẽ làm số `spurious` tăng chứ không giảm.
- **Owner:** `qa`
- **Recommendation:** người phân xử xác nhận trên ảnh gốc. Nếu đúng là xe ba bánh ≥H, thêm `ThreeWheeler` vào reference B3-dense. Nếu không đủ rõ, thêm `ignore_region reason=unreadable` để thành don't-care cho mọi bên.
