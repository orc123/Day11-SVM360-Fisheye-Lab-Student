# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B3 | ATTRIBUTE | 4 |
| center | B3 | BOX_GEOMETRY | 1 |
| center | B3 | MISSING | 3 |
| center | B3 | SPURIOUS | 4 |
| center | C0 | SPURIOUS | 1 |
| center | C0 | WRONG_CLASS | 1 |
| edge | B3 | BOX_GEOMETRY | 1 |
| edge | B3 | MISSING | 5 |
| edge | B3 | SPURIOUS | 3 |
| edge | C0 | WRONG_CLASS | 1 |
| mid | B3 | BOX_GEOMETRY | 1 |
| mid | B3 | IGNORE_SCOPE | 4 |
| mid | B3 | MISSING | 8 |
| mid | B3 | SPURIOUS | 7 |
| mid | B3 | WRONG_CLASS | 2 |
| mid | C0 | WRONG_CLASS | 1 |
| unknown | B3 | IGNORE_SCOPE | 2 |
| unknown | B3 | MISSING | 2 |
| unknown | C0 | IGNORE_SCOPE | 1 |

## Top defects
- MISSING: 18 (ví dụ frame adasind_199770.jpg)
- SPURIOUS: 15 (ví dụ frame adasind_019560.jpg)
- IGNORE_SCOPE: 7 (ví dụ frame adasind_019560.jpg)

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

Lỗi nổi bật nhất là **MISSING (18)**, tập trung ở **zone `mid` của B3 (8)**, gần hết thuộc frame `adasind_199770.jpg`. Bảng đếm gộp cả dòng người (L), reference (R) và model (M), nên tách theo nguồn:

- Nguyên nhân khả dĩ (`why`) và vì sao bạn nghĩ vậy:
  - **(a) `E1_annotator_error`, cảnh đông và tối.** Ở 199770 tôi gộp 2 xe máy đậu sát thành một box (`L1+R5`, còn `R6` thành thiếu), và bỏ sót người đứng trong quán tối (`R3`, ~118 px, trên H). Cả hai nằm trong bóng cây và dãy quán, đúng loại cảnh mà card 6 nhắc "đếm được thì box riêng". Tôi đã soát nhanh theo cảm giác "cụm xe" thay vì đếm từng vật.
  - **(b) `E0_reference_defect`.** `167700 R2` và `199770 R4` là MISSING giả: box của tôi bị polygon `ego_body` đặt sai bên của reference loại khỏi phạm vi.
  - **(c) Model (`LR_noM`, `R_only`).** Model thiếu chủ yếu vì gọi xe ba bánh là Truck hoặc Car. Lỗi này lặp lại 4/4 xe ba bánh gần, đủ để coi là `E4_model_domain`.
- Cách sửa và ai nhận việc (`owner`):
  - (a) `annotator`: soát lại từng cụm vật sát nhau bằng cách phóng to và đếm, ưu tiên vùng tối.
  - (b) `qa`: sửa polygon `ego_body` của reference (Ticket 1). Thêm bước kiểm tự động "không box nào nằm trong `ignore_region` của chính reference".
  - (c) `ai_team`: bổ sung dữ liệu ThreeWheeler (auto-rickshaw) cho model.
  - Rework P5 mới sửa 1 ca (xe tải nhỏ 199770, `mid` missing 4 → 3). Các ca (a) còn tồn, đã liệt kê trong `rework/delta.md`.
- Bằng chứng (ảnh trong `screenshots/`, dòng findings, rule):
  - `findings.csv`: các dòng `r1_craft` 199770 `L1+R5`, `R3`, `R6` (R01) và 167700 `R2`, 199770 `R4` (R07/R09); các dòng `r3_diag` `L7+R6`, `L4+R2`, `L7+R8`, `M8`, `M12` (R04).
  - `screenshots/escalation_ref_ego_body.png` cho (b).
  - `r3_diag/local_quality.md`: frame 199770 có recall 0.333, Bike có recall 0.333.
