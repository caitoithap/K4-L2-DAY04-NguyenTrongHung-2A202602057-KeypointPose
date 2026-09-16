# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Trọng Hùng   Nhóm: solo   Ngày: 16/09

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 27 |
| v=2 / v=1 / v=0 | 355 / 73 / 31 |
| Thời gian trung bình mỗi ảnh | 5 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `right_ear`: 37% (10/27 skeleton)
2. `right_wrist`: 30% (8/27 skeleton)
3. `right_knee`: 30% (8/27 skeleton)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

<!-- Trả lời 2–4 câu. Phân biệt “hay bị che” với “khó xác định vị trí giải phẫu”; nêu bằng
chứng nhìn thấy thay vì chỉ nêu cảm giác. -->

Đúng một phần. Tai phải và cổ tay phải thường bị tóc, thân người hoặc vật cầm che nên có tỷ lệ `v=1` cao. Tuy nhiên, việc bị che không tự động có nghĩa là khó xác định vị trí giải phẫu: khi còn thấy đầu, vai hoặc cẳng tay liền kề, có thể ước lượng vị trí khớp. Đầu gối phải cũng có `v=1` cao, nhưng đồng thời có 7 trường hợp `v=0`; vì vậy cần phân biệt rõ đầu gối bị che trong khung với phần chân thực sự đã ra khỏi mép ảnh.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | Chưa có file trước rework | 0.4293 |
| OKS@0.50 | Chưa có file trước rework | 0.2759 |
| OKS@0.75 | Chưa có file trước rework | 0.2414 |
| Lỗi `dao_trai_phai` | Chưa có file trước rework | 20 |
| Lỗi `nham_nguoi` | Chưa có file trước rework | 18 |
| Lỗi `xoa_khop_bi_che` | Chưa có file trước rework | 1 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Mỗi dòng phải có: tên ảnh + người thứ mấy + keypoint + thao tác sửa. Không viết “đã sửa
lại một số lỗi”. -->

Không có log chấm trước rework hoặc nhật ký thao tác sửa nhãn trong repository, nên không thể xác nhận trung thực các sửa đổi giữa hai lần chạy. Cần chạy/lưu lần chấm đầu và ghi thao tác theo từng ảnh trước khi hoàn tất mục này.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

<!-- Nếu không có lỗi, ghi rõ “Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh.” -->

Lỗi đảo trái/phải xuất hiện ở nhiều ảnh, không chỉ một ảnh: `train_01`, `03–05`, `07–08`, `10–15`, `17–20` (20 skeleton). Ví dụ rõ nhất là `train_10.jpg`, người 1, có OKS 0.1020. Đây không phải lỗi do ảnh dễ hay khó riêng lẻ mà là lỗi hệ thống khi xác định trái/phải theo phía trên ảnh thay vì theo cơ thể người; cần kiểm tra cặp vai–hông–tay/chân trước khi lưu mỗi skeleton.

## 3. Kiểm chéo

Bạn cùng nhóm: không áp dụng (làm solo).

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| Chưa có bản đối chiếu | — | — | — | Không có visibility report của người thứ hai |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi “cẩn thận hơn khi gán”. -->

- Không có kiểm chéo để rút ra luật mới. Khi thực hiện kiểm chéo, dùng quy tắc: nếu khớp còn trong khung nhưng bị che và có thể suy ra từ bộ phận liền kề, đặt điểm và gắn `v=1`; chỉ dùng `v=0` khi vị trí giải phẫu đã nằm ngoài mép ảnh.

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?

   `pose_mAP50-95` tăng 0.0055, từ 0.6853 lên 0.6908, nên không có bằng chứng cho kết luận nó giảm. Với tập 20 ảnh nhỏ, mức tăng này chỉ là quan sát trên tập test, không đủ để khẳng định mô hình tổng quát tốt hơn.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao?

   Sau fine-tune, `box_mAP50-95` là 0.8041, cao hơn `pose_mAP50-95` 0.6908 là 0.1133. Model tìm người dễ hơn tìm đúng các khớp vì box chỉ cần bao phủ cơ thể, còn pose phải xác định chính xác 17 vị trí, kể cả các khớp nhỏ, bị che hoặc gần nhau.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

   Repository chỉ lưu chỉ số tổng hợp trong `outputs/eval_model.json`; không có ảnh dự đoán test hoặc log per-image của model. Vì vậy chưa thể gọi đúng loại lỗi cho một ảnh cụ thể mà không xem lại đầu ra của cell 5 trong notebook.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

   Chưa xác định được: bảng OKS model-vs-nhãn-của-bạn theo từng ảnh (cell 6 của notebook) không được lưu trong repository. Không thể kết luận ai đúng chỉ từ `eval_model.json`; cần đối chiếu ảnh dự đoán với ảnh gốc và gold.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?

   Theo gold, `train_13.jpg` là ảnh gán tệ nhất vì thiếu hoàn toàn 2 người (`OKS = 0` cho gold person 1 và 2). Không có kết quả model-vs-nhãn theo ảnh, nên chưa thể xác định nó có đồng thời là ảnh model đoán tệ nhất hay không. Nếu trùng nhau, đó sẽ là dấu hiệu ảnh có độ khó thực sự (nhiều người, che khuất hoặc bố cục khó), cần xem ảnh và nhãn chứ không được kết luận từ một con số.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

<!-- Cấu trúc gợi ý: (1) train_XX + người thứ mấy + keypoint; (2) căn cứ thị giác như phần cơ
thể liền kề, trang phục hoặc vật che; (3) vì sao khớp còn trong khung (v=1) hay đã ra khỏi
khung (v=0). -->

Ở `train_13.jpg`, người 1, hai đầu gối và hai mắt cá chân được gắn `v=0`. Ảnh chỉ cho thấy phần cơ thể từ đầu đến hông; toàn bộ phần chân nằm dưới mép dưới của ảnh, nên các vị trí giải phẫu này thực sự ở ngoài khung. Vì vậy không đặt chấm và không dùng `v=1`: `v=1` chỉ áp dụng khi khớp bị che nhưng vẫn nằm trong phạm vi ảnh. Quyết định này dựa vào mép ảnh cắt qua thân dưới, không phải vì không nhìn thấy trực tiếp đầu gối hay mắt cá.
