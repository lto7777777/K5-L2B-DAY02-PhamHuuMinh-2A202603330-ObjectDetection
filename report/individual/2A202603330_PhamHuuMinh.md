# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Phạm Hữu Minh
- MSSV: 2A202603330
- Lớp: L2B
- Link repo bài làm: https://github.com/lto7777777/K5-L2B-DAY02-PhamHuuMinh-2A202603330-ObjectDetection
- Link Colab đã chạy: «ĐIỀN sau khi chạy notebook»

## 2. Schema và mini guideline

Định nghĩa bốn lớp và quy tắc box nằm trong [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema giữ đúng thứ tự `car=0`, `truck=1`, `bus=2`, `van=3` theo `data.yaml`; không thêm lớp, không gán người, xe máy, xe đạp, biển báo hay phản chiếu trong kính.

Ba ca mơ hồ đã quyết định:

1. `drive_038`, box 57,3×89,0 điểm ảnh, góc trên-trái ở (2, 178), nằm sát lề trái ảnh. Phân vân `car` / `van`, chọn `van` vì thân hộp nhỏ và kín đúng mô tả lớp `van`; không phải `truck` vì không có khoang hàng tách rời khỏi cabin.
2. `drive_038`, box 212,7×155,4 điểm ảnh, góc trên-trái ở (270, 343), xe lớn nhất trong ảnh. Cũng phân vân `car` / `van`, chọn `car` vì xe không có thân hộp cao kín như mô tả lớp `van`.
3. `drive_034`, `drive_038`, `drive_046`: xe cẩu của công an xuất hiện trong cả ba ảnh. Phân vân `car` hay `truck`; em loại `van` vì xe không có thân hộp kín liền khối, rồi chọn `car` theo phần thân nhìn thấy. Em ghi nhận lập luận ngược ngay trong guideline: tiêu chí của lớp `truck` gồm "thiết bị chuyên dụng" và cần cẩu đúng là thiết bị chuyên dụng, nên ca này có thể phải là `truck`. Đây là ca em xin Lab Coach quyết định, theo yêu cầu của `GUIDE.md` mục 1.

Cách áp dụng schema: tiêu chí phân biệt `car` / `van` là dáng thân hộp cao và kín, không phải kích thước box. Hai ca trên lệch nhau rất nhiều về kích thước (57,3×89,0 so với 212,7×155,4) nhưng vẫn tách lớp được bằng dáng thân.

Phần cần Lab Coach làm rõ: `GUIDE.md` mục 1 yêu cầu không tự quyết ca `van` mơ hồ với `bus` hoặc `car` mà phải ghi case và hỏi. Em đã ghi quyết định để áp dụng nhất quán trong lúc gán nhãn, và xin Lab Coach xác nhận ranh giới `car` / `van`, vì bảng định nghĩa chỉ mô tả bằng lời chứ không có ngưỡng đo được.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file, đúng một file cho mỗi ảnh, không có file rỗng vì mọi ảnh đều có ít nhất một xe thuộc schema. Tổng 958 box: 811 `car`, 12 `truck`, 96 `bus`, 39 `van`.
- Kết quả checker và lỗi đã xử lý: chạy `python tools/check_yolo_labels.py` từ thư mục gốc repo, kết quả "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ". Không có lỗi định dạng nào phải xử lý: đủ 50 file đúng tên, mỗi dòng đúng 5 cột, ID lớp trong 0-3, toạ độ chuẩn hoá và không box nào vượt biên ảnh.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_009`, `drive_045`, `drive_036`, `drive_026`, `drive_003`, `drive_030`, `drive_043`, `drive_038`, `drive_034`, `drive_046`. Em rà trên ảnh dựng sẵn đặt nhãn của em cạnh nhãn gold, mỗi lớp một màu giống nhau ở hai bên nên lệch lớp và box thừa nhìn ra ngay.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: lỗi lớn nhất là gán thừa — em vẽ 958 box so với 653 box của gold, phần thừa gần như chỉ ở lớp `car` và phần lớn là xe ở xa, cạnh ngắn dưới 40 điểm ảnh. `drive_036` không có box nào khớp gold. Em đã báo Lab Coach và được trả lời "thừa annotate không sao", nên giữ nguyên bộ nhãn thay vì xoá bớt, và ghi lại ở đây để người chấm thấy rõ.
- Commit khóa nhãn ban đầu: «ĐIỀN hash commit»

Ghi chú về bốn ảnh `drive_008`, `drive_022`, `drive_033`, `drive_038`: bốn ảnh này em đã gán nhãn một lần ở buổi làm nháp trước, và lần này gán lại từ đầu trong cùng task CVAT với 46 ảnh còn lại. Toàn bộ 50 file nhãn nộp đều lấy từ một bản xuất duy nhất của task đó. So sánh bản nháp cũ với bản mới của bốn ảnh: thứ tự lớp không đổi (61 so với 62 `car`, 4 `truck`, 9 `bus`, 6 `van`), chênh một box `car` mà bản mới bắt thêm ở `drive_008`; các box khác lệch toạ độ nhỏ do vẽ lại.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 381 | |
| FP | 577 | |
| FN | 272 | |
| Precision | 0,3977 | |
| Recall | 0,5835 | |
| F1 | 0,4730 | |
| mean_iou_of_matches | 0,6719 | |
| passed | false | |

Đã chạy `python tools/evaluate_annotations.py --student-labels dataset/labels/train --gold-labels gold_train_labels`; kết quả lưu ở `outputs/annotation_evaluation.json`. Ngưỡng đi tiếp: `F1 >= 0,80` tại IoU 0,50 và `mean_iou_of_matches >= 0,70`. Lần chạy đầu CHƯA ĐẠT cả hai ngưỡng.

Lưu ý quan trọng về thứ tự lớp: bộ gold dùng `1 = van, 2 = truck, 3 = bus`, trong khi `data.yaml` và `README.md` của repo quy định `1 = truck, 2 = bus, 3 = van`. Vì `evaluate_annotations.py` chỉ ghép box cùng ID lớp, điểm ở bảng trên bị hạ do lệch thứ tự chứ không chỉ do em gán sai. Em giữ nguyên thứ tự của `data.yaml` trong nhãn của mình vì đó là schema bắt buộc của bài và `RULES.md` cấm đổi thứ tự ID lớp. Khi đổi ID của gold sang thứ tự `data.yaml` rồi chấm lại, kết quả là TP 461, FP 497, FN 192, precision 0,4812, recall 0,7060, F1 0,5723, mean IoU 0,6852 — vẫn chưa đạt, nhưng cao hơn rõ rệt. Em đã báo Lab Coach và xin xác nhận nên chấm theo bản nào.

Số liệu để biết nên mở lại ảnh nào (tính sau khi đã khớp thứ tự lớp): em vẽ 958 box so với 653 box của gold — car 811/533, truck 12/20, bus 96/54, van 39/46. Phần lệch gần như chỉ ở `car`. Trong 497 FP có 397 box gần như không trùng box nào của gold, 72 box cùng lớp chỉ đạt IoU 0,3-0,5, và 28 box sai lớp thật sự. 60% số box không trùng có cạnh ngắn dưới 40 điểm ảnh.

`GUIDE.md` mục 4 yêu cầu không suy nguyên nhân chỉ từ FP/FN nên em mở ảnh kiểm trước khi kết luận. Trên ảnh so sánh, phần lớn box thừa nằm ở làn xa và ở rìa ảnh, là xe mà em vẫn vẽ dù không đủ chi tiết để chắc chắn thuộc lớp nào — đúng trường hợp `GUIDE.md` mục 1 bảo không gán. Nhóm thứ hai là box hơi rộng hơn thân xe, làm IoU rơi xuống khoảng 0,3-0,5 nên không được tính khớp. Quyết định: giữ nguyên bộ nhãn, không rework, vì Lab Coach trả lời "thừa annotate không sao". Em ghi rõ ở đây là bộ nhãn nộp chưa đạt ngưỡng tự động của `evaluate_annotations.py` để người chấm không hiểu nhầm.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: «ĐIỀN»
- Các tham số thay đổi so với notebook nếu có và lý do: «ĐIỀN, ghi "không thay đổi" nếu giữ nguyên»
- Đường dẫn best checkpoint trong phiên chạy: «ĐIỀN»

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | |
| Recall | |
| mAP50 | |
| mAP50–95 | |

Ảnh và chỉ số: «ĐIỀN đường dẫn tương đối tới `assets/eval_predictions/visuals/...` và `assets/yolo26_runs/...` sau khi giải nén `day2_colab_outputs.zip`». Nhận xét một ảnh và một vật thể cụ thể: «ĐIỀN».

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa? «ĐIỀN»
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box? «ĐIỀN»
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét? «ĐIỀN»
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì? «ĐIỀN»

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Trước CP1 | Làm theo repo đề bài Ngày 2 của khóa trước (`Day2-TrackData-ObjectDetection-Student`) thay vì repo `K5-L2-Day02-ObjectDetection` | Đối chiếu hai repo: commit gốc của bản đang làm thuộc repo khóa trước, còn repo K5-L2 mới được tạo trong ngày lab | Chuyển sang repo đúng; giữ lại nhãn của 4 ảnh trùng tên và trùng SHA-256 |
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
| Gold-set annotation QC | 15 | Đã chạy evaluator, kết quả `passed: false` (F1 0,4730; mean IoU 0,6719). Không rework vì Lab Coach chấp nhận phần gán thừa |
| Pipeline YOLO và phân tích | 10 | Chưa chạy Colab |

## 8. Công cụ AI và tài liệu tham khảo

Công cụ AI đã dùng: Claude (Claude Code).

- Xác định repo đề bài nào là bài thật: Claude đối chiếu commit gốc, ngày tạo repo và danh sách repo của tổ chức. Em tự kiểm lại bằng cách mở cả hai repo trên GitHub và so với thông báo của lớp.
- Đối chiếu bốn ảnh của buổi nháp với bộ train của bài: Claude tính SHA-256 và so thứ tự lớp của bản xuất YOLO với `data.yaml`. Em tự kiểm lại số box theo lớp (61/4/9/6, tổng 80) khớp với báo cáo buổi nháp.
- Soạn câu chữ cho `GUIDELINE_MINI.md`: định nghĩa lớp và hai ca mơ hồ là quyết định của em từ buổi gán nhãn trước, Claude diễn đạt lại thành bảng theo mẫu. Em đọc lại toàn bộ và chịu trách nhiệm về nội dung.
- Nhãn của cả 50 ảnh: em tự gán trong CVAT, không dùng AI sinh nhãn. Claude chỉ kiểm định dạng bản xuất (số file, số cột, ID lớp, toạ độ chuẩn hoá, box trong biên ảnh) và đếm box theo lớp.
- Chạy `check_yolo_labels.py` và `evaluate_annotations.py`, đọc kết quả JSON.
- Phân tích FP/FN: tách số box thừa, box lỏng và box sai lớp; phát hiện bộ gold dùng thứ tự lớp khác `data.yaml` và chấm lại sau khi quy về cùng thứ tự. Em tự kiểm lại bằng cách mở `gold_train_labels/drive_009.txt`: ba box lớn nhất đều mang ID 3, và trên ảnh đó là ba chiếc xe buýt.
- Dựng ảnh so sánh nhãn của em với gold cho cả 50 ảnh để rà bằng mắt.
- Soạn bản nháp báo cáo này theo số liệu thật của các lần chạy. Em đọc lại toàn bộ, sửa chỗ cần sửa và chịu trách nhiệm về nội dung.

Tài liệu ngoài repo đã dùng: không, trừ tài liệu CVAT của buổi làm nháp trước (`docs/cvat-setup.md` và `docs/cvat-step-by-step.md` trong repo bài nháp) để nhớ lại thao tác xuất nhãn YOLO.
