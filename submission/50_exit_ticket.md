# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy
   tắc riêng? Vì sao?

   **Cần một quy tắc riêng, không mặc định là `DUPLICATE`.** `DUPLICATE` là hai box cho cùng một vật **trên cùng một ảnh**. Ở seam, mỗi camera là một ảnh riêng: nếu nhãn được chấm theo từng camera trên ảnh gốc (như bài này), hai box là hai nhãn đúng trên hai ảnh. Chỉ khi output đích là **một object hợp nhất** (ví dụ trên BEV hoặc trong hệ tọa độ xe), hai box mới cần được ghép và mới có thể sinh trùng. Vì vậy quy tắc phải nói rõ output đích trước. Việc ghép chỉ được làm khi có timestamp đồng bộ và calibration để chiếu hai box về cùng không gian; thiếu hai thứ đó thì giữ cả hai box và đánh dấu "seam – chờ policy".

2. Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái
   Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera.

   - **Giữ cùng track ID:** khi vẫn là cùng một vật quan sát được liên tục, kể cả khi bị che một phần (`occluded`) hoặc bị vòng kính cắt dần (`truncated`).
   - **Thêm keyframe:** khi hình học đổi lớn, ví dụ vật đi từ `center` ra `edge` và bị méo mạnh, hoặc đổi hướng hay kích thước đột ngột. Không để nội suy tự kéo box qua vùng méo.
   - **Đặt Outside:** khi vật ra khỏi vòng kính hoặc khung ảnh, hoặc bị che hoàn toàn. Nếu vật quay lại mà không chắc là cùng vật thì mở track mới thay vì nối.
   - **Bằng chứng cần trước khi nối track qua hai camera:** timestamp đồng bộ của hai camera; calibration intrinsics và extrinsics để chiếu vị trí về cùng hệ tọa độ; vùng chồng (seam) xác định trước; policy output (một object hợp nhất hay track riêng theo camera); và kiểm tra nhất quán về vị trí, vận tốc và class ở khung chuyển giao.

3. Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`),
   bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm?

   **Ca tôi tin mình đúng:** `adasind_167700.jpg` `L1` là người quàng khăn dắt xe đạp ở mép phải, tôi gán `Pedestrian` `truncated`. Reference lại phủ người này bằng `ignore_region reason=ego_body`, nên box của tôi bị tính `IGNORE_SCOPE` và xe đạp của họ thành `MISSING`. Ở 199770, reference còn đặt `ego_body` lên xe ba bánh `R4` mà chính nó đã box.

   **Cách xử lý:** tôi không xóa box để khớp reference. Tôi đối chiếu với 145860 (ego đúng ở góc trái), với R07 và R09, và với model (M2 cũng thấy Pedestrian). Sau đó tôi ghi `E0_reference_defect` với `action=escalate`, viết Ticket 1 kèm ảnh, và ghi D03 `escalated`.

   **Ngược lại, có chỗ tôi tưởng đúng nhưng sai:** xe ở xa 199770 (`L6+R7`). Tôi gán ThreeWheeler theo bối cảnh, còn phóng to thì thấy đó là xe tải nhỏ.

   **Nếu làm lại slice này:**
   - (1) Phóng to từng vật nhỏ hoặc trong vùng tối trước khi chọn class, và đếm từng xe trong cụm thay vì vẽ một box cho cả cụm.
   - (2) Sửa hết cảnh báo self-QC **trước** khi khóa, thay vì khóa luôn rồi để dồn sang rework.
   - (3) Vẽ `ego_body` phủ đủ tay, thân và bàn chân ngay từ đầu ở cả 3 frame.
