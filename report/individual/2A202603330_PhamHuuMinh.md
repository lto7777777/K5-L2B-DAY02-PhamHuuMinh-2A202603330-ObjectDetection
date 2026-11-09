# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Phạm Hữu Minh
- MSSV: 2A202603330
- Lớp: L2B
- Link repo bài làm: https://github.com/lto7777777/K5-L2B-DAY02-PhamHuuMinh-2A202603330-ObjectDetection
- Link Colab đã chạy: không có. Colab không cấp được backend (báo "Failed to assign a backend", đầu tiên là hết GPU, sau đó hết cả backend CPU), nên em chạy notebook trên máy cá nhân sau khi được Lab Coach đồng ý. Chi tiết ở mục 6.

## 2. Schema và mini guideline

Định nghĩa bốn lớp và quy tắc box nằm trong [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema giữ đúng thứ tự `car=0`, `truck=1`, `bus=2`, `van=3` theo `data.yaml`; không thêm lớp, không gán người, xe máy, xe đạp, biển báo hay phản chiếu trong kính.

Ba ca mơ hồ đã quyết định:

1. `drive_038`, box 57,3×89,0 điểm ảnh, góc trên-trái ở (2, 178), nằm sát lề trái ảnh. Phân vân `car` / `van`, chọn `van` vì thân hộp nhỏ và kín đúng mô tả lớp `van`; không phải `truck` vì không có khoang hàng tách rời khỏi cabin.
2. `drive_038`, box 212,7×155,4 điểm ảnh, góc trên-trái ở (270, 343), xe lớn nhất trong ảnh. Cũng phân vân `car` / `van`, chọn `car` vì xe không có thân hộp cao kín như mô tả lớp `van`.
3. `drive_034`, `drive_038`, `drive_046`: xe cẩu của công an xuất hiện trong cả ba ảnh. Phân vân `car` hay `truck`; em loại `van` vì xe không có thân hộp kín liền khối, rồi chọn `car` theo phần thân nhìn thấy. Em ghi nhận lập luận ngược ngay trong guideline: tiêu chí của lớp `truck` gồm "thiết bị chuyên dụng" và cần cẩu đúng là thiết bị chuyên dụng, nên ca này có thể phải là `truck`. Đây là ca em xin Lab Coach quyết định, theo yêu cầu của `GUIDE.md` mục 1.

Cách áp dụng schema: tiêu chí phân biệt `car` / `van` là dáng thân hộp cao và kín, không phải kích thước box. Hai ca trên lệch nhau rất nhiều về kích thước (57,3×89,0 so với 212,7×155,4) nhưng vẫn tách lớp được bằng dáng thân.

Phần cần Lab Coach làm rõ: `GUIDE.md` mục 1 yêu cầu không tự quyết ca `van` mơ hồ với `bus` hoặc `car` mà phải ghi case và hỏi. Em đã ghi quyết định để áp dụng nhất quán trong lúc gán nhãn, và xin Lab Coach xác nhận ranh giới `car` / `van`, vì bảng định nghĩa chỉ mô tả bằng lời chứ không có ngưỡng đo được.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file, đúng một file cho mỗi ảnh, không có file rỗng. Bản nộp sau rework có 772 box: 634 `car`, 12 `truck`, 91 `bus`, 35 `van` (bản trước rework là 958 box: 811/12/96/39).
- Kết quả checker và lỗi đã xử lý: chạy `python tools/check_yolo_labels.py` từ thư mục gốc repo, kết quả "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ". Không có lỗi định dạng nào phải xử lý: đủ 50 file đúng tên, mỗi dòng đúng 5 cột, ID lớp trong 0-3, toạ độ chuẩn hoá và không box nào vượt biên ảnh.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_009`, `drive_045`, `drive_036`, `drive_026`, `drive_003`, `drive_030`, `drive_043`, `drive_038`, `drive_034`, `drive_046`. Em rà trên ảnh dựng sẵn đặt nhãn của em cạnh nhãn gold, mỗi lớp một màu giống nhau ở hai bên nên lệch lớp và box thừa nhìn ra ngay.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: lỗi lớn nhất là gán thừa — em vẽ 958 box so với 653 box của gold, phần thừa gần như chỉ ở lớp `car` và phần lớn là xe ở xa, cạnh ngắn dưới 40 điểm ảnh. `drive_036` không có box nào khớp gold. Em đã báo Lab Coach và được trả lời "thừa annotate không sao", nên giữ nguyên bộ nhãn thay vì xoá bớt, và ghi lại ở đây để người chấm thấy rõ.
- Commit khóa nhãn ban đầu: `a50100d` — "Lock 50-image YOLO label set, mini guideline and gold QC result", trên nhánh `main` của repo bài làm.

Ghi chú về bốn ảnh `drive_008`, `drive_022`, `drive_033`, `drive_038`: bốn ảnh này em đã gán nhãn một lần ở buổi làm nháp trước, và lần này gán lại từ đầu trong cùng task CVAT với 46 ảnh còn lại. Toàn bộ 50 file nhãn nộp đều lấy từ một bản xuất duy nhất của task đó. So sánh bản nháp cũ với bản mới của bốn ảnh: thứ tự lớp không đổi (61 so với 62 `car`, 4 `truck`, 9 `bus`, 6 `van`), chênh một box `car` mà bản mới bắt thêm ở `drive_008`; các box khác lệch toạ độ nhỏ do vẽ lại.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 381 | 361 |
| FP | 577 | 411 |
| FN | 272 | 292 |
| Precision | 0,3977 | 0,4676 |
| Recall | 0,5835 | 0,5528 |
| F1 | 0,4730 | **0,5067** |
| mean_iou_of_matches | 0,6719 | 0,6737 |
| passed | false | false |

Đã chạy `python tools/evaluate_annotations.py --student-labels dataset/labels/train --gold-labels gold_train_labels`; kết quả lưu ở `outputs/annotation_evaluation.json`. Ngưỡng đi tiếp: `F1 >= 0,80` tại IoU 0,50 và `mean_iou_of_matches >= 0,70`. Lần chạy đầu CHƯA ĐẠT cả hai ngưỡng.

Lưu ý quan trọng về thứ tự lớp: bộ gold dùng `1 = van, 2 = truck, 3 = bus`, trong khi `data.yaml` và `README.md` của repo quy định `1 = truck, 2 = bus, 3 = van`. Vì `evaluate_annotations.py` chỉ ghép box cùng ID lớp, điểm ở bảng trên bị hạ do lệch thứ tự chứ không chỉ do em gán sai. Em giữ nguyên thứ tự của `data.yaml` trong nhãn của mình vì đó là schema bắt buộc của bài và `RULES.md` cấm đổi thứ tự ID lớp. Khi đổi ID của gold sang thứ tự `data.yaml` rồi chấm lại, kết quả là TP 461, FP 497, FN 192, precision 0,4812, recall 0,7060, F1 0,5723, mean IoU 0,6852 — vẫn chưa đạt, nhưng cao hơn rõ rệt. Em đã báo Lab Coach và xin xác nhận nên chấm theo bản nào.

Số liệu để biết nên mở lại ảnh nào (tính sau khi đã khớp thứ tự lớp): em vẽ 958 box so với 653 box của gold — car 811/533, truck 12/20, bus 96/54, van 39/46. Phần lệch gần như chỉ ở `car`. Trong 497 FP có 397 box gần như không trùng box nào của gold, 72 box cùng lớp chỉ đạt IoU 0,3-0,5, và 28 box sai lớp thật sự. 60% số box không trùng có cạnh ngắn dưới 40 điểm ảnh.

`GUIDE.md` mục 4 yêu cầu không suy nguyên nhân chỉ từ FP/FN nên em mở ảnh kiểm trước khi kết luận. Trên ảnh so sánh, phần lớn box thừa nằm ở làn xa và ở rìa ảnh, là xe mà em vẫn vẽ dù không đủ chi tiết để chắc chắn thuộc lớp nào — đúng trường hợp `GUIDE.md` mục 1 bảo không gán. Nhóm thứ hai là box hơi rộng hơn thân xe, làm IoU rơi xuống khoảng 0,3-0,5 nên không được tính khớp. Quyết định rework: Lab Coach yêu cầu nếu FP quá cao thì bớt bớt đi, và đặt mức chấp nhận là F1 >= 0,50. Em áp dụng đúng một quy tắc, lấy từ `GUIDE.md` mục 1 ("xe quá nhỏ/mờ tới mức không phân được lớp thì không gán"): xoá mọi box có cạnh ngắn dưới 32 điểm ảnh trên ảnh 640x640. Quy tắc này chỉ dựa trên nhãn của em, không dựa vào vị trí hay lớp của gold. Kết quả: xoá 186 box (958 xuống 772), F1 từ 0,4730 lên 0,5067, vượt mức 0,50 của Lab Coach. Hai ngưỡng tự động của `evaluate_annotations.py` (F1 0,80 và mean IoU 0,70) vẫn chưa đạt; em ghi rõ `passed: false` ở bảng trên. Em ghi rõ ở đây là bộ nhãn nộp chưa đạt ngưỡng tự động của `evaluate_annotations.py` để người chấm không hiểu nhầm.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: chạy trên máy cá nhân (không dùng Colab, lý do ở mục 6). GPU NVIDIA GeForce RTX 4060 Laptop (8 GB), Ultralytics 8.4.208, Torch 2.6.0+cu124. 50 epoch chạy hết 0,124 giờ (khoảng 7 phút 30 giây).
- Các tham số thay đổi so với notebook nếu có và lý do: giữ nguyên `yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`, `plots=True`. Chỉ thêm `workers=0` vì trên Windows, DataLoader của Ultralytics sinh tiến trình con và làm script lỗi `freeze_support`; tham số này chỉ đổi cách nạp dữ liệu, không đổi kết quả huấn luyện. Vì chạy ngoài Colab nên `data.yaml` dùng bản có đường dẫn tuyệt đối đặt ngoài repo, giữ `data.yaml` trong repo nguyên vẹn.
- Đường dẫn best checkpoint trong phiên chạy: `outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (5,5 MB; không commit vì `.gitignore` loại `*.pt`).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0,3517 |
| Recall | 0,2778 |
| mAP50 | 0,2718 |
| mAP50–95 | 0,1983 |

Kết quả theo từng lớp trên 10 ảnh eval (113 vật thể):

| Lớp | Số vật thể | P | R | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|
| car | 94 | 0,720 | 0,745 | 0,731 | 0,513 |
| truck | 5 | 0,463 | 0,200 | 0,234 | 0,202 |
| bus | 2 | 0,000 | 0,000 | 0,008 | 0,003 |
| van | 12 | 0,224 | 0,167 | 0,114 | 0,074 |

Ảnh dự đoán trên 10 ảnh eval: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/) (drive_051 tới drive_060). Biểu đồ và ma trận nhầm lẫn của phiên train: [assets/yolo26_runs/vehicle_annotation/](assets/yolo26_runs/vehicle_annotation/), gồm `confusion_matrix.png`, `BoxPR_curve.png`, `BoxF1_curve.png` và `results.csv`. Phần đánh giá riêng trên tập eval: [assets/yolo26_runs/vehicle_eval/](assets/yolo26_runs/vehicle_eval/).

Nhận xét một ảnh và một vật thể cụ thể: trong [assets/eval_predictions/visuals/drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg), chiếc SUV đỏ ở tiền cảnh được model gán `van` với độ tin cậy 0,99 — dự đoán chắc chắn nhất trong cả ảnh. Nhưng nhãn gold của `drive_051` chỉ có 8 `car` và 1 `bus`, không có `van` nào. Đây là lỗi lớp chứ không phải lỗi định vị: box ôm đúng chiếc xe, chỉ gán sai lớp. Cùng ảnh đó, chiếc `bus` mà gold đánh dấu không được model sinh ra box nào.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa? — `bus`, recall 0,000 (precision cũng 0,000, mAP50 0,008) trên 2 vật thể của tập eval. Hai ảnh duy nhất có `bus` trong nhãn gold là `drive_051` và `drive_058`; mở [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg) thì không có box `bus` nào được sinh ra. Cần nói thêm hai điều để không đọc sai con số: mẫu quá nhỏ (2 vật thể) nên recall 0 chỉ là "trượt cả hai", và ở chiều ngược lại model lại sinh box `bus` ở `drive_052`, `drive_053`, `drive_056`, `drive_060` là những ảnh gold không có `bus` — tức model có học khái niệm `bus` nhưng học lệch.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box? — Phải kiểm độ bám sát của box, không phải việc tìm ra vật thể. mAP50 chỉ cần IoU 0,50, còn mAP50–95 lấy trung bình tới IoU 0,95, nên khoảng cách giữa hai số chính là phần box lệch mép. Lớp `car` của em là ví dụ rõ: mAP50 0,731 nhưng mAP50–95 0,513, tụt 0,218. Điều này khớp với chất lượng nhãn train: `mean_iou_of_matches` của em so với gold chỉ 0,6737, nghĩa là ngay cả box khớp cũng rộng hơn thân xe, và model học lại đúng thói quen vẽ rộng đó.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét? — Ranh giới `car` / `van`, và việc dùng `bus` quá tay. Dữ kiện: (a) `drive_051` có SUV bị gán `van` 0,99 trong khi gold không có `van` nào ở ảnh đó; (b) `van` chỉ đạt P 0,224 / R 0,167 còn `car` đạt 0,720 / 0,745, tức model lẫn ở đúng cặp lớp mà guideline của em mô tả bằng lời ("thân hộp cao, kín") chứ không có ngưỡng đo được; (c) trước rework em gán 96 box `bus` trong khi gold chỉ có 54, nên tập train dạy model một khái niệm `bus` rộng hơn định nghĩa của bài; (d) ca mơ hồ thứ ba trong mục 2 (xe cẩu công an) em chọn `car` còn gold ghi `truck`. Nói ngắn gọn: chỗ guideline mô tả mơ hồ là đúng chỗ model sai.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì? — Để tránh rò rỉ dữ liệu (data leakage). Nếu ảnh eval đi vào train thì model đã nhìn thấy đáp án, chỉ số đo được sẽ cao giả tạo và không còn nói lên khả năng tổng quát hoá. Trong notebook, 10 ảnh eval được khai là `val` trong `data.yaml`, nên ngoài việc báo cáo chỉ số cuối chúng còn được dùng để chọn `best.pt` qua từng epoch. Vì vậy đây không phải test set độc lập: chỉ số trên nó đã lạc quan sẵn một chút do checkpoint được chọn theo chính nó, và nếu còn dùng để train nữa thì con số sẽ mất hết ý nghĩa.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Trước CP1 | Làm theo repo đề bài Ngày 2 của khóa trước (`Day2-TrackData-ObjectDetection-Student`) thay vì repo `K5-L2-Day02-ObjectDetection` | Đối chiếu hai repo: commit gốc của bản đang làm thuộc repo khóa trước, còn repo K5-L2 mới được tạo trong ngày lab | Chuyển sang repo đúng; giữ lại nhãn của 4 ảnh trùng tên và trùng SHA-256 |
| CP6, lúc train | Colab báo "Failed to assign a backend": lần đầu không còn GPU, thử lại thì không còn backend nào | Hỏi Lab Coach và được đồng ý cho chạy trên máy cá nhân; chạy đúng các bước của notebook (train yolo26n, 50 epoch, imgsz 640, seed 20260909) trên GPU RTX 4060 Laptop | Train và đánh giá chạy xong trên máy cá nhân; không có link Colab, các số ở mục 5 lấy từ lần chạy này |
| CP5, sau khi nhận gold | Thứ tự lớp của gold (`1=van, 2=truck, 3=bus`) khác `data.yaml` của repo (`1=truck, 2=bus, 3=van`) | Chấm lại sau khi đổi ID của gold sang thứ tự `data.yaml` để tách phần lệch thứ tự ra khỏi phần em gán sai | F1 từ 0,4730 lên 0,5723; sai lớp thật sự chỉ còn 28 box thay vì 106. Giữ nguyên nhãn theo `data.yaml`, đã hỏi Lab Coach nên chấm theo bản nào |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md):

| Tiêu chí | Điểm | Tình trạng |
|---|---:|---|
| Hoàn thành và định dạng YOLO | 10 | Đủ 50 file, `check_yolo_labels.py` báo ĐẠT 50/50, không lỗi định dạng |
| Đầy đủ vật thể | 20 | Recall 0,7060 sau khi khớp thứ tự lớp; còn 192 box của gold chưa khớp. Em gán thừa chứ không thiếu là chính |
| Độ bám sát và hình học box | 20 | mean IoU của box khớp là 0,6852, dưới ngưỡng 0,70; 72 box đúng lớp nhưng IoU chỉ 0,3-0,5 do box rộng hơn thân xe |
| Phân lớp nhất quán | 15 | Sau khi khớp thứ tự lớp chỉ còn 28 box sai lớp trên 958 box. Giữ đúng thứ tự `data.yaml` cho toàn bộ 50 ảnh |
| Mini guideline và quyết định mơ hồ | 10 | Đã có định nghĩa bốn lớp, quy tắc box và đủ ba ca mơ hồ; ca thứ ba kèm lập luận ngược và câu hỏi cho Lab Coach |
| Gold-set annotation QC | 15 | Đã chạy evaluator hai lần. Trước rework F1 0,4730; sau khi xoá box nhỏ dưới 32 điểm ảnh còn 772 box, F1 0,5067 — vượt mức 0,50 Lab Coach đặt ra, nhưng `passed` vẫn là false theo ngưỡng 0,80 trong script |
| Pipeline YOLO và phân tích | 10 | Đã train và đánh giá xong trên máy cá nhân (mAP50 0,2718; mAP50–95 0,1983), có đủ ảnh dự đoán và biểu đồ trong `assets/`; còn phần trả lời bốn câu hỏi phân tích |

## 8. Công cụ AI và tài liệu tham khảo

Công cụ AI đã dùng: Claude (Claude Code).

- Chạy `check_yolo_labels.py` và `evaluate_annotations.py`, đọc kết quả JSON và tính các số liệu ở mục 4.
- Phân tích FP/FN: tách số box thừa, box lỏng và box sai lớp; phát hiện bộ gold dùng thứ tự lớp khác `data.yaml` và chấm lại sau khi quy về cùng thứ tự. Phần nhận xét và trả lời câu hỏi ở mục 5 dựa trên phân tích này. Em tự kiểm lại bằng cách mở `gold_train_labels/drive_009.txt`: ba box lớn nhất đều mang ID 3, và trên ảnh đó là ba chiếc xe buýt.
- Dựng ảnh so sánh nhãn của em với nhãn gold cho cả 50 ảnh để rà bằng mắt.
- Train và đánh giá YOLO26n trên máy cá nhân, lấy số liệu mục 5 từ `outputs/model_eval_metrics.json` và log của lần chạy.

Tài liệu ngoài repo đã dùng: không, trừ tài liệu CVAT của buổi làm nháp trước (`docs/cvat-setup.md` và `docs/cvat-step-by-step.md` trong repo bài nháp) để nhớ lại thao tác xuất nhãn YOLO.
