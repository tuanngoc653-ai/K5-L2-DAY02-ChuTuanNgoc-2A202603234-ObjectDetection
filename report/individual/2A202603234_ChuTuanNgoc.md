# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Chu Tuấn Ngọc
- MSSV: 2A202603234
- Email: tuanngoc653@gmail.com
- Lớp: K5-L2A
- Link repo bài làm: https://github.com/tuanngoc653-ai/K5-L2-DAY02-ChuTuanNgoc-2A202603234-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/tuanngoc653-ai/K5-L2-DAY02-ChuTuanNgoc-2A202603234-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb (notebook đã chạy trên Colab T4, lưu kèm output trong repo)

## 2. Schema và mini guideline

Xem [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Bốn ca mơ hồ đã ghi quyết định:

- `drive_019` (cùng loại xe ở 014, 026, 034, 038, 046): xe cứu hộ công an có cần cẩu → `truck`.
- `drive_006`: xe nhỏ dáng cao, có giá nóc → `van`.
- `drive_024`, `drive_027`: minivan nhỏ màu be/bạc → `van`.
- `drive_026`: xe cứu hộ công an nhìn từ phía sau → `truck`, nhất quán với 019.

Cần giảng viên làm rõ: xe nhỏ đến mức nào thì phải gán nhãn, và xe chỉ còn khoảng một nửa (bị che hoặc bị cắt khung) có cần vẽ box hay không.

## 3. Annotation và self-QC

- Số file nhãn train: 50/50, gán bằng CVAT (local), export định dạng Ultralytics YOLO Detection 1.0.
- Kết quả checker: `python tools/check_yolo_labels.py` → "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ".
- Danh sách ảnh đã rà bằng mắt: rà lại 30 ảnh đầu (`drive_001`–`drive_030`) sau lần export đầu tiên, đối chiếu với ảnh vẽ box lên ảnh gốc. Không ghi lại danh sách chi tiết trong lúc rà.
- Lỗi phát hiện khi rà và thay đổi thực tế (so sánh lần export đầu với bản khóa nhãn): `drive_030` lúc đầu mới gán 10 box, gán bổ sung lên 48 box (thêm 30 car, 5 bus, 3 van); `drive_019` đổi 2 box từ `van` sang `car`. Các lỗi khác được chỉ ra khi rà (một số xe bị cắt ở mép ảnh, xe tải thùng kín ở `drive_022` đang là `bus`, hàng xe đỗ lề phải `drive_028`) chưa được sửa trước khi khóa nhãn.
- Commit khóa nhãn ban đầu: `2176c69` (CP4: lock initial labels (50 images) + mini guideline).

## 4. Gold QC và rework

Chạy `tools/evaluate_annotations.py` với `gold_train_labels/` do giảng viên phát; kết quả lưu ở [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json).

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 469 | không rework |
| FP | 584 | |
| FN | 184 | |
| Precision | 0.4454 | |
| Recall | 0.7182 | |
| F1 | 0.5498 | |
| mean_iou_of_matches | 0.8519 | |
| passed | false | |

Phân tích lỗi:

- **Thứ tự ID lớp của gold khác README.** 121 cặp box khớp vị trí nhưng lệch lớp theo vòng: van (học viên) ↔ ID 1 (gold), bus ↔ 3, truck ↔ 2. Ví dụ `drive_022`: gold gán xe buýt nối toa = 3 và xe tải thùng kín = 2. Như vậy gold dùng `0 car, 1 van, 2 truck, 3 bus`, còn README/`data.yaml` dùng `0 car, 1 truck, 2 bus, 3 van`. Khi chỉ đổi ID để chẩn đoán (không sửa gold, không nộp bản này): lỗi lệch lớp giảm từ 121 còn 12, recall 0.885, F1 0.678, mean IoU 0.858.
- **Box ngoài phạm vi gold.** 445 box không gần box gold nào, cạnh ngắn trung vị ≈ 23 px (gold ≈ 47 px), tập trung ở xe nhỏ ở xa / xe đỗ trong các ảnh 030, 003, 006, 011, 002, 031, 012.
- Giảng viên (Lab Coach) xác nhận gold đang lệch nhãn và cho phép bỏ qua cổng F1, làm đủ các bước còn lại.

Nhận xét: những xe nhỏ ở xa mắt người nhìn ảnh vẫn nhận ra nên em đã gán nhãn. Đây là lần đầu em gán nhãn nên có thể em gán kỹ hơn chuẩn của gold, và chưa chắc model nhìn được những xe nhỏ như người. Em không rework vì giảng viên đã xác nhận gold lệch ID lớp và cho phép bỏ qua cổng F1.

## 5. Kết quả YOLO

- Runtime: Google Colab, GPU Tesla T4 (15 GB); Ultralytics 8.4.208, Python 3.13.16, Torch 2.14.1+cu130.
- Tham số: giữ nguyên notebook (`yolo26n.pt`, 50 epoch, imgsz 640, batch tự động = 43, seed 20260909). Chỉ điền `REPO_URL`. Thời gian train ≈ 78 giây.
- Best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không nộp `.pt`).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4071 |
| Recall | 0.2340 |
| mAP50 | 0.2737 |
| mAP50–95 | 0.2018 |

Theo lớp (tên lớp do Ultralytics đọc từ `data.yaml`):

| Lớp | Instances | P | R | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|
| car | 94 | 0.836 | 0.653 | 0.738 | 0.523 |
| truck | 5 | 0.656 | 0.200 | 0.239 | 0.206 |
| bus | 2 | 0.000 | 0.000 | 0.014 | 0.005 |
| van | 12 | 0.136 | 0.083 | 0.103 | 0.074 |

Lưu ý: nhãn `dataset/labels/eval/` dùng cùng thứ tự ID với gold, nên dòng "van" (12) thực chất là 12 xe bus, "truck" (5) là van, "bus" (2) là truck.

Ảnh và chỉ số:

- 10 ảnh dự đoán: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/), ví dụ [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg).
- Log train: [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [results.png](assets/yolo26_runs/vehicle_annotation/results.png).
- Đánh giá: [confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [BoxPR_curve.png](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png).

Ví dụ cụ thể: trong [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg) chỉ có 2 xe bus; model gán nhầm 1 xe bus thành `car`, và bỏ sót 1 xe `car` ở giữa ảnh (không có box dự đoán).

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa? Lớp "bus" (recall 0.000) và "van" (recall 0.083) theo tên trong `data.yaml`. Nguyên nhân chính là nhãn eval dùng thứ tự ID lớp giống gold (1 = van, 2 = truck, 3 = bus), khác README mà em dùng khi gán nhãn train, nên model dự đoán đúng xe bus (ID 2) vẫn bị chấm sai so với nhãn eval (ID 3). Ví dụ `drive_053`.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box? Với lớp car, mAP50 = 0.738 còn mAP50–95 = 0.523. Box dự đoán tìm đúng vị trí xe (đạt IoU 0.5) nhưng chưa ôm sát mép xe, nên bị trượt ở các ngưỡng IoU cao (0.75–0.95). Cần kiểm tra độ sát của box, nhất là với xe nhỏ ở xa và xe mờ do chuyển động.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét? Có: thứ tự ID lớp của gold và nhãn eval khác README. Dữ kiện: ở `drive_022` gold gán xe buýt nối toa = 3 và xe tải thùng kín = 2; khi đổi ID cho khớp, số box lệch lớp giảm từ 121 xuống 12; giảng viên đã xác nhận.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì? Nếu train trên chính 10 ảnh eval thì model đã thấy đáp án, điểm chấm lại trên đó bị thổi phồng và không đáng tin. Eval phải là ảnh model chưa học. Trong notebook, 10 ảnh eval còn được dùng làm validation để chọn `best.pt`, nên chúng không phải test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Khởi động CVAT local | Container `traefik` dừng sau khi Docker Desktop khởi động lại, không vào được localhost:8080 | `docker start traefik` | Chạy lại được |
| Gold QC | Gold dùng thứ tự ID lớp khác README | Kiểm tra `drive_022`, thống kê nhầm lớp, báo giảng viên | Giảng viên xác nhận, cho bỏ qua cổng F1 |
| Chạy lại notebook trên bản Drive để có link xem được | Colab báo lỗi cấp runtime, kẹt ở "Allocating runtime"; upload notebook qua Colab bị treo | Tải `.ipynb` đã chạy (kèm output) từ phiên gốc, lưu vào repo | Link Colab trỏ tới notebook trong repo, hiển thị đủ output |

## 7. Tự đánh giá theo rubric

Đây là lần đầu em gán nhãn nên em chưa tự quy ra điểm được. Đối chiếu theo từng tiêu chí:

- Định dạng YOLO: checker đạt 50/50.
- Đầy đủ vật thể: recall 0.718 so với gold (0.885 nếu đổi ID lớp cho khớp); còn sót một số xe bị cắt ở mép ảnh.
- Độ bám sát box: mean IoU 0.852.
- Phân lớp: sau khi đổi ID cho khớp gold còn 12 box sai lớp; ca `drive_022` còn đang là `bus`.
- Mini guideline: có định nghĩa 4 lớp, quy tắc box và 4 ca mơ hồ có lý do.
- Gold-set QC: chưa đạt F1 ≥ 0.80, chưa rework; đã giải thích nguyên nhân ở mục 4.
- Pipeline YOLO: Colab chạy được, không dùng eval để train, có metrics và ảnh eval.

## 8. Công cụ AI và tài liệu tham khảo

Công cụ: Claude (Claude Code).

- Giải thích quy trình lab, khái niệm phân biệt lớp, phím tắt CVAT.
- Tạo project/task trong CVAT và upload ảnh; mở và chạy notebook trên Colab; tải và sắp xếp kết quả.
- Rà nhãn 30 ảnh đầu và nêu lỗi (sót xe ở mép, sai lớp); học viên tự sửa trong CVAT.
- Chạy checker/evaluator, phân tích FP/FN và phát hiện lệch ID lớp của gold.
- Góp ý câu chữ cho mini guideline (lý do do học viên viết).
- Lưu notebook đã chạy vào repo và cập nhật link Colab.
- Toàn bộ bounding box do học viên tự vẽ; không dùng auto-annotation. Phần phân tích ở mục 4–5 và tự đánh giá ở mục 7 do học viên viết.
