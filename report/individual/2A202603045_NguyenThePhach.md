# Báo cáo Day 13 — Nguyễn Thế Phách, 2A202603045

> Báo cáo dùng số liệu từ Bảng A và phiếu tự kiểm hiện có trong `submission/`, cùng ảnh preview trong `work/xem/`. Những việc không có ghi chép được đánh dấu rõ, không suy đoán. Ảnh preview không thay thế ảnh chụp CVAT cần nộp.

## 1. Kết luận trước

MSSV: `2A202603045`. Task: `D13-2A202603045`. Hệ điều hành: Windows 11. Tôi đã gán nhãn thủ công 10 frame: `s01f01`, `s01f26`, `s02f02`, `s02f28`, `s03f14`, `s03f44`, `s04f06`, `s04f27`, `s05f11`, `s05f28`; mỗi frame có 7 camera. Trình duyệt là Chrome; phiên bản trình duyệt và CVAT không được ghi lại. MSSV bạn ngồi cạnh và kết quả kiểm chéo chưa được lưu trong repo.

Bảng A hiện có **86 cuboid**. Ít cuboid nhất trong một frame là **5** (`s02f02` và `s03f44`). Phiếu tự kiểm mới nhất gắn cờ **11/86 cuboid**; cảnh báo là gợi ý để xem lại, không khẳng định hộp sai. Tôi đã hoàn thành vẽ thủ công; cần đối chiếu trực tiếp các cờ trước khi chốt chất lượng.

Trong CVAT, tôi dùng Shape để đặt từng hộp và nháy đúp để kết thúc. Ở Top tôi chỉnh tâm, dài/rộng và yaw; ở Side/Front tôi kiểm tra độ cao và đáy. Tôi bật Cuboid orientation, đối chiếu camera rồi Save. Không dùng model để tạo nhãn.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ: `s01f01` và `s01f26`. Theo tọa độ VF, phía trước là hướng `−y`; trên BEV preview, phía trước ở phía trên, `+x` sang trái và `+y` xuống dưới.

| Frame | Hướng và mặt đường | Vật xa nhất đã gán | Vật thấy được; phía sau/hai bên | Cụm khó đọc |
|---|---|---|---|---|
| `s01f01` | Giao lộ, mặt đường nhìn gần phẳng; ước lượng thấp hơn LiDAR khoảng 1,8–2,0 m theo các hộp có ground estimate. | Xe xa nhất trong bảng khoảng 41,7 m. | Xe con, xe tải, xe hai bánh ở nhiều làn và hướng giao cắt. | Xe nhỏ ở xa và returns nền quanh cây/ven đường khó phân loại. |
| `s01f26` | Đoạn đường thẳng, mặt đường gần phẳng; ground estimate quanh 1,7 m dưới LiDAR. | Xe xa nhất trong bảng khoảng 45,4 m. | Xe con, xe buýt, xe tải và người đi bộ; camera thấy xe ở phía trước và các làn bên. | Đối tượng xa hoặc bị xe gần che khuất có ít điểm, khó xác định đủ kích thước. |

Camera cho biết màu sắc/ngoại hình, phần nhìn thấy của vật và quan hệ che khuất. LiDAR cho biết khoảng cách 3D theo mét, mật độ returns trên bề mặt và hình dạng không gian/độ cao tương đối.

Tám frame còn lại được xem qua preview. Hướng trước trong BEV vẫn ở phía trên; các điểm cần chú ý là vật xa thưa điểm và returns nền ven đường.

| Frame | Tóm tắt |
|---|---|
| `s02f02` | Xe buýt rất gần cùng nhiều xe con; xe lớn che một phần vùng nhìn phía trước/bên. |
| `s02f28` | Chủ yếu xe con trên đoạn đường có dải phân cách; có hộp ở xa. |
| `s03f14` | Xe con và xe tải; xe gần che một phần xe phía sau. |
| `s03f44` | Xe con và xe hai bánh; có đối tượng nhỏ, thưa điểm. |
| `s04f06` | Xe buýt và xe con; kích thước xe buýt bị cảnh báo sát/ngoài khoảng tham chiếu. |
| `s04f27` | Xe con, xe hai bánh và rào ven đường; cần tách returns của rào khỏi vật thể. |
| `s05f11` | Xe con và xe tải trên vùng đường rộng; một hộp xe tải có nhiều điểm sát mép. |
| `s05f28` | Xe con và xe hai bánh; một số hộp Cyclist ít điểm và cần xem lại kích thước. |

## 3. Ba cuboid có hướng dẫn (Bước 3)

| Vật | Frame, idx | Class | Yaw theo bảng | Đáy theo phiếu tự kiểm | Kích thước và nhận xét |
|---|---|---|---:|---|---|
| Xe con gần | `s01f01`, #1 | Car | 267,8° | `z_bottom = −1,714 m`, ground gap 0,10 m | 4,498 × 1,726 × 1,437 m; trong khoảng xe con tham chiếu. |
| Xe buýt | `s02f02`, #1 | Bus | 267,8° | `z_bottom = −1,724 m`, ground gap 0,12 m | 12,017 × 3,060 × 3,027 m; trong khoảng Bus tham chiếu. |
| Xe hai bánh | `s01f01`, #11 | Cyclist | 260,1° | `z_bottom = −1,463 m`, ground gap 0,23 m | 2,017 × 0,893 × 1,684 m; trong khoảng Cyclist tham chiếu. |

Các con số lấy từ Bảng A/phiếu tự kiểm; xác nhận mũi tên đúng đầu xe và đã kiểm đủ Top/Side/Front cần đối chiếu trực tiếp trên CVAT. Repo không có ghi chép kết quả vòng hiệu chuẩn, danh sách vật đã bỏ qua hay MSSV người kiểm chéo, nên không kết luận các việc đó đã diễn ra.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Nguyên nhân dưới đây là giả thuyết từ preview và point cloud, không phải kết luận về cảm biến.

| Hiện tượng | Frame / nguồn quan sát | Giả thuyết và dấu hiệu | Ảnh hưởng khi gán; minh chứng ảnh |
|---|---|---|---|
| Bóng che | `s02f02`; ảnh CVAT `t4-1-bong-che.png` | Xe buýt trong camera che một phần xe/vùng phía sau; quan sát cả camera và point cloud. | Ảnh minh họa hiện tượng che khuất; chỉ gán hộp cho phần vật thể có đủ căn cứ. |
| Thưa ở xa | `s02f28`, Car #3 (CVAT ID #39); Bảng A và ảnh CVAT người học cung cấp | Xe xa nhất cách LiDAR 45,99 m, có 63 returns trong hộp; point cloud thưa hơn rõ so với xe gần. | Ảnh CVAT cho thấy cuboid và các view 3D/Top/Side/Front; không nới hộp để gom các returns ngoài xe. Ảnh này đã lưu thành `t4-2-thua-o-xa.png`. |
| Phản xạ thấp / ít returns | `s01f01`; ảnh CVAT `t4-3-phan-xa-thap.png` | Vùng được khoanh có returns LiDAR thưa; ảnh camera cần được đối chiếu để xác định vật thể. Không thể kết luận riêng do phản xạ thấp; xa/che khuất cũng có thể là nguyên nhân. | Dùng làm ví dụ vùng ít returns, ghi nguyên nhân là giả thuyết. |
| Điểm lạ | `s02f28`; ảnh CVAT `t4-4-diem-la.png` | Khung đỏ khoanh một patch returns cô lập cần kiểm tra; chỉ xem là ứng viên, chưa xác nhận chắc là nhiễu hay vật thể nhỏ. | Không tạo cuboid cho các return này. |

## 5. Số từ cuboid của tôi (Bước 5)

| Cuboid | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw | Nhận xét từ phiếu |
|---|---:|---:|---:|---:|---:|---:|---|
| Gần nhất: `s02f02` #1 Bus | −0,210 | 3,027 | −1,724 | 3,35 | 3,36 | 267,8° | Ground gap 0,12 m; gần mặt đường tham chiếu. |
| Xa nhất: `s02f28` #3 Car | −1,048 | 1,519 | −1,808 | 45,99 | 46,01 | 267,8° | Không có ground estimate đáng tin trong phiếu; không kết luận lơ lửng/chìm chỉ từ z đáy. |
| `s01f01` #11 Cyclist | −0,621 | 1,684 | −1,463 | 11,78 | 11,80 | 260,1° | Ground gap 0,23 m; kích thước trong khoảng tham chiếu. |

Xe con `s01f01` #1 có kích thước 4,498 × 1,726 × 1,437 m, trong khoảng tham chiếu. Cyclist `s01f01` #11 có kích thước 2,017 × 0,893 × 1,684 m, trong khoảng Cyclist. Xe `s01f01` #1 có yaw 267,8°; xe #6 có yaw 87,5°, gần cặp hướng cùng/ngược chiều 270°/90°.

| Cuboid Car | dist_m | points_in |
|---|---:|---:|
| `s01f01` #1 | 4,19 m | 2.912 |
| `s01f01` #3 | 14,34 m | 585 |
| `s01f01` #5 | 26,05 m | 114 |
| `s02f28` #3 | 45,99 m | 63 |

Trong bốn mẫu này, khoảng cách tăng gần 11 lần thì số điểm trong hộp giảm khoảng 46 lần. Đây là các xe/frame khác nhau nên chỉ minh họa xu hướng; kích thước, góc quét và che khuất cũng ảnh hưởng. Xu hướng giảm theo khoảng cách phù hợp với đường cong tham chiếu trên lớp, nhưng số điểm từng hộp dao động.

Phiếu tự kiểm hiện tại còn **11/86** hộp có cờ: điểm sát mép ở một số Car/Cyclist/Truck; đáy cao hơn mặt đường ở một số Car/Cyclist; kích thước ngoài khoảng ở một số Cyclist và Bus. Repo không lưu lịch sử chỉnh sửa CVAT nên không xác định được hai hộp nào đã sửa hay lý do giữ từng cờ. Cần đối chiếu trực tiếp các hộp còn cờ và ghi lý do theo kết quả thực tế.

Thông tin kiểm chéo (MSSV, hai số được đối chiếu, kết quả) chưa được ghi lại trong repo; cần bổ sung sau khi thực hiện.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D dùng mét vì vị trí, kích thước và khoảng cách cần cùng đơn vị vật lý giữa các cảm biến; pixel chỉ biểu diễn vị trí trên một ảnh và thay đổi theo góc nhìn/tiêu cự. Vật thưa điểm cần quy tắc riêng vì returns giảm theo khoảng cách hoặc che khuất; dùng ngưỡng điểm cố định có thể bỏ nhầm vật thật hoặc khiến người gán nới hộp quá mức.

- Công cụ: ChatGPT/Codex.
- Câu hỏi chính: cách dùng cuboid 3D trong CVAT (Shape, Top/Side/Front, chỉnh kích thước/yaw, hướng hộp và occlusion) cùng các lệnh repo.
- Cách dùng: tham khảo để hiểu thao tác; cuboid trong task được tự vẽ thủ công, không dùng model tạo nhãn.

Điều còn chưa chắc là các cảnh báo còn lại, nhất là hộp Cyclist ít điểm, điểm sát mép và Bus `s04f06`; cần xem trực tiếp point cloud/camera trước khi quyết định giữ hay sửa. Repo không có ghi chép vòng hiệu chuẩn, kiểm chéo hay danh sách vật bỏ qua. Chưa có lỗi phát hiện sau hạn được ghi nhận.

### Ảnh minh chứng cần lưu trong `report/individual/images/`

Trong thư mục hiện có 11 file ảnh: bốn ảnh T1; ba ảnh T2; và bốn ảnh T4 (`t4-1-bong-che.png` frame `s02f02`, `t4-2-thua-o-xa.png` frame `s02f28`, `t4-3-phan-xa-thap.png` frame `s01f01`, `t4-4-diem-la.png` frame `s02f28`). Hai ảnh Top T1 có chú thích hướng. Các khoanh đỏ trên T4-3/T4-4 là vùng ứng viên để thảo luận; không khẳng định nguyên nhân cảm biến. Ảnh Cyclist dự phòng được lưu ngoài thư mục nộp. Ảnh xem trước trong `work/xem/` không thay thế ảnh CVAT.
