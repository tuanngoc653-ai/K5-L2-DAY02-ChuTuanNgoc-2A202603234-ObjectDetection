# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe chở người cỡ nhỏ, khoảng 4–7 chỗ. Có nắp capo rõ, thân xe thấp. Gồm sedan, hatchback, SUV, taxi, bán tải. | Car: ô tô con mui thấp, dáng 3 khối (đầu, thân, cốp) có capo nhô ra phía trước, ví dụ Vios, Kia Morning, CX-5. |
| `truck` | Có cabin lái tách riêng với thùng hàng (thùng kín, thùng hở, bồn, ben). Thường cao, xe lớn có từ 2 trục bánh trở lên. | Truck: xe có cabin tách riêng khỏi thùng hàng hoặc thùng hở phía sau, ví dụ Hyundai Porter, Isuzu QKR. |
| `bus` | Xe dài, thân hình hộp, nhiều cửa sổ chạy dọc thân, có cửa lên xuống cho khách. Gồm xe buýt thành phố, buýt nối toa, xe khách. | Bus: xe chở nhiều khách, thân rất dài (khoảng 8 m trở lên), nóc cao, hàng cửa sổ liên tục, ví dụ Thaco Universe, xe buýt thành phố. |
| `van` | Thân một khối hộp liền (cabin và khoang sau không tách rời), đầu xe ngắn hoặc gần như phẳng. Cỡ xe nằm giữa car và bus. | Van: xe thân hộp liền khối, nóc cao, mặt trước gần thẳng đứng, không tách cabin khỏi thùng, ví dụ Ford Transit, Solati. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: khung hình chữ nhật vẽ vừa khít quanh vật thể, bốn cạnh chạm sát mép ngoài của vật thể, không rộng thừa và không bị cắt mất phần nào.
- Vật thể bị che khuất: nếu vẫn nhìn thấy đủ để biết là xe gì thì vẫn vẽ khung quanh phần nhìn thấy; nếu bị che gần hết, không biết là xe gì thì bỏ qua.
- Vật thể bị cắt bởi rìa ảnh: xe nằm lấp ló ở mép ảnh thì vẫn vẽ khung quanh phần nhìn thấy, cạnh khung trùng với mép ảnh; không vẽ khung tràn ra ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: nếu nhỏ nhưng vẫn nhận ra được là xe gì thì vẫn vẽ khung; nếu quá nhỏ, quá mờ, không chắc là xe gì thì bỏ qua, không đoán bừa.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_019` (cùng loại xe ở 014, 034, 038, 046) | Xe cứu hộ của công an màu trắng ở ngã tư trời mưa: cabin giống van, phía sau là bệ hở gắn cần cẩu và tời kéo | `truck` | Cabin phía trước tách rời phần thiết bị phía sau, khớp cụm "cabin lái tách riêng với thùng hàng" của truck (xe chuyên dụng chở thiết bị). Không phải van vì phía sau không có khoang kín chở người hay hàng, chỉ có thiết bị lộ ra ngoài. |
| `drive_006` | Xe nhỏ màu xanh, dáng cao, có giá nóc (góc phải) | `van` | Nóc cao, thân hộp liền khối từ đầu đến đuôi, xe ngắn, đầu xe gần như phẳng, không có capo dài; khớp cụm "minivan chở hàng hoặc chở người" của van. Không phải car vì car có nóc thấp, thân chia rõ capo, khoang khách và cốp. Không phải truck vì cabin và khoang sau liền một khối. |
| `drive_024`, `drive_027` | Minivan nhỏ màu be/bạc trong cùng cảnh đường mưa | `van` | Nóc cao, thân hộp, ngắn, có hàng kính dọc thân, đầu xe ngắn và gần như phẳng; hàng kính bên cho thấy khoang sau chở được người hoặc hàng, khớp "minivan chở hàng hoặc chở người". Không phải car vì cao và vuông hơn hẳn các sedan/taxi bên cạnh, không có cốp tách riêng. Không phải truck vì không có cabin tách rời thùng hàng. Hai ảnh có cùng loại xe nên gán cùng nhãn. |
| `drive_026` | Xe công an ở mép phải, nhìn từ phía sau: bệ hở gắn cần cẩu và tời kéo | `truck` | Cùng loại xe với `drive_019`: cabin tách rời phần thiết bị phía sau, khớp "cabin lái tách riêng với thùng hàng" của truck. Không phải van vì không có khoang kín chở người hay hàng. |