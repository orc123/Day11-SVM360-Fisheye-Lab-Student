# QA review · B3-dense

Mã khóa: CE4A-167F

Hình thức: **cold review** chính bản khóa của mình (làm solo), sau khi nghỉ; chỉ dùng ảnh gốc, `qa_overlay.html` và `docs/02-rules-vi.md`. Chưa mở teaching reference, model overlay hay worked HTML. `object_ref` theo số L trong `qa_overlay.html`.

| frame | object_ref | rule_id | nhận xét |
|---|---|---|---|
| adasind_167700.jpg | L6 | R02 | Bike của người quàng khăn kéo cạnh phải tới mép ảnh (x=1080), nhưng xe đạp chỉ nhìn thấy tới x≈980; phần sau bị chân người che. Box nên bám phần thấy được, giữ `occluded`. |
| adasind_167700.jpg | L8 | R05 | Car (xe van) tick `truncated` nhưng box nằm gọn trong ảnh và trong vòng kính; xe chỉ bị thùng hàng che góc dưới-trái → đúng ra `occluded`, không `truncated`. |
| adasind_167700.jpg | L10 | R05 | Truck xám ở xa tick `truncated` dù không chạm mép ảnh hay vòng kính; chỉ bị xe van che → bỏ `truncated`, giữ `occluded`. |
| adasind_167700.jpg | ego_body | R07 | Polygon `ego_body` chỉ phủ tay áo caro; thân tối màu phía dưới tay tới vành kính (x 0–270, y≈1440–1680) và túi/tay nắm góc trên-trái còn lộ → vùng ego chưa được loại khỏi phạm vi. |
| adasind_145860.jpg | ego_body | R07 | `ego_body` phủ tay, thân, hai chân nhưng thiếu bàn chân/dép thứ hai ở đáy vòng kính (x≈255–345, y≈1740–1795). |
| adasind_199770.jpg | missing@278,802 | R01 | Thiếu box xe ba bánh mui trắng thứ 2 ở xa (x≈278–330, y≈802–860, cao ~58 px ≥ H=40), nằm ngay phải L6. |
| adasind_145860.jpg | L2 | R04 | Xe màu vàng ở xa gán `Truck`; ảnh mờ, nhìn từ phía sau giống thùng xe ben nhưng chưa loại trừ được auto-rickshaw. Cần người phân xử class. |
| adasind_199770.jpg | missing@950,1300 | R01 | Cẳng chân + dép ở mép phải dưới (x≈950–1060, y≈1300–1560, phần thấy ~260 px) không có box cũng không có ignore. Theo R01 phần nhìn thấy ≥ H; nếu coi là Pedestrian thì cần box `truncated`, nếu không nhận diện được thì cần `ignore_region` `unreadable`. |

Không thấy lỗi ở: class của các ThreeWheeler (167700 L7, L11; 199770 L4–L7), luật rider (167700 L2+L3, L4+L5, L1+L6; 199770 L2+L3), `truncated` của 167700 L1 và 199770 L4, L5, `lens_border` ở cả 3 frame.

Ghi finding r2_qa: cell=L_only, rule_id có giá trị, why để trống.
