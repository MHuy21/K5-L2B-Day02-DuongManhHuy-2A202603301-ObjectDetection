# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Dương Mạnh Huy
- MSSV: `2A202603301`
- Email: **manhhuy2104@gmail.com**
- Lớp: L2B
- Link repo bài làm: [github.com/MHuy21/K5-L2B-Day02-DuongManhHuy-2A202603301-ObjectDetection.git](https://github.com/MHuy21/K5-L2B-Day02-DuongManhHuy-2A202603301-ObjectDetection.git)
- Link Colab đã chạy: [colab.research.google.com/github/MHuy21/K5-L2B-Day02-DuongManhHuy-2A202603301-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb](https://colab.research.google.com/github/MHuy21/K5-L2B-Day02-DuongManhHuy-2A202603301-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb)

## 2. Schema và mini guideline

Guideline chi tiết: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema 4 lớp theo `data.yaml`: `0 car`, `1 truck`, `2 bus`, `3 van`.

**Cách áp dụng schema:**

- Box chỉ ôm phần thân xe nhìn thấy được, không lấn mặt đường, bóng xe hay xe bên cạnh; mỗi xe một box riêng.
- Xe bị che một phần hoặc bị cắt ở rìa ảnh vẫn gán nhãn nếu phần còn lại đủ nhận ra lớp; box chạm đúng mép ảnh, không đoán phần ngoài khung.
- Xe quá nhỏ hoặc bị che gần hết, không chắc lớp thì không tự đoán.
- Không gán mô tô, xe ba bánh, người đi bộ, biển báo hay vật thể ngoài 4 lớp.

**Các ca mơ hồ đã quyết định:**

| Ảnh          | Tình huống                                                                              | Quyết định                                 |
| ------------- | ----------------------------------------------------------------------------------------- | --------------------------------------------- |
| `drive_025` | SUV đen ở làn xa bị hàng rào cây che phần thân dưới, chỉ thấy nóc và kính | `car`, box chỉ bao phần nhìn thấy       |
| `drive_027` | Xe van nhỏ dạng hộp (mini cargo van), không rõ chở hàng hay chở người           | `van`                                       |
| `drive_048` | Xe buýt khớp nối hai toa, dễ nhầm là hai xe                                         | `bus`, một box cho cả xe                  |
| `drive_014` | Xe cứu hộ có cần cẩu phía sau, cabin nhỏ giống xe con/van                         | `truck`                                     |
| `drive_011` | Xe ba bánh chở thùng hàng, dễ nhầm với `truck`                                   | Không gán (mô tô ba bánh, ngoài schema) |

**Phần cần giảng viên làm rõ:**

- File gold train và nhãn eval trong repo dùng thứ tự class ID `car, van, truck, bus`, khác với `data.yaml`/README (`car, truck, bus, van`). Bằng chứng: ở `drive_008`, gold ghi xe tải ben là `2`, xe buýt lớn là `3`, xe van là `1`; trong [val_batch0_labels.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_labels.jpg), xe buýt lớn ở `drive_052` được nhãn eval ghi là `van`.
- Ngưỡng "xe rất nhỏ ở xa" có cần gán nhãn hay không: phần lớn box của tôi không khớp gold nằm ở vùng xa phía trên ảnh.

## 3. Annotation và self-QC

- Số file nhãn train: 50/50 (`dataset/labels/train/drive_001.txt` → `drive_050.txt`), tổng 999 box: car 800, truck 21, bus 105, van 73.
- Kết quả checker và lỗi đã xử lý: `tools/check_yolo_labels.py` ĐẠT cấu trúc 50/50.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_003`, `drive_026`, `drive_038`, `drive_039`, `drive_030`, `drive_012`, `drive_022`, `drive_042`, `drive_047`, `drive_013` .
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: không phát hiện lỗi bỏ sót.
- Commit khóa nhãn ban đầu: **`8001ee1`**

## 4. Gold QC và rework

| Chỉ số            | Trước sửa | Sau sửa nếu có |
| ------------------- | -----------: | ----------------: |
| TP                  |          432 |                — |
| FP                  |          567 |                — |
| FN                  |          221 |                — |
| Precision           |       0.4324 |                — |
| Recall              |       0.6616 |                — |
| F1                  |        0.523 |                — |
| mean_iou_of_matches |       0.7514 |                — |
| passed              |        False |                — |

Kết quả chi tiết: [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json). File gold train không được commit vào repo.

**Không rework.** F1 0.523 thấp hơn ngưỡng 0.80 trong RUBRIC, nhưng lab coach đã thông báo chấp nhận F1 > 0.5; mean IoU 0.7514 ≥ 0.70 cho thấy các box khớp được đủ sát. Nhãn giữ nguyên bản đã khóa ở commit `8001ee1`.

Phân tích FP/FN (đã đối chiếu ảnh, không chỉ dựa trên số đếm):

- **Lệch thứ tự class ID của gold** (bằng chứng ở mục 2): không có box `truck`/`bus`/`van` nào của tôi trùng lớp với gold dù trùng vị trí — gold `van` ↔ tôi `bus` (53 box), gold `truck` ↔ tôi `van` (37), gold `bus` ↔ tôi `truck` (17).
- **Thừa box xe nhỏ ở xa**: tôi vẽ 999 box, gold có 653. Box không khớp tập trung ở 20% phía trên ảnh (vùng xa), nhiều nhất ở `drive_003`, `drive_002`, `drive_030`, `drive_031`; vùng gần ở nửa dưới ảnh gần như khớp. Đây là khác biệt về ngưỡng gán nhãn xe nhỏ ở xa, đã nêu ở mục 2 để giảng viên làm rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab, GPU Tesla T4 (15 GB); Ultralytics 8.4.208, Torch 2.14.1+cu130, Python 3.13.16. Train 50 epoch trong ~0.025 giờ (~1.5 phút), AutoBatch chọn batch 44.
- Các tham số thay đổi so với notebook nếu có và lý do: không thay đổi — `yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909` (theo [args.yaml](assets/yolo26_runs/vehicle_annotation/args.yaml)).
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không commit file `.pt`).

| Chỉ số trên 10 ảnh eval | Giá trị |
| --------------------------- | --------: |
| Precision                   |    0.4105 |
| Recall                      |    0.2303 |
| mAP50                       |    0.2723 |
| mAP50–95                   |    0.2017 |

Kết quả chi tiết:

- Ảnh dự đoán trên 10 ảnh eval: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/) (ví dụ [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg), [drive_058.jpg](assets/eval_predictions/visuals/drive_058.jpg)).
- Log train theo epoch: [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [results.png](assets/yolo26_runs/vehicle_annotation/results.png).
- Đánh giá trên eval: [confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [BoxPR_curve.png](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png), nhãn eval [val_batch0_labels.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_labels.jpg) và dự đoán [val_batch0_pred.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_pred.jpg).

Kết quả theo lớp trên 10 ảnh eval (log `best.pt` trong notebook):

| Lớp  | Số box eval | Precision | Recall | mAP50 | mAP50–95 |
| ----- | -----------: | --------: | -----: | ----: | --------: |
| car   |           94 |     0.833 |  0.638 | 0.739 |     0.523 |
| truck |            5 |     0.675 |  0.200 | 0.239 |     0.206 |
| bus   |            2 |     0.000 |  0.000 | 0.008 |     0.004 |
| van   |           12 |     0.134 |  0.083 | 0.103 |     0.074 |

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**

   Theo tên lớp trong `data.yaml`, `bus` (0/2) và `van` (0.083, 1/12) có recall thấp nhất; `truck` 0.200, `car` 0.638. Minh họa: [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg) — model khoanh xe buýt hai tầng ở giữa ảnh là `bus 0.51` (đúng với thực tế), nhưng nhãn eval ghi xe này là `van` ([val_batch0_labels.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_labels.jpg)), nên bị tính sai ở cả hai lớp. Confusion matrix cho thấy 11/12 box nhãn `van` bị dự đoán `bus` ⇒ recall thấp của `bus`/`van` chủ yếu do nhãn eval dùng thứ tự class ID khác `data.yaml` (xem mục 2). Lỗi thực sự của model thấy rõ nhất ở `truck`: 4/5 xe tải bị nhận thành `car`.
2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**

   Khi đó model tìm đúng vị trí xe nhưng box chưa khớp chặt ở IoU cao, cần kiểm mép box (lỏng hay chật, phần xe bị che, xe bị cắt ở rìa ảnh) và độ nhất quán cách vẽ box trong nhãn train. Ở lần chạy này mức chênh không quá lớn: tổng 0.272 → 0.202, `car` 0.739 → 0.523. Nhãn train của tôi so với gold có mean IoU 0.75, tức chính nhãn người cũng lệch nhau ở mức này, nên model khó khớp tốt ở IoU 0.85–0.95. Có thể kiểm bằng cách so [val_batch0_pred.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_pred.jpg) với [val_batch0_labels.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_labels.jpg).
3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**

   - Thứ tự class ID: guideline và `data.yaml` quy định `car, truck, bus, van`, nhưng nhãn eval và gold dùng `car, van, truck, bus`, nên xe buýt thật bị chấm thành `van`. Dữ kiện: 11/12 box `van` bị dự đoán `bus`; ví dụ `drive_052`.
   - `truck` ↔ `car`: 4/5 xe tải bị dự đoán `car`. Guideline xếp xe bán tải/cabin nhỏ vào `car`, nên ranh giới xe tải nhỏ – xe con dễ lẫn; thêm vào đó tập train chỉ có 21 box `truck` so với 800 box `car` (mất cân bằng lớp). Với chỉ 5 xe tải trong eval, nhận xét này cần kiểm thêm, chưa đủ để kết luận.
4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**

   `data.yaml` đặt `val: images/eval`: 10 ảnh này dùng để đo metric sau mỗi epoch và chọn `best.pt`. Nếu đưa chúng vào train, model đã thấy chính ảnh dùng để đánh giá, metric sẽ bị thổi phồng và không còn đo được khả năng tổng quát trên ảnh mới. Vì đã được dùng để chọn `best.pt`, tập eval cũng không phải test set hoàn toàn độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm           | Lỗi quan sát                                                                   | Cách đã thử                                                                                                                                                                                  | Kết quả/còn thiếu                                                                                                          |
| ----------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| CP5 – chấm với gold        | F1 0.523 < 0.80; không box `truck`/`bus`/`van` nào trùng lớp với gold | Ghép box không xét lớp và đối chiếu ảnh `drive_008` → gold dùng thứ tự `car, van, truck, bus`                                                                                   | Lab coach thông báo chấp nhận F1 > 0.5 nên không rework                                                                 |
| CP6 – đánh giá trên eval | mAP của `bus`/`van` gần 0; 11/12 box nhãn `van` bị dự đoán `bus`  | Xem [val_batch0_labels.jpg](assets/yolo26_runs/vehicle_eval/val_batch0_labels.jpg): xe buýt lớn ở `drive_052` được nhãn eval ghi `van` → nhãn eval cùng lệch thứ tự ID như gold | Ghi nhận trong report (mục 2, 5), không sửa nhãn eval; metric `bus`/`van` chưa phản ánh đúng chất lượng model |

## 7. Tự đánh giá theo rubric

| Tiêu chí (điểm)                           | Bằng chứng                                                                                                                     | Trạng thái                                                                                                                          |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Hoàn thành và định dạng YOLO (10)       | 50/50 file, `check_yolo_labels.py` ĐẠT; class ID theo `data.yaml`                                                          | Đã có kết quả                                                                                                                    |
| Đầy đủ vật thể (20)                     | Recall so với gold 0.6616 (≈ 0.83 nếu đổi ID gold về đúng schema); rà tay 10 ảnh không thấy sót                     | Một phần — chưa kiểm được chính xác vì gold lệch thứ tự ID; vẫn có xe bị sót (ví dụ `drive_041`, `drive_027`) |
| Độ bám sát và hình học box (20)        | mean IoU 0.7514 ≥ 0.70                                                                                                          | Đạt ngưỡng "Đạt", chưa tới mức "Xuất sắc" (≥ 0.80)                                                                        |
| Phân lớp nhất quán (15)                   | Nhãn theo đúng schema `data.yaml`; không gán mô tô/xe ba bánh (ví dụ `drive_011`)                                  | Chưa kiểm được bằng gold do lệch thứ tự ID; xe nhỏ ở xa có thể bị gán thừa so với gold                               |
| Mini guideline và quyết định mơ hồ (10) | `GUIDELINE_MINI.md`: định nghĩa 4 lớp, 4 quy tắc box, 5 ca mơ hồ có lý do                                             | Đã có kết quả                                                                                                                    |
| Gold-set annotation QC (15)                   | F1 0.523, mean IoU 0.7514, `outputs/annotation_evaluation.json`; không rework; phân tích nguyên nhân ở mục 4            | Chưa đạt ngưỡng F1 ≥ 0.80 của rubric; giảng viên thông báo chấp nhận F1 > 0.5                                            |
| Pipeline YOLO và phân tích (10)            | Colab chạy hết (link ở mục 1); notebook kiểm "eval không đi vào train"; có metrics, ảnh eval và trả lời 4 câu hỏi | Đã có kết quả                                                                                                                    |

**Điều kiện bắt buộc:**

- Đủ 50 file nhãn và checker đạt → không bị giới hạn 49 điểm.
- Đã chạy gold-set evaluation → không bị giới hạn 69 điểm.
- Không dùng eval để train, không sửa gold hay nhãn eval, không truy vết nhãn ngoài repo.
- mAP thấp (0.272) nhưng giữ đúng split và đã phân tích nguyên nhân ở mục 5.

**Còn thiếu/chưa kiểm được:** thứ tự class ID của gold/eval.

## 8. Công cụ AI và tài liệu tham khảo

**Công cụ AI:** Claude Code (Anthropic), dùng trong VS Code.

| Bước | AI đã làm | Tôi tự làm / tự kiểm tra |
| --- | --- | --- |
| Gán nhãn (CP2) | Không gán nhãn; chỉ hướng dẫn dùng CVAT và cách export YOLO | Tự gán nhãn toàn bộ 50 ảnh trên CVAT (local) |
| Train/eval (CP6) | Hướng dẫn chạy Colab; chép kết quả vào `assets/` (bỏ file `.pt`); đọc metrics, confusion matrix, kiểm notebook trên GitHub có output | Tự chạy notebook trên Colab, tải kết quả, lưu notebook lên GitHub |

**Tài liệu/công cụ ngoài repo:** CVAT (gán nhãn), Google Colab (GPU T4), Ultralytics YOLO26 (`yolo26n.pt`). Không dùng nguồn dữ liệu hay nhãn nào ngoài repo. Không ghi mật khẩu/token; không đính kèm gold train.
