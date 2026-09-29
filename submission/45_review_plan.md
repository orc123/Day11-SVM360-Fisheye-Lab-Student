# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| **Vùng `ego_body` / mép ảnh của reference**: `adasind_167700.jpg`, `adasind_199770.jpg` | 4 khác biệt L–R do polygon ego đặt sai bên (167700 L1, L6/R2; 199770 L5/R4), cộng 1 M_only giả (199770 M4). Cả 4 đều là `E0`, P0 | Lỗi phạm vi (R10 – P0) làm sai số của **mọi** học viên nhận slice này, và làm lệch luôn `delta.md`. Sửa một polygon có thể thay đổi 4–5 dòng số | `screenshots/escalation_ref_ego_body.png`, Ticket 1, decision log D03, các dòng findings có `why=E0_reference_defect` |
| **Cụm vật sát nhau, vùng tối, zone `mid`**: `adasind_199770.jpg` | MISSING 8 ở `mid` trên error card. Riêng lỗi người: gộp 2 xe máy đậu (`L1+R5`, `R6`), sót người trong quán (`R3`), gán sai xe tải nhỏ xa (`L6+R7`, đã rework). Local quality của frame: recall 0.333 | Đây là frame có recall thấp nhất, và lỗi lặp theo một kiểu (đếm cụm, vùng tối) nên sẽ tái diễn ở frame đông khác. Model cũng có 7 `M_only` ở đây: 3 Pedestrian nhỏ cần soi, 4 là lỗi class ThreeWheeler | Crop phóng to từng cụm (ví dụ `screenshots/escalation_199770_far_threewheeler.png`), `r3_diag/local_quality_conflicts.csv`, `model_compare.html` |

Giới hạn của kết luận từ ba frame ADASIND:
- Chỉ có 3 frame, 20 box reference và một camera. Một ca thêm hay bớt đã làm đổi recall của một class tới hàng chục phần trăm.
- Teaching reference đã được chứng minh có lỗi. Mọi con số của 167700 và 199770 chỉ là tạm cho tới khi Ticket 1 được phân xử.
- Zone `center/mid/edge` là bán kính trên ảnh, không phải khoảng cách tới xe, nên không suy ra được rủi ro an toàn.
- Mọi frame là cảnh ban ngày đô thị Ấn Độ, không nói gì về đêm, mưa hay cao tốc (145860 chỉ có 2 box).

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv` (kể cả tránh đếm nhiều frame liền nhau trong cùng cảnh
như nhiều ca độc lập), và vì sao kế hoạch đó chỉ giúp tìm ca cần soi, chưa đo được tỷ lệ lỗi:

- **Phân tầng trước, rồi lấy mẫu:** với mỗi camera, chia 50.000 frame theo chuyến/tuyến và khung thời gian, rồi chọn frame trong từng ô `normal`/`hard`. Mỗi đoạn liên tục (cùng chuyến, cách nhau dưới vài giây) chỉ lấy tối đa 1 frame, để 5 frame liền nhau của cùng một ngã tư không bị tính thành 5 ca độc lập.
- **Kiểm độ phủ bằng bảng đếm:** trước khi chốt, đếm 200 frame theo camera × điều kiện (ngày/đêm, mật độ, có seam hay không, có ego_body hay không) và theo loại ca khó rút ra từ bài này: ThreeWheeler, cụm xe sát nhau, người trong vùng tối, vật cắt ở rìa. Ô nào bằng 0 thì bổ sung.
- **Soát riêng ego và ignore của từng camera:** mỗi camera có hình `ego_body` khác nhau, nên cần kiểm ignore trước khi đếm lỗi (bài học từ Ticket 1).
- **Vì sao chỉ giúp tìm ca, chưa đo tỷ lệ lỗi:**
  - Ô `hard` được lấy dư có chủ đích (128/200), nên tỷ lệ lỗi trên mẫu cao hơn thực tế.
  - 200 frame chia 8 ô là quá ít để có khoảng tin cậy hẹp cho từng camera.
  - Muốn đo tỷ lệ lỗi cần mẫu ngẫu nhiên có trọng số theo phân bố thật của 50.000 frame, và một gold set đã được phân xử, chứ không phải teaching reference.
