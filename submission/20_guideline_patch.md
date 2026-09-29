# Guideline patch

- **Rule mới đề xuất:**
  - **R07a:** `ego_body` chỉ được vẽ trên vùng **thân xe, người ngồi trên xe, gương hoặc tay lái của chính xe gắn camera**. Xác định vùng này bằng vị trí cố định qua các frame của cùng camera; ADASIND B3 thấy ở góc dưới-trái. Người hoặc xe **khác** đứng sát mép ảnh không phải ego, kể cả khi to và bị cắt: vẽ box với `truncated`. Một polygon `ignore_region` **không được chứa box nào** của cùng bản nhãn (R09 áp dụng cho cả reference).
  - **R12:** vật cao ≥ H nhưng quá mờ hoặc nhỏ để xác định class (ví dụ khi hai người soát không thống nhất class sau khi phóng to) thì vẽ `ignore_region` với `reason=unreadable` thay vì đoán class. Nếu class đọc được sau khi phóng to thì vẫn box bình thường, **không được đoán class theo bối cảnh** (ví dụ "cảnh này nhiều xe ba bánh").
- **Áp dụng cho:** `ignore_region` (`ego_body`, `unreadable`), R09, và mọi class ở zone `mid`/`edge` khi vật nhỏ gần ngưỡng H. Áp dụng cho cả annotator lẫn người tạo teaching reference.
- **Vì sao luật hiện tại (`docs/02-rules-vi.md`) không đủ:**
  - R07 chỉ nói "vẽ khi có thân xe ego", không nói cách **nhận ra** vùng nào là ego. Hệ quả là reference B3-dense đặt `ego_body` lên người quàng khăn (167700) và lên xe ba bánh có box R4 (199770), trong khi thân ego thật ở góc trái không có polygon (Ticket 1, 4 khác biệt bị tính sai).
  - R01 và R06 không nói rõ khi nào vật ≥H thì chuyển sang `unreadable`. Hệ quả là 145860 L2 (tôi gán Truck, reference gán unreadable) và 199770 R7 (tôi gán ThreeWheeler, reference Truck, model Car).
- **`rules_version` mới:** v1.0.0 → v1.1.0
- **Hiệu lực từ:** round kế tiếp sau khi Ticket 1 được phân xử. Reference B3-dense cần sửa theo R07a trước, rồi chạy lại `compare`/`local-quality`. Các bản đã khóa ở v1.0.0 giữ nguyên, không sửa ngược.
