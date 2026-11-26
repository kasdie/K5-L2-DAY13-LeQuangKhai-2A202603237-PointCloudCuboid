# Báo cáo Day 13 — Lê Quang Khải, 2A202603237

## 1. Kết luận trước

- MSSV 2A202603237, task `D13-2A202603237`, macOS, Google Chrome, CVAT 2.83.0.
- Bảng A có **78 cuboid trên 10 frame**, trong đó **75 cuboid nằm trong phạm vi 50 m**. `s03f25` và `s05f39` có ít hộp nhất, mỗi frame 5 hộp trước khi lọc khoảng cách. Sau khi loại ba hộp ngoài 50 m, `s03f25` còn 2 hộp trong phạm vi. Chưa xác định nguyên nhân hai frame này có ít hộp hơn.
- Phiếu tự kiểm gắn cờ **31/78 cuboid**, trong đó **12** hộp có cờ `length < width`. Sau khi đối chiếu đầu và đuôi qua camera, 63 hộp đã được chỉnh yaw. Các hộp đã chỉnh có 46 hộp ở 270° và 17 hộp ở 90°. Còn 15 hộp yaw 0°, gồm 12 hộp trong phạm vi chưa đủ căn cứ để chỉnh và 3 hộp ngoài 50 m.
- MSSV bạn cùng lớp được ghi cho phần đổi kiểm: `2A202603172`.

**Thao tác CVAT**

- Xoay khung 3D lớn để nhìn thẳng xuống, kéo chuột phải để dời góc nhìn và dùng con lăn để đổi mức thu phóng.
- Chọn label và **Shape**, nháy đúp để đặt hộp. Dùng **Top** để căn tâm, kích thước ngang và yaw, rồi dùng **Side** và **Front** để kiểm đáy và chiều cao.
- Bật **Appearance → Cuboid orientation** để thấy mũi tên hướng. Dùng ảnh do `preview` tạo để đối chiếu hình chiếu cuboid với camera.

## 2. Đọc frame

| Frame | Hướng trên ảnh từ trên xuống | Mặt đường so với LiDAR | Vật xa còn nhận ra | Cảnh vật và cụm chưa xác định |
|---|---|---|---|---|
| `s01f10` | Phía trước ở mép trên. +x hướng sang trái, +y hướng về sau. | Phẳng theo quan sát, ước khoảng −1,8 m ở vùng đã kiểm. | Bus `idx 7` ở 46,12 m. Car xa nhất đã gán là `idx 4` ở 23,90 m. | Phía trước có một Car và một Bus. Phía sau gần có một Car. Bên trái có một Car hơi chếch về trước. Không có cụm điểm chưa xác định. |
| `s01f39` | Phía trước ở mép trên. +x hướng sang trái, +y hướng về sau. | Phẳng theo quan sát, ước khoảng −1,87 m ở vùng đã kiểm. | Car `idx 7` ở 27,61 m. | Bên trái sát xe có một Car. Trước và sau mỗi phía có hai Car trong khoảng 30 m. Bên phải có hàng phân cách với làn xe đạp và ba người đang đi xe trên làn đó. Không có cụm điểm chưa xác định. |

Hai ước lượng mặt đường dựa trên `z_bottom − ground_gap_m` tại các hộp tham chiếu. Khoảng cách trong bảng đo theo phương ngang đến tâm hộp. Ảnh đọc kỹ gồm `t1-s01f10-3d`, `t1-s01f10-top`, `t1-s01f39-3d` và `t1-s01f39-top`.

**Góc nhìn camera.** Hai frame dùng chung hiệu chuẩn của sequence 01. Khi kiểm bằng phép chiếu qua cả bảy camera, một phần vùng thấp ngay sau xe nằm ngoài khung của cả bảy ảnh. Ví dụ, tại tầm ngang 2 m và thấp hơn LiDAR 1 m, vùng khuất nằm gần hướng thẳng sau, kéo lệch về bên phải. Đây là ví dụ cho độ cao và khoảng cách cụ thể. Khi quét vùng sau và hai bên, dùng LiDAR để định vị trước rồi kiểm camera có góc nhìn phù hợp.

- **Camera:** màu sắc và điều kiện môi trường, biển báo hoặc vạch giao thông, đặc điểm bề ngoài giúp nhận diện loại vật.
- **LiDAR:** khoảng cách và vị trí tương đối, kích thước 3D, cấu trúc không gian xung quanh.

### Tám frame đọc lướt

Tám frame được kiểm ở cùng góc nhìn từ trên xuống trong CVAT, với +x sang trái và +y hướng về mép dưới.

| Frame | Hướng trước xe (−y) | Số hộp đã xuất | Ghi chú từ Bảng A |
|---|---|---:|---|
| `s02f05` | Mép trên | 8 | |
| `s02f24` | Mép trên | 8 | Car `idx 8` có cờ che khuất. |
| `s03f03` | Mép trên | 9 | Car `idx 5` có rất ít điểm trong hộp và cần kiểm vị trí hộp. |
| `s03f25` | Mép trên | 5 | Ba hộp vượt 50 m. |
| `s04f10` | Mép trên | 9 | |
| `s04f28` | Mép trên | 11 | |
| `s05f05` | Mép trên | 8 | Car `idx 4` có ít điểm hơn Car `idx 3` ở tầm gần nhau. |
| `s05f39` | Mép trên | 5 | |

## 3. Ba cuboid có hướng dẫn

Tôi dùng camera để nhận diện vật và tham khảo hướng đầu, đuôi. Tôi dùng Top, Side và Front để kiểm hướng, kích thước và đáy so với mặt đường. Số đo sau đây lấy từ lần xuất gần nhất.

| Mẫu | Frame, idx | Class | Yaw xuất và việc cần kiểm | Đáy so với mặt đường cục bộ | So với bảng 4.3 |
|---|---|---|---|---|---|
| `t2-a-car` | `s01f10`, 1 | Car | 270°, hướng về −y theo đầu xe trên camera. | Cao hơn 0,10 m, gần mặt đường ước lượng. | 4,317 × 1,841 × 1,321 m. Dài và rộng nằm trong dải sedan/hatchback, chiều cao còn thấp. |
| `t2-b-bus-truck` | `s01f10`, 7 | Bus | 270°, hướng về −y theo đầu xe trên camera. | Chênh khoảng 0,00 m. | 12,090 × 4,875 × 3,350 m. Dài và cao nằm trong dải bus thường, chiều rộng còn lớn. |
| `t2-c-ped-cyclist` | `s01f10`, 6 | Cyclist | 270°, theo hướng người và xe quan sát từ camera. | Cao hơn 0,82 m, có dấu hiệu lơ lửng. | 1,448 × 0,712 × 1,064 m. Cần kiểm đáy và việc hộp bao đủ người lẫn xe. |

**Trường hợp loại khỏi phạm vi.** Ba hộp `idx 3–5` ở `s03f25` vẫn có trong Bảng A nhưng bị loại khỏi phần đánh giá trong 50 m. Các tâm lần lượt ở 55,01 m, 58,94 m và 102,13 m. Trong ghi chép các frame đã đọc kỹ, chưa ghi nhận trường hợp cụ thể khác bị bỏ qua vì thiếu căn cứ.

**Hiệu chuẩn.** Task chung `D13-HC485087` được dùng làm nguồn cho ví dụ ở mục 4. Chưa có kết quả so sánh giữa các học viên trong vòng hiệu chuẩn để báo cáo.

## 4. Bốn hiện tượng chất lượng

| Hiện tượng | Frame và ảnh bằng chứng | Bằng chứng và giả thuyết | Hệ quả khi gán |
|---|---|---|---|
| Bóng che | `s02f00`, task hiệu chuẩn `D13-HC485087`, `t4-1-bong-che` | Case theo hướng dẫn của đề: vùng phía sau Bus ở bên trái ít điểm hơn hai bên. Giả thuyết là thân Bus chặn tia LiDAR đi tới vùng phía sau. | Chỉ đánh dấu che khuất cho vật có bằng chứng quan sát được. Vùng ít điểm phía sau Bus chưa đủ để kết luận có vật hay không. |
| Thưa ở xa | `s01f10`, Bus `idx 7`, `t4-2-thua-o-xa` | Bus ở 46,12 m, hộp có 314 điểm. Khoảng cách xa là một nguyên nhân có thể làm các điểm lấy mẫu thưa hơn. | Đối chiếu camera trước khi suy ra kích thước. Có thể gán khi thấy khoảng 15–20% thể tích hoặc có đủ căn cứ suy ra phần còn lại. Nếu thiếu căn cứ, ghi `LOW_VISIBILITY`. |
| Ít điểm bất thường | `s05f05`, Car `idx 4` và Car `idx 3`, `t4-3-phan-xa-thap` | `idx 4` có 402 điểm ở 18,81 m, ít hơn khoảng 39% so với 659 điểm của `idx 3` ở 16,69 m. Camera nhận ra được cả hai xe. Khác biệt về khoảng cách, góc nhìn và phần bề mặt nhận tia có thể góp phần tạo chênh lệch. Số điểm trong hộp chưa đủ để kết luận vật có phản xạ thấp. | Đối chiếu cụm điểm với camera để kiểm phần thân được quét. Không nới hộp theo điểm lẻ xung quanh. |
| Điểm lạ | `s02f35`, task hiệu chuẩn `D13-HC485087`, `t4-4-diem-la` | Trong vùng gợi ý phía sau bên trái, cách khoảng 29 m theo phương ngang, có 46 điểm thấp hơn mặt đường. Nguyên nhân có thể là phản xạ gián tiếp, chưa xác định được từ frame này. | Không gán cuboid cho cụm dưới mặt đường vì không có bằng chứng đó là vật thuộc các class của lab. |

## 5. Số từ cuboid của tôi

Số liệu lấy từ Bảng A và phiếu tự kiểm của lần xuất hiện tại. Khoảng cách `dist_m` đo theo phương ngang, còn `r = √(x² + y² + z²)` đo đến tâm hộp trong không gian 3D.

| Cuboid | z (m) | h (m) | z đáy = z − h/2 (m) | dist_m (m) | r (m) | yaw (°) | Nhận xét |
|---|---:|---:|---:|---:|---:|---:|---|
| `s01f10`, 1, Car | −1,010 | 1,321 | −1,670 | 5,97 | 6,05 | 270,0 | Đáy gần mặt đường cục bộ, chênh +0,10 m. |
| `s01f10`, 7, Bus | −0,144 | 3,350 | −1,819 | 46,12 | 46,12 | 270,0 | Đáy gần trùng mặt đường cục bộ. |
| `s01f10`, 6, Cyclist | −0,361 | 1,064 | −0,893 | 22,33 | 22,33 | 270,0 | Đáy cao hơn khoảng 0,82 m, cần kiểm trong Side View. |

**Khoảng cách.** Hộp gần nhất là Car `s05f05` `idx 1` với `dist_m = 3,45 m`, `r = 3,53 m`. Hộp xa nhất trong Bảng A là Car `s03f25` `idx 5` với `dist_m = 102,13 m`, `r = 102,22 m`, ngoài phạm vi lab. Trong phạm vi 50 m, hộp xa nhất cần đối chiếu theo Bảng A là Bus `s01f10` `idx 7` với `dist_m = 46,12 m`, `r = 46,12 m`.

**Kích thước và yaw.** Car mẫu dài 4,317 m và rộng 1,841 m, nằm trong dải sedan/hatchback. Chiều cao 1,321 m thấp hơn dải 1,40–1,60 m. Cyclist mẫu cao 1,064 m, thấp hơn dải xe đạp có người 1,60–1,90 m. Cần xem lại cả người và xe trong hộp. Với những hộp có trục dài ban đầu song song với trục y của xe thu dữ liệu, lần chỉnh yaw dùng hai hướng trục 270° và 90° theo đầu, đuôi nhìn thấy trên camera. Tâm, chiều cao và vùng bao của hộp đã chỉnh được giữ nguyên. Car `s01f10` `idx 4` cùng chiều có yaw 270°. Car `s04f10` `idx 9` ngược chiều có yaw 90°.

### Mật độ theo khoảng cách

| Cuboid | dist_m (m) | Số điểm trong hộp |
|---|---:|---:|
| `s01f10`, 1, Car | 5,97 | 2.044 |
| `s04f10`, 5, Car | 13,31 | 1.002 |
| `s01f10`, 4, Car | 23,90 | 254 |
| `s04f28`, 6, Car | 37,21 | 105 |

Trong bốn mẫu Car, khi khoảng cách tăng khoảng bốn lần (5,97 m lên 23,90 m), số điểm còn khoảng một phần tám (2.044 xuống 254). Xu hướng giảm phù hợp với slide bài giảng, có trung vị lần lượt khoảng 1.968, 718, 214 và 72 điểm ở bốn dải 0–10, 10–20, 20–30 và 30–40 m. Số điểm trong từng hộp còn phụ thuộc độ che, góc nhìn và cách căn hộp.

### Chỉnh sửa và đổi kiểm

| Frame | idx | Thay đổi |
|---|---|---|
| `s02f24` | 6 | Tăng chiều cao theo trục z cho Car. |
| `s01f10` | 7 | Tăng chiều cao theo trục z cho Bus. |

Tôi chỉnh chiều cao vì hộp ban đầu chưa bao hết xe, một phần do có ít điểm quét ở vùng đó. Sau lần chỉnh hướng, cả hai hộp không còn cờ `length < width`. Bus `s01f10` `idx 7` vẫn có cờ rộng hơn dải tham chiếu. Cyclist ở `s01f10` `idx 6` cần kiểm lại đáy trong Side View.

**Tự đối chiếu số liệu.** Tính lại khoảng cách ngang từ tâm cuboid cho Bus `s01f10` `idx 7` và Car `s01f39` `idx 7`, được lần lượt **46,12 m** và **27,61 m**, khớp cột `dist_m` trong Bảng A. Phiếu tự kiểm khớp mã băm của Bảng A hiện tại.

**Đổi kiểm.** Bạn cùng lớp `2A202603172` chọn hai giá trị `yaw_deg` ở mục 5 để đối chiếu.

| Cuboid | Báo cáo | Bảng A hiện tại |
|---|---:|---:|
| Car `s01f10`, `idx 4` | 270° | 270° |
| Car `s04f10`, `idx 9` | 90° | 90° |

Kết quả đổi kiểm: bạn `2A202603172` xác nhận cả hai giá trị trong báo cáo khớp với Bảng A. Hai số này giữ nguyên sau đối chiếu.

## 6. Ý nghĩa của nhãn và điều còn chưa chắc

Tâm và kích thước cuboid dùng mét để mô hình học vị trí và hình học thực của vật trong hệ LiDAR, vì kích thước theo pixel thay đổi theo camera và khoảng cách. Với vật thưa điểm, cần quy tắc riêng để mô hình được học từ những hộp có căn cứ đủ rõ và hạn chế nhãn suy đoán sai hình dạng hoặc vị trí.

Tôi còn chưa chắc về hướng của 12 hộp trong phạm vi vẫn có yaw 0°, chiều rộng của Bus mẫu và độ cao đáy của Cyclist ở `s01f10` `idx 6`. Ba hộp ngoài 50 m ở `s03f25` và ca ít điểm trong `s03f03` cũng cần xem lại trong CVAT.
|Khai báo sử dụng AI|
Công cụ đã dùng là Codex. 
Sử dụng AI để chạy, set up sẵn các lệch trong powershell, terminal, docker, đọc hiểu về rules và guildline, cách làm bài.