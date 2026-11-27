# Báo cáo Day 13 — Nguyễn Thế Phách, 2A202603045

> Báo cáo này tổng hợp số liệu từ Bảng A, phiếu tự kiểm và ảnh đã lưu. Những việc tôi chưa ghi lại hoặc chưa kiểm chứng được sẽ được nói rõ; ảnh preview không thay cho ảnh chụp CVAT.

## 1. Kết luận trước

MSSV: `2A202603045`. Task: `D13-2A202603045`. Tôi dùng Windows 11 và Chrome để gán nhãn thủ công 10 frame: `s01f01`, `s01f26`, `s02f02`, `s02f28`, `s03f14`, `s03f44`, `s04f06`, `s04f27`, `s05f11`, `s05f28`. Mỗi frame có 7 camera. Tôi chưa ghi lại phiên bản Chrome/CVAT, MSSV của bạn ngồi cạnh hay kết quả kiểm chéo.

Bảng A hiện có **86 cuboid**. `s02f02` và `s03f44` là hai frame ít hộp nhất, mỗi frame có 5. Phiếu tự kiểm còn gắn cờ **11/86 cuboid**. Đây là các chỗ cần xem lại, không có nghĩa là hộp chắc chắn sai.

Trong CVAT, tôi dùng Shape để vẽ cuboid, chỉnh tâm, kích thước và yaw ở Top, rồi xem độ cao và đáy ở Side/Front. Tôi bật Cuboid orientation, đối chiếu với camera và bấm Save. Tôi không dùng model để tạo nhãn.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ: `s01f01` và `s01f26`. Theo tọa độ VF, phía trước là hướng `−y`; trên BEV preview, phía trước ở phía trên, `+x` sang trái và `+y` xuống dưới.

| Frame | Hướng và mặt đường | Vật xa nhất đã gán | Vật thấy được; phía sau/hai bên | Cụm khó đọc |
|---|---|---|---|---|
| `s01f01` | Giao lộ, mặt đường nhìn gần phẳng; ước lượng thấp hơn LiDAR khoảng 1,8–2,0 m theo các hộp có ground estimate. | Xe xa nhất trong bảng khoảng 41,7 m. | Xe con, xe tải, xe hai bánh ở nhiều làn và hướng giao cắt. | Xe nhỏ ở xa và returns nền quanh cây/ven đường khó phân loại. |
| `s01f26` | Đoạn đường thẳng, mặt đường gần phẳng; ground estimate quanh 1,7 m dưới LiDAR. | Xe xa nhất trong bảng khoảng 45,4 m. | Xe con, xe buýt, xe tải và người đi bộ; camera thấy xe ở phía trước và các làn bên. | Đối tượng xa hoặc bị xe gần che khuất có ít điểm, khó xác định đủ kích thước. |

Camera giúp nhận ra loại vật, màu sắc và phần bị che. LiDAR cho biết vị trí, khoảng cách và hình dạng 3D của vật.

Tôi xem tám frame còn lại qua preview. Trên BEV, phía trước vẫn hướng lên trên. Ở các frame này, vật ở xa và điểm nền ven đường thường khó phân biệt.

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

Các số liệu lấy từ Bảng A và phiếu tự kiểm. Tôi chưa lưu kết quả vòng hiệu chuẩn, danh sách vật bỏ qua hay thông tin người kiểm chéo; cần bổ sung theo đúng những gì đã làm. Hướng mũi tên và các góc Top/Side/Front cũng nên kiểm tra lại trực tiếp trên CVAT.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Những nhận xét dưới đây là điều tôi quan sát được; nguyên nhân chỉ là phỏng đoán, không phải kết luận về cảm biến.

| Hiện tượng | Frame / nguồn quan sát | Giả thuyết và dấu hiệu | Ảnh hưởng khi gán; minh chứng ảnh |
|---|---|---|---|
| Bóng che | `s02f02`; ảnh CVAT `t4-1-bong-che.png` | Xe buýt trong camera che một phần xe/vùng phía sau; quan sát cả camera và point cloud. | Ảnh minh họa hiện tượng che khuất; chỉ gán hộp cho phần vật thể có đủ căn cứ. |
| Thưa ở xa | `s02f28`, Car #3 (CVAT ID #39); Bảng A và ảnh CVAT người học cung cấp | Xe xa nhất cách LiDAR 45,99 m, có 63 returns trong hộp; point cloud thưa hơn rõ so với xe gần. | Ảnh CVAT cho thấy cuboid và các view 3D/Top/Side/Front; không nới hộp để gom các returns ngoài xe. Ảnh này đã lưu thành `t4-2-thua-o-xa.png`. |
| Phản xạ thấp / ít returns | `s01f01`; ảnh CVAT `t4-3-phan-xa-thap.png` | Vùng được khoanh có returns LiDAR thưa; ảnh camera cần được đối chiếu để xác định vật thể. Không thể kết luận riêng do phản xạ thấp; xa/che khuất cũng có thể là nguyên nhân. | Dùng làm ví dụ vùng ít returns, ghi nguyên nhân là giả thuyết. |
| Điểm lạ | `s02f28`; ảnh CVAT `t4-4-diem-la.png` | Khung đỏ khoanh một cụm điểm trông có vẻ rời khỏi các cụm khác. Tôi chưa xác định được đó là nhiễu hay vật thể nhỏ. | Không tạo cuboid khi chưa nhận ra vật thể. |

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

Trong bốn ví dụ này, xe càng xa thì số điểm trong hộp càng ít. Đây là các xe ở những frame khác nhau nên chỉ dùng để tham khảo; kích thước hộp và vật che cũng ảnh hưởng đến số điểm.

Phiếu tự kiểm hiện tại còn **11/86** hộp có cờ: điểm sát mép ở một số Car/Cyclist/Truck; đáy cao hơn mặt đường ở một số Car/Cyclist; kích thước ngoài khoảng ở một số Cyclist và Bus. Repo không lưu lịch sử chỉnh sửa CVAT nên không xác định được hai hộp nào đã sửa hay lý do giữ từng cờ. Cần đối chiếu trực tiếp các hộp còn cờ và ghi lý do theo kết quả thực tế.

Thông tin kiểm chéo (MSSV, hai số được đối chiếu, kết quả) chưa được ghi lại trong repo; cần bổ sung sau khi thực hiện.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D dùng mét để vị trí và kích thước có cùng đơn vị thực tế. Camera là ảnh 2D nên không cho biết trực tiếp khoảng cách. Vật ở xa hoặc bị che thường có ít điểm LiDAR hơn; vì vậy không nên chỉ dựa vào số điểm để quyết định có vẽ hộp hay không.

- Công cụ: ChatGPT/Codex.
- Câu hỏi chính: cách dùng cuboid 3D trong CVAT (Shape, Top/Side/Front, chỉnh kích thước/yaw, hướng hộp và occlusion) cùng các lệnh repo.
- Cách dùng: tham khảo để hiểu thao tác; cuboid trong task được tự vẽ thủ công, không dùng model tạo nhãn.

Tôi vẫn cần xem lại các hộp còn bị gắn cờ, nhất là hộp Cyclist ít điểm, hộp có nhiều điểm sát mép và Bus `s04f06`. Tôi chưa lưu kết quả kiểm chéo, vòng hiệu chuẩn hay danh sách vật đã bỏ qua. Chưa ghi nhận lỗi phát hiện sau hạn.

### Ảnh minh chứng cần lưu trong `report/individual/images/`

Thư mục ảnh có đủ 11 file theo danh sách nộp: bốn ảnh T1, ba ảnh T2 và bốn ảnh T4. T4-3 và T4-4 có khoanh vùng để người chấm xem; nguyên nhân chưa được xác nhận. Hai ảnh Top T1 có chú thích hướng. Ảnh preview trong `work/xem/` không thay thế ảnh CVAT.
