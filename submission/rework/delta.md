# Rework delta

| zone | matched before | matched after | missing before | missing after | spurious before | spurious after |
|---|---:|---:|---:|---:|---:|---:|
| center | 8 | 8 | 1 | 1 | 1 | 1 |
| mid | 3 | 4 | 4 | 3 | 2 | 1 |
| edge | 2 | 2 | 2 | 2 | 1 | 1 |

## Findings action=rework
- adasind_019560.jpg L3 SPURIOUS: không áp dụng
- adasind_019560.jpg L4+R3 WRONG_CLASS: không áp dụng
- adasind_019560.jpg L6+R5 WRONG_CLASS: không áp dụng
- adasind_019560.jpg L7+R6 WRONG_CLASS: không áp dụng
- adasind_019560.jpg ignore_region#1 IGNORE_SCOPE: không áp dụng
- adasind_167700.jpg L6 BOX_GEOMETRY: đã sửa
- adasind_167700.jpg L8 ATTRIBUTE: chưa sửa
- adasind_167700.jpg L10 ATTRIBUTE: chưa sửa
- adasind_167700.jpg ego_body IGNORE_SCOPE: không áp dụng
- adasind_145860.jpg ego_body IGNORE_SCOPE: không áp dụng
- adasind_167700.jpg L6 IGNORE_SCOPE: chưa sửa
- adasind_167700.jpg L8+R4 ATTRIBUTE: chưa sửa
- adasind_167700.jpg L10+R8 ATTRIBUTE: chưa sửa
- adasind_167700.jpg R2 MISSING: chưa sửa
- adasind_199770.jpg L1+R5 BOX_GEOMETRY: chưa sửa
- adasind_199770.jpg L6+R7 WRONG_CLASS: đã sửa
- adasind_199770.jpg R3 MISSING: chưa sửa
- adasind_199770.jpg R6 MISSING: chưa sửa
- adasind_167700.jpg R2+M7 MISSING: chưa sửa
- adasind_199770.jpg L1+M5 SPURIOUS: đã sửa
- adasind_199770.jpg L6 SPURIOUS: đã sửa
- adasind_199770.jpg R3+M10 MISSING: chưa sửa
- adasind_199770.jpg R5 MISSING: chưa sửa
- adasind_199770.jpg R6 MISSING: chưa sửa
- adasind_199770.jpg R7 MISSING: đã sửa

## Giải thích của tôi

- **Phạm vi rework:** `degrade rework`, chỉ sửa **một ca**: 199770 box xe ở xa (216–275, 814–862) đổi `ThreeWheeler` → `Truck` (finding `L6+R7 WRONG_CLASS`, E1, P1). Không có ca P0 nào do annotator; các dòng P0 đều là `E0_reference_defect` đã escalate (Ticket 1), nên không sửa. Bản v2 được tạo bằng cách sửa đúng một thuộc tính `label` trong XML đã khóa, không qua CVAT (xem D05 trong `40_decision_log.csv`).
- **Số thay đổi thật:** chỉ zone `mid`: matched 3 → 4, missing 4 → 3, spurious 2 → 1. Đây là tác động của đúng một box đổi class: box từ sai class chuyển thành khớp R7. `center` và `edge` giữ nguyên vì không sửa gì ở hai zone này.
- **Hai dòng tool ghi "đã sửa" nhưng thực tế CHƯA sửa:**
  - `167700 L6 BOX_GEOMETRY` (dòng QA): box Bike người quàng khăn vẫn là 756–1080.
  - `199770 L1+M5 SPURIOUS`: 2 xe máy đậu vẫn bị gộp một box.

  Tool đánh "đã sửa" vì không còn tìm thấy đúng cặp khác biệt đó sau khi số thứ tự L thay đổi. Không dùng hai dòng này làm bằng chứng cải thiện.
- **Còn tồn, chuyển sang vòng sau:** L6 và ego_body của 167700, tách 2 xe máy và thêm người trong quán ở 199770, ego_body bàn chân ở 145860, bỏ `truncated` ở L8/L10.
