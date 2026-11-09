# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

Thứ tự lớp cố định theo `data.yaml`: `car=0`, `truck=1`, `bus=2`, `van=3`.

| Mã | Lớp | Gán khi thấy | Không phải lớp này |
| ---: | --- | --- | --- |
| 0 | `car` | sedan, hatchback, SUV, taxi, bán tải dùng như xe con | có thùng/ben rõ; thân xe buýt; van thân hộp |
| 1 | `truck` | thùng, ben, sàn chở hàng, thiết bị chuyên dụng | xe con; xe buýt; van kín một khối |
| 2 | `bus` | thân dài, nhiều cửa sổ hoặc hàng ghế | van nhỏ; xe tải |
| 3 | `van` | thân hộp nhỏ, kín, chở người hoặc hàng | thân xe buýt; khoang hàng tách rời như xe tải |

Phạm vi: chỉ bốn lớp trên. Không gán người, xe máy, xe đạp, biển báo, bóng phản chiếu. Một xe một box, không gộp nhiều xe. Xe quá nhỏ hoặc mờ tới mức không phân lớp được thì không gán và ghi lý do trong báo cáo.

## Quy tắc chung

- Box bám sát vật thể nghĩa là: bốn mép box trùng mép ngoài cùng của phần xe nhìn thấy, kể cả gương khi thấy rõ. Kiểm lại ở mức phóng 100%: không để lọt dải nền quanh xe, cũng không cắt mất phần xe đang thấy.
- Vật thể bị che khuất: chỉ vẽ phần nhìn thấy, không đoán phần bị xe khác hoặc cột, cây che. Vẫn gán box nếu phần nhìn thấy đủ để xác định lớp; nếu không đủ bằng chứng để chọn một trong bốn lớp thì bỏ và ghi lý do trong báo cáo.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán nếu phần còn trong ảnh đủ để phân lớp, box dừng đúng ở mép ảnh. Toạ độ chuẩn hoá phải nằm trong `[0, 1]`, không vượt biên.
- Vật thể rất nhỏ/mơ hồ: không gán khi mờ hoặc nhỏ tới mức không phân được lớp, và ghi lại quyết định đó. Mỗi xe một box, không gộp nhiều xe thành một box, không gán người, xe máy, xe đạp, biển báo hay bóng phản chiếu.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_038` | Xe nhỏ sát lề trái ảnh, box 57,3×89,0 điểm ảnh, góc trên-trái ở (2, 178). Phân vân `car` hay `van`. | `van` | Thân hộp nhỏ và kín, đúng mô tả lớp `van`. Không phải `truck` vì không có khoang hàng tách rời khỏi cabin. |
| `drive_038` | Xe lớn nhất trong ảnh, box 212,7×155,4 điểm ảnh, góc trên-trái ở (270, 343). Cũng phân vân `car` hay `van`. | `car` | Xe không có thân hộp cao, kín như mô tả lớp `van`; schema chỉ loại `car` khi xe là van thân hộp, nên giữ `car`. |
| `drive_034`, `drive_038`, `drive_046` | Xe cẩu của công an, xuất hiện trong cả ba ảnh. Phân vân `car` hay `truck`. | `car` | Loại `van` trước vì xe không có thân hộp kín liền khối. Chọn `car` theo phần thân nhìn thấy. Lập luận ngược đã ghi nhận: tiêu chí của lớp `truck` có "thiết bị chuyên dụng", và cần cẩu đúng là thiết bị chuyên dụng, nên ca này có thể phải gán `truck`. Xin Lab Coach quyết định. |

> Cần Lab Coach làm rõ: `GUIDE.md` mục 1 yêu cầu không tự quyết ca `van` mơ hồ với `bus` hoặc `car` mà phải ghi case và hỏi. Hai ca trong bảng trên đã ghi quyết định để áp dụng nhất quán trong lúc gán nhãn, nhưng vẫn xin Lab Coach xác nhận ranh giới `car` / `van`. Ca thứ ba cần Lab Coach quyết định riêng: xe chuyên dụng như xe cẩu thì tính theo thân xe nhìn thấy (`car`) hay theo tiêu chí "thiết bị chuyên dụng" của lớp `truck`.

> Ranh giới `car` / `van` là chỗ bảng định nghĩa chỉ mô tả bằng lời chứ không có ngưỡng đo được. Hai ca ở trên lệch nhau rất nhiều về kích thước (57,3×89,0 so với 212,7×155,4), nên tiêu chí dùng để quyết định là dáng thân hộp cao và kín, không phải kích thước box. Áp dụng tiêu chí này nhất quán cho cả 50 ảnh.
