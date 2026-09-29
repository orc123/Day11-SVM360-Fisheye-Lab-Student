# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. “Gold set” ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

> Bản nháp P0: ý đầu tiên trước khi có bằng chứng lỗi. Phân bổ hiện tại: normal 72 / hard 128 (front 54, rear 50,
> left 48, right 48). Hard được lấy dư vì ca khó hiếm trong 50.000 frame nhưng là nơi nhãn hay sai.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front | Cảnh đông người và xe hai bánh; người dắt xe; ngược sáng/lóa; vật bị vòng kính cắt ở rìa | Luật rider (R03) dễ nhầm giữa một box `Bike` và cặp `Pedestrian` + `Bike`; lóa làm mất biên vật; vật ở rìa méo mạnh | Box trên **ảnh fisheye gốc** của front; ghi version intrinsics/extrinsics, mask vòng kính, `rules_version` | Hai annotator gán độc lập, người thứ ba phân xử ca lệch; soát tay từng ca hard trên ảnh, không chỉ đọc chỉ số agreement |
| rear | Vật thấp sát cản sau; vạch ô đỗ mờ hoặc lẫn vạch lối xe; thân xe ego che; thiếu sáng | Vật gần camera bị méo và che một phần; `parking_line` dễ bị gán nhầm cho vạch lối xe hoặc mép bãi | Ghi rõ nhãn nằm ở ảnh gốc hay BEV (parking thường dùng BEV); giữ calibration dùng để chiếu sang BEV | Người review kiểm cả ảnh gốc và ảnh chiếu BEV; ca vạch mơ hồ cần rule ghi rõ trước khi đưa vào gold |
| left | Seam góc trước-trái và sau-trái; xe máy vượt sát; bus/truck dài trải qua hai camera | Một vật xuất hiện trên hai camera nên dễ bị coi là trùng hoặc bị bỏ sót; `truncated` và `occluded` bị lẫn (R05) | Extrinsics của left cùng với front/rear; timestamp đồng bộ giữa các camera | Đối chiếu cùng timestamp với camera kề trước khi quyết định ca seam; ca chưa có policy để trạng thái escalated, chưa gọi là gold |
| right | Seam góc trước-phải và sau-phải; curb/vỉa hè sát xe; người đi bộ sát thân xe | Người đi bộ sát xe dễ bị thân xe ego che hoặc bị vòng kính cắt; curb dễ lẫn với vạch | Như left: extrinsics, timestamp và mask `ego_body` riêng cho camera phải | Như left; thêm kiểm chéo polygon `ego_body` vì vị trí gắn camera bên hai phía có thể khác nhau |

- Khi nào cần refresh gold set (đổi camera, calibration hoặc rule): khi thay camera/ống kính hoặc đổi vị trí gắn; khi calibration lại (intrinsics/extrinsics đổi làm lệch mask vòng kính, `ego_body` hoặc phép chiếu BEV); khi bump `rules_version` (ví dụ đổi luật rider hay luật seam); khi thêm domain mới chưa có trong mẫu (đêm, mưa, tuyến mới); hoặc khi review định kỳ thấy tỷ lệ bất đồng với gold tăng.
- Một ca seam/cross-camera cần policy và evidence trước khi ghép hai box: xe máy vượt ở góc trước-trái, cùng lúc hiện ở rìa (`edge`) của front và vùng `mid` của left. Trước khi coi hai box là cùng một vật hoặc gán `DUPLICATE`, cần có timestamp đồng bộ hai camera, calibration để chiếu cả hai về cùng không gian (BEV/xe), và policy output đích: giữ box riêng theo từng camera, hay hợp nhất thành một object. Chưa đủ cả ba điều kiện thì ghi escalated, không tự ghép box hay gán cùng track ID.
- Vì sao peer agreement hoặc quality report trên ảnh một camera chưa chứng minh gold set đúng cho cả bốn camera: agreement chỉ đo mức **nhất quán** giữa người gán, không đo mức đúng, vì hai người có thể cùng hiểu sai một luật. ADASIND chỉ có một camera, không có seam, không có góc nhìn rear/side và không có calibration, nên chỉ số trên nó không nói gì về lỗi riêng của từng camera SVM. Teaching reference ADASIND là bản sửa tay để dạy, có thể sai, và không phải gold set bốn camera.
- Bổ sung sau P4, từ lỗi thật trên slice ADASIND B3-dense (một camera, 3 frame, nên chỉ là tín hiệu định hướng):
  - (1) **Ignore/ego sai là lỗi đắt nhất.** Reference đặt `ego_body` sai bên ở 2/3 frame, sinh 4 khác biệt giả (Ticket 1). Vì vậy mọi camera cần một bước **soát `ego_body` và `ignore_region` riêng** trước khi gọi là gold, cùng một kiểm tự động "không box nào nằm trong ignore". Đã thêm vào cột review ở trên.
  - (2) **Cụm vật sát nhau và vùng tối** gây phần lớn MISSING của người ở zone `mid` (199770: gộp 2 xe máy, sót người trong quán). Vì vậy giữ ô `hard` của **left/right** (vỉa hè, dãy quán, xe đậu) ở mức 32 thay vì giảm.
  - (3) **Class ThreeWheeler** sai ở cả người (C0: 3/3 xe gán Truck) lẫn model (4/4 xe gần gán Truck/Car), nên ô `front,hard` (34) phải có đủ xe ba bánh và xe tải nhỏ ở xa.
  - (4) **`truncated` tick nhầm** cho vật bị che nhưng không bị cắt (167700 L8, L10) cho thấy người review cần kiểm attribute theo hình học vòng kính, không kiểm bằng cảm giác.
  - Giữ nguyên tổng phân bổ 200 (72 normal / 128 hard): bằng chứng từ 3 frame chưa đủ để dời frame giữa các camera.
