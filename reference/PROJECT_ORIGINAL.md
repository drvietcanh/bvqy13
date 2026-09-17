# DỰ ÁN CỔNG THÔNG TIN & DẪN ĐƯỜNG BỆNH VIỆN BẰNG QR

## 1. Tóm tắt dự án

### 1.1. Mục tiêu

Xây dựng một hệ thống đơn giản, chi phí gần bằng 0, dễ triển khai và dễ duy trì cho bệnh viện quy mô nhỏ, với hai nhóm chức năng chính:

1. **Cổng thông tin bệnh viện**
   - Giới thiệu bệnh viện.
   - Danh sách khoa/phòng.
   - Gói khám bệnh và khám sức khỏe.
   - Giá dịch vụ.
   - Các kỹ thuật bệnh viện có thể thực hiện.
   - Hướng dẫn khám bệnh.
   - Giờ làm việc.
   - Số điện thoại/hotline.
   - Thông tin hữu ích cho bệnh nhân và người nhà.

2. **Dẫn đường trong bệnh viện**
   - Xác định vị trí hiện tại thông qua QR.
   - Tìm khoa/phòng/địa điểm.
   - Hiển thị hướng dẫn đi từng bước.
   - Hiển thị sơ đồ tầng tĩnh.
   - Hỗ trợ quét lại QR gần nhất khi bệnh nhân bị mất phương hướng.
   - Dùng chung trên điện thoại, kiosk và tablet.

### 1.2. Nguyên tắc xuyên suốt

Ưu tiên theo thứ tự:

**Miễn phí → dễ làm → dễ sửa → dễ đào tạo → dễ bàn giao → bệnh nhân dễ sử dụng.**

Không chọn công nghệ phức tạp chỉ vì hiện đại hơn.

### 1.3. Phạm vi không làm ở phiên bản đầu

Không triển khai:

- HIS.
- LIS/PACS.
- Hồ sơ bệnh án.
- Dữ liệu cá nhân bệnh nhân.
- Tài khoản bệnh nhân.
- App Android/iOS riêng.
- Chatbot AI.
- Indoor positioning bằng Bluetooth/Wi-Fi/UWB.
- Bản đồ 3D.
- Thanh toán trực tuyến.
- Đặt lịch phức tạp.
- Dashboard phân tích phức tạp.
- CMS riêng.
- Backend/server riêng.
- Database chuyên dụng như PostgreSQL/Supabase trong giai đoạn đầu.

---

# 2. Kiến trúc tổng thể đã chốt

## 2.1. Mô hình

```text
                    GOOGLE SHEETS
                         │
             Nhân viên sửa nội dung
                         │
              Xuất bản dữ liệu công khai
                     dạng CSV
                         │
                         ▼
              WEBSITE TĨNH BỆNH VIỆN
                HTML + CSS + JS thuần
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       Điện thoại       Kiosk          Tablet
          ▲
          │
         QR
```

### 2.2. Hosting

Ưu tiên:

- Cloudflare Pages, hoặc
- GitHub Pages.

Lý do:

- Miễn phí.
- Không cần server.
- Không có cold-start.
- Không phải quản lý deployment kiểu Apps Script Web App.
- Dễ chuyển hosting nếu sau này cần.
- Tốc độ tốt do là website tĩnh.

### 2.3. Vai trò Google Apps Script

Không dùng Apps Script làm backend chính.

Chỉ dùng Apps Script cho các việc phụ nếu cần:

- Backup Google Sheet tự động.
- Menu kiểm tra dữ liệu.
- Sinh file backup định kỳ.

Như vậy Apps Script không nằm trên đường truy cập của bệnh nhân.

---

# 3. Một website duy nhất cho toàn bệnh viện

Không làm:

- một website thông tin bệnh viện riêng;
- một website dẫn đường riêng.

Chỉ có **một website duy nhất**.

Ví dụ:

```text
https://benhvienabc.pages.dev
```

hoặc khi chính thức:

```text
https://benhvienabc.vn
```

hoặc:

```text
https://huongdan.benhvienabc.vn
```

## 3.1. Truy cập thông thường

Người dùng mở:

```text
https://benhvienabc.vn
```

Website hiển thị trang chủ bệnh viện.

## 3.2. Truy cập bằng QR

Ví dụ QR tại Khu Phòng khám:

```text
https://benhvienabc.vn/?p=PKHAM
```

Website hiểu:

```text
Vị trí hiện tại = PKHAM
```

Sau đó tự hiển thị:

> Bạn đang ở: Khu Phòng khám

và mọi chức năng dẫn đường lấy `PKHAM` làm điểm xuất phát.

## 3.3. Ví dụ QR tại các khu

```text
?p=SANH
?p=PKHAM
?p=XN
?p=CDHA
?p=NT
?p=VP
```

Trong đó:

- `SANH`: Sảnh chính.
- `PKHAM`: Khu Phòng khám.
- `XN`: Khoa Xét nghiệm.
- `CDHA`: Chẩn đoán hình ảnh.
- `NT`: Nhà thuốc.
- `VP`: Thu viện phí.

---

# 4. Trang chủ website

Trang chủ cần đơn giản, chữ lớn, ít nút.

Gợi ý:

```text
BỆNH VIỆN ABC

[ GIỚI THIỆU BỆNH VIỆN ]

[ KHOA / PHÒNG ]

[ GÓI KHÁM - DỊCH VỤ ]

[ GIÁ DỊCH VỤ ]

[ KỸ THUẬT BỆNH VIỆN ]

[ TÌM ĐƯỜNG ]

[ HƯỚNG DẪN KHÁM ]

[ LIÊN HỆ - HỖ TRỢ ]
```

## 4.1. Thanh điều hướng cố định

Trên mọi trang:

```text
🏠 Trang chủ   🔎 Tìm kiếm   🗺 Tìm đường   ☎ Hỗ trợ
```

Dù bệnh nhân đang xem giá, gói khám hay kỹ thuật, vẫn có thể bấm **Tìm đường** ngay.

---

# 5. Nguyên tắc hoạt động của QR

## 5.1. QR chỉ chứa ID vị trí

Không mã hóa tên khoa trực tiếp trong QR.

Đúng:

```text
https://benhvienabc.vn/?p=PKHAM
```

Không nên:

```text
https://benhvienabc.vn/?p=Phong-Kham-Noi-Tong-Hop-Tang-1
```

Lợi ích:

- Đổi tên khoa không cần in lại QR.
- Chuyển phòng chỉ sửa dữ liệu.
- QR dùng được lâu dài.
- Dễ quản lý.

## 5.2. Mã vị trí

Nên dùng mã ngắn và ổn định.

Ví dụ:

```text
SANH
PKHAM
XN
CDHA
NT
VP
CC
NOI
NGOAI
```

Hoặc nếu muốn chi tiết hơn:

```text
Q-SANH-01
Q-PKHAM-01
Q-XN-01
```

---

# 6. Sinh QR tự động

Trong Google Sheet `DIEM_QR`, tạo các cột:

| QR_ID | Tên vị trí | Khu/Tầng | URL | QR |
|---|---|---|---|---|

Giả sử URL gốc nằm ở ô `H1`:

```text
https://benhvienabc.pages.dev/
```

Cột URL:

```excel
=$H$1&"?p="&A2
```

Cột QR có thể dùng công thức:

```excel
=IMAGE("https://quickchart.io/qr?text="&ENCODEURL(D2)&"&size=300&margin=4")
```

Nhân viên chỉ cần nhập:

```text
PKHAM | Khu Phòng khám
```

URL và QR tự sinh.

## 6.1. Nguyên tắc in

Giai đoạn thử nghiệm:

- dùng QR sinh trong Sheet;
- in giấy thường;
- không cần lưu ảnh riêng.

Giai đoạn chính thức:

- xuất QR thành PNG/SVG;
- lưu vào Drive;
- dùng QR thực trong mẫu biển chính thức.

---

# 7. Dẫn đường: không dùng Dijkstra ở phiên bản đầu

Không xây graph phức tạp.

Không dùng:

```text
Node → Edge → Dijkstra → Route
```

Ở phiên bản đầu, dùng:

```text
Vị trí hiện tại
      +
Điểm đến
      ↓
Tra bảng hướng dẫn
      ↓
Hiển thị hướng dẫn đã viết sẵn
```

## 7.1. Ví dụ

| Từ | Đến | Hướng dẫn |
|---|---|---|
| PKHAM | XN | Ra khỏi khu Phòng khám → đi thẳng đến sảnh → rẽ trái → Khoa Xét nghiệm ở bên phải |
| PKHAM | NT | Ra khỏi khu Phòng khám → đi về phía quầy thu viện phí → Nhà thuốc nằm đối diện |
| XN | PKHAM | Ra khỏi Khoa Xét nghiệm → rẽ phải → đi thẳng qua sảnh → Khu Phòng khám ở phía trước |

## 7.2. Không cần viết mọi tổ hợp

Không cần tạo hàng trăm tuyến.

Chỉ nhập khoảng:

- 30–60 tuyến phổ biến;
- tập trung vào các luồng bệnh nhân thực tế.

Ví dụ:

- Sảnh → Phòng khám.
- Phòng khám → Xét nghiệm.
- Phòng khám → X-quang.
- Phòng khám → Nhà thuốc.
- Phòng khám → Thu viện phí.
- Xét nghiệm → Phòng khám.
- X-quang → Phòng khám.
- Sảnh → Cấp cứu.
- Sảnh → Khoa Nội.
- Sảnh → Khoa Ngoại.

Nếu không có tuyến:

> Chưa có hướng dẫn chi tiết cho tuyến này. Vui lòng xem sơ đồ hoặc gọi quầy hướng dẫn.

---

# 8. Sơ đồ bệnh viện

Không làm bản đồ indoor động ở V1.

Dùng:

- ảnh PNG/JPG; hoặc
- SVG tĩnh.

Mỗi tầng có một ảnh.

Ví dụ:

```text
Tang_1.png
Tang_2.png
Tang_3.png
```

Website có thể hiển thị:

- sơ đồ tầng hiện tại;
- sơ đồ tầng đích;
- cho phép pinch-to-zoom trên điện thoại.

Không vẽ đường màu động ở giai đoạn đầu.

---

# 9. Số lượng và vị trí QR

Không dán QR khắp mọi nơi.

Mục tiêu ban đầu:

**15–25 QR cho toàn bệnh viện.**

Các vị trí ưu tiên:

- Sảnh chính.
- Quầy tiếp đón.
- Khu Phòng khám.
- Thang máy.
- Cầu thang chính.
- Giao điểm hành lang khó.
- Xét nghiệm.
- Chẩn đoán hình ảnh.
- Nhà thuốc.
- Thu viện phí.
- Cấp cứu.
- Cửa khoa lớn.

QR là **điểm xác nhận vị trí**, không thay thế biển chỉ dẫn vật lý.

---

# 10. Khu Phòng khám: chỉ dùng một QR chung

## 10.1. Phạm vi

Khu Phòng khám có 2 tầng.

Không tạo QR riêng cho từng phòng khám chuyên khoa nhỏ.

Chỉ dùng một ID:

```text
PKHAM
```

Toàn bộ Khu Phòng khám dùng chung một QR.

Có thể in cùng mã QR và dán ở nhiều vị trí thuận tiện của khu nếu cần.

## 10.2. Trang Khu Phòng khám

Khi quét QR:

```text
KHU PHÒNG KHÁM
Tầng 1–2

[ TÌM KHOA/PHÒNG KHÁM ]

[ GÓI KHÁM SỨC KHỎE ]

[ GIÁ DỊCH VỤ ]

[ KỸ THUẬT BỆNH VIỆN ]

[ HƯỚNG DẪN KHÁM ]

[ TÌM ĐƯỜNG TOÀN BỆNH VIỆN ]

[ GỌI HỖ TRỢ ]
```

## 10.3. Các phòng khám chuyên khoa nhỏ

Không xem mỗi phòng khám nhỏ là node dẫn đường.

Chỉ lưu như nội dung:

| Chuyên khoa | Tầng | Phòng |
|---|---:|---|
| Nội tổng hợp | 1 | 101 |
| Ngoại | 1 | 103 |
| Tai Mũi Họng | 2 | 201 |
| Răng Hàm Mặt | 2 | 203 |
| Sản phụ khoa | 2 | 205 |

Khi bệnh nhân chọn:

> Tai Mũi Họng

hiển thị:

> Tầng 2 – Phòng 201.  
> Từ sảnh khu Phòng khám đi thang máy/cầu thang lên tầng 2, rẽ trái.

Không cần thuật toán tìm đường nội bộ giữa các phòng.

---

# 11. Nội dung riêng cho Khu Phòng khám

Khu Phòng khám là nơi nên có nội dung phong phú nhất.

## 11.1. Thông tin cơ bản

- Tên khu.
- Tầng.
- Giờ làm việc.
- Số điện thoại.
- Hotline hỗ trợ.
- Danh sách các phòng khám chuyên khoa.

## 11.2. Gói khám bệnh / khám sức khỏe

Chia thành:

### Khám bệnh thông thường

- Khám Nội.
- Khám Ngoại.
- Khám Nhi.
- Tai Mũi Họng.
- Răng Hàm Mặt.
- Sản phụ khoa.
- Các chuyên khoa thực tế của bệnh viện.

### Gói khám sức khỏe

Ví dụ:

- Gói cơ bản.
- Gói tiêu chuẩn.
- Gói nâng cao.
- Gói người cao tuổi.
- Gói khám cơ quan/doanh nghiệp.
- Gói lái xe nếu có.
- Gói đi học/đi làm nếu có.

Mỗi gói nên có:

- Tên gói.
- Đối tượng phù hợp.
- Nội dung chính.
- Giá tham khảo.
- Thời gian dự kiến.
- Chuẩn bị trước khám.
- Hotline tư vấn.

## 11.3. Giá dịch vụ

Không đưa bảng giá dài lên một màn hình.

Chia nhóm:

- Giá khám.
- Xét nghiệm phổ biến.
- Siêu âm.
- X-quang.
- Điện tim.
- Gói khám.

Mỗi dòng:

```text
Tên dịch vụ | Giá tham khảo | Ghi chú
```

Có cảnh báo:

> Giá mang tính tham khảo và có thể thay đổi theo quy định của bệnh viện.

## 11.4. Kỹ thuật bệnh viện có thể thực hiện

Chia nhóm dễ hiểu:

### Xét nghiệm

- Huyết học.
- Sinh hóa.
- Nước tiểu.
- Đường huyết.
- Mỡ máu.
- Gan.
- Thận.
- Các xét nghiệm thực tế khác.

### Chẩn đoán hình ảnh

- X-quang.
- Siêu âm.
- CT/MRI nếu có.

### Thăm dò chức năng

- ECG.
- Holter nếu có.
- Hô hấp ký nếu có.

### Thủ thuật

- Thay băng.
- Cắt chỉ.
- Khâu vết thương.
- Tiêm truyền.
- Khí dung.
- Nội soi.
- Tiểu phẫu.
- Các kỹ thuật thực tế khác.

Mỗi kỹ thuật nên có:

- Tên.
- Khoa thực hiện.
- Mô tả ngắn.
- Có cần chỉ định hay không.
- Nút dẫn đường đến nơi thực hiện.

## 11.5. Hướng dẫn khám

Nên có:

- Đi khám cần mang gì?
- BHYT cần giấy tờ gì?
- Có cần nhịn ăn không?
- Chuẩn bị xét nghiệm máu.
- Chuẩn bị siêu âm.
- Quy trình khám.
- Nhận kết quả ở đâu.
- Sau khi có kết quả quay lại đâu.

## 11.6. “Tôi cần đi đâu tiếp?”

Các nút nhanh:

```text
Tôi cần làm xét nghiệm
Tôi cần chụp X-quang
Tôi cần siêu âm
Tôi cần đóng tiền
Tôi cần mua thuốc
Tôi cần quay lại Phòng khám
```

Mỗi nút gọi module dẫn đường chung.

---

# 12. Tích hợp nội dung với dẫn đường

Đây là nguyên tắc quan trọng.

Mỗi nội dung có liên quan đến một địa điểm phải có `DIA_DIEM_ID`.

Ví dụ:

| Kỹ thuật | Khoa thực hiện | DIA_DIEM_ID |
|---|---|---|
| Siêu âm bụng | Chẩn đoán hình ảnh | CDHA |
| X-quang phổi | Chẩn đoán hình ảnh | CDHA |
| Công thức máu | Xét nghiệm | XN |

Website tự tạo nút:

```text
[DẪN ĐƯỜNG ĐẾN ĐÂY]
```

Nếu bệnh nhân vào từ QR `PKHAM`, hệ thống biết:

```text
FROM = PKHAM
TO = XN
```

và hiển thị hướng dẫn tương ứng.

---

# 13. Cấu trúc Google Sheets đề xuất

Dùng một file Google Sheets duy nhất.

## 13.1. `DIA_DIEM`

| ID | Tên | Tầng | Khu | Loại | Mô tả | Điện thoại | Hiển thị |
|---|---|---|---|---|---|---|---|

Ví dụ:

```text
PKHAM | Khu Phòng khám | 1–2 | A | KHU_KHAM
XN    | Khoa Xét nghiệm | 1 | B | CAN_LAM_SANG
CDHA  | Chẩn đoán hình ảnh | 1 | B | CAN_LAM_SANG
NT    | Nhà thuốc | 1 | A | DICH_VU
VP    | Thu viện phí | 1 | A | DICH_VU
```

## 13.2. `DIEM_QR`

| QR_ID | DIA_DIEM_ID | Tên hiển thị | Vị trí dán | URL | QR | Hiển thị |
|---|---|---|---|---|---|---|

## 13.3. `HUONG_DAN_DI_CHUYEN`

| Từ | Đến | Hướng dẫn | Sơ đồ liên quan | Hiển thị |
|---|---|---|---|---|

## 13.4. `THONG_TIN_BV`

| Mã | Nhóm | Tiêu đề | Nội dung | Thứ tự | Hiển thị |
|---|---|---|---|---|---|

Nhóm có thể gồm:

- Giới thiệu.
- Giờ làm việc.
- BHYT.
- Liên hệ.
- Hướng dẫn.
- FAQ.

## 13.5. `PHONG_KHAM_CHUYEN_KHOA`

| ID | Tên chuyên khoa | Tầng | Phòng | Mô tả | Hiển thị |
|---|---|---|---|---|---|

## 13.6. `GOI_KHAM`

| ID | Tên gói | Nhóm | Đối tượng | Nội dung | Giá | Chuẩn bị | Hiển thị |
|---|---|---|---|---|---|---|---|

## 13.7. `GIA_DICH_VU`

| ID | Nhóm | Tên dịch vụ | Giá tham khảo | Ghi chú | DIA_DIEM_ID | Hiển thị |
|---|---|---|---|---|---|---|

## 13.8. `KY_THUAT`

| ID | Nhóm | Tên kỹ thuật | Mô tả | Khoa thực hiện | DIA_DIEM_ID | Cần chỉ định | Hiển thị |
|---|---|---|---|---|---|---|---|

## 13.9. `HUONG_DAN`

| ID | Nhóm | Tiêu đề | Nội dung | Hiển thị |
|---|---|---|---|---|

## 13.10. `SO_DO_TANG`

| Tầng | Khu | Link ảnh | Ghi chú | Hiển thị |
|---|---|---|---|---|

## 13.11. `CAU_HINH`

| Khóa | Giá trị | Mô tả |
|---|---|---|

Ví dụ:

```text
TEN_BV
LOGO_URL
HOTLINE
MAU_CHU_DAO
URL_GOC
```

## 13.12. `PHAN_HOI`

Google Form tự đổ dữ liệu vào đây.

| Thời gian | Loại lỗi | Vị trí | Nội dung | Trạng thái |
|---|---|---|---|---|

---

# 14. Quản trị dữ liệu

Không làm trang Admin riêng.

Google Sheets chính là trang quản trị.

## 14.1. Bảo vệ cấu trúc

Bắt buộc:

- khóa hàng tiêu đề;
- khóa cột ID;
- dùng Data Validation;
- dùng dropdown;
- dùng TRUE/FALSE chuẩn;
- tô màu cột cho phép sửa;
- cảnh báo ID trùng;
- không cho người dùng xóa cột;
- chỉ 1–2 người có quyền chỉnh sửa chính.

## 14.2. Tab hướng dẫn

Nên có tab đầu tiên:

```text
HUONG_DAN_SU_DUNG
```

Nội dung:

- được sửa cột nào;
- không được sửa cột nào;
- cách thêm địa điểm;
- cách thêm gói khám;
- cách thêm kỹ thuật;
- cách thêm tuyến;
- cách kiểm tra sau khi sửa.

---

# 15. Giao diện dành cho người lớn tuổi

Nguyên tắc:

- chữ body tối thiểu khoảng 18–20px;
- tiêu đề lớn;
- nút cao, dễ bấm;
- ít lựa chọn trên một màn hình;
- nền sáng, tương phản cao;
- không icon đơn độc;
- luôn có chữ;
- không hover;
- không animation thừa;
- không menu nhiều lớp;
- không cuộn ngang;
- luôn có nút Trang chủ;
- luôn có nút Gọi hỗ trợ.

Ví dụ màn hình QR:

```text
KHU PHÒNG KHÁM
Tầng 1–2

📍 BẠN ĐANG Ở ĐÂY

[ TÌM KHOA/PHÒNG ]

[ GÓI KHÁM ]

[ GIÁ DỊCH VỤ ]

[ KỸ THUẬT BỆNH VIỆN ]

[ HƯỚNG DẪN KHÁM ]

[ TÌM ĐƯỜNG ]

[ ☎ GỌI HỖ TRỢ ]
```

---

# 16. Kiosk

Không làm app kiosk riêng.

Kiosk dùng chính website chung.

Ví dụ:

```text
https://benhvienabc.vn/?p=SANH&mode=kiosk
```

Website biết:

- vị trí kiosk cố định = SANH;
- giao diện kiosk;
- nút lớn hơn;
- không cần quét QR;
- tự reset về trang chủ sau 60–90 giây không thao tác.

Một codebase duy nhất cho:

- điện thoại;
- tablet;
- kiosk;
- PC.

---

# 17. Báo lỗi và góp ý

Tạo một Google Form đơn giản:

## BÁO LỖI / GÓP Ý HỆ THỐNG

Các lựa chọn:

```text
QR không quét được
Vị trí không đúng
Hướng dẫn sai
Không tìm thấy khoa/phòng
Thông tin giá sai
Thông tin dịch vụ sai
Khác
```

Có ô:

```text
Mô tả:
.................................
```

Nút:

```text
[BÁO CHO BỆNH VIỆN]
```

Dữ liệu tự đổ vào `PHAN_HOI`.

---

# 18. In QR

## 18.1. Giai đoạn thử nghiệm

Dùng:

- giấy A4 trắng 80–100 gsm;
- in laser/inkjet;
- cắt thành A5;
- cho vào túi ép plastic/bìa nhựa;
- dán băng keo hai mặt.

Ưu điểm:

- rất rẻ;
- sửa nhanh;
- đổi vị trí dễ.

## 18.2. Kích thước

Khổ đề xuất:

- A5.

QR:

- khoảng 5 × 5 cm;
- nếu hành lang rộng: 6–8 cm.

QR:

- đen trên trắng;
- không dùng màu cầu kỳ;
- có khoảng trắng xung quanh.

## 18.3. Nội dung biển QR

Mẫu:

```text
BỆNH VIỆN ABC
HƯỚNG DẪN BỆNH NHÂN

📍 BẠN ĐANG Ở

KHU PHÒNG KHÁM
TẦNG 1–2

QUÉT QR ĐỂ TÌM KHOA/PHÒNG

[ QR ]

Tìm khoa/phòng • Xét nghiệm
Nhà thuốc • Thu viện phí

Mã vị trí: PKHAM

Hotline hỗ trợ: xxxxxxxxx

Bị lạc? Quét QR gần nhất hoặc gọi quầy hướng dẫn.
```

Không biến biển QR thành poster quảng cáo.

Mục tiêu:

**hiểu trong 2–3 giây từ khoảng cách 1–2 m.**

## 18.4. Sau pilot

Khi hệ thống ổn định:

- giấy couche 180–250 gsm + ép plastic mờ; hoặc
- decal PP/PVC cán mờ.

Sau vài tháng:

- mica 2–3 mm;
- hoặc decal cố định;
- in UV nếu muốn.

Không làm biển kim loại/mica hàng loạt ngay từ đầu.

---

# 19. Backup và bàn giao

## 19.1. Backup dữ liệu

Dùng:

1. Version History của Google Sheets.
2. Apps Script backup 1 lần/tuần.
3. Copy file vào thư mục:

```text
Backup_HuongDan_BenhVien
```

4. Định kỳ tải `.xlsx`.

## 19.2. Quyền sở hữu

Không để hệ thống thuộc Gmail cá nhân của lập trình viên.

Phải thuộc:

- tài khoản bệnh viện;
- tài khoản CNTT;
- hoặc Shared Drive tổ chức.

## 19.3. Bàn giao

Cần tối thiểu:

- tài liệu 1–2 trang: sửa nội dung ở đâu;
- tài liệu: cách thêm QR;
- tài liệu: cách thêm gói khám;
- tài liệu: cách sửa giá;
- tài liệu: cách sửa tuyến đường;
- tài liệu: cách phục hồi backup.

Source code website cần lưu lại đầy đủ.

---

# 20. Domain

## Giai đoạn thử nghiệm

Không bắt buộc.

Có thể dùng:

```text
benhvienabc.pages.dev
```

## Khi triển khai chính thức

Nên dùng subdomain bệnh viện:

```text
huongdan.benhvienabc.vn
```

hoặc website chính:

```text
benhvienabc.vn
```

Lợi ích:

- QR không phụ thuộc hosting;
- sau này đổi Cloudflare/GitHub vẫn giữ URL;
- không phải in lại QR.

---

# 21. Lộ trình MVP

## Giai đoạn 1: Pilot nhỏ

Chỉ triển khai:

- Sảnh chính.
- Khu Phòng khám.
- Xét nghiệm.
- Chẩn đoán hình ảnh.
- Nhà thuốc.
- Thu viện phí.

Khoảng:

**5–10 QR.**

## Giai đoạn 2: Nội dung

Nhập:

- thông tin bệnh viện;
- danh sách khoa/phòng;
- 10–20 kỹ thuật phổ biến;
- vài gói khám;
- giá dịch vụ phổ biến;
- hướng dẫn khám;
- 10–20 tuyến chính.

## Giai đoạn 3: Test thực tế

Test với:

- 5–10 nhân viên không tham gia làm dự án;
- 10–20 bệnh nhân/người nhà;
- đặc biệt người lớn tuổi.

Quan sát:

- có quét được QR không?
- có hiểu “Bạn đang ở đâu” không?
- có tìm được nơi cần đến không?
- có đọc được chữ không?
- có giảm hỏi đường không?

## Giai đoạn 4: Sửa

Sửa:

- tên nút;
- thứ tự;
- hướng dẫn;
- kích thước chữ;
- vị trí QR;
- các tuyến thiếu.

## Giai đoạn 5: Mở rộng

Chỉ sau khi pilot ổn định mới mở rộng ra:

- khoa điều trị;
- các tầng khác;
- khu khác.

---

# 22. Chức năng MVP chính thức

Phiên bản 1 chỉ cần:

1. Trang chủ bệnh viện.
2. Giới thiệu bệnh viện.
3. Danh sách khoa/phòng.
4. Tìm kiếm.
5. Thông tin Khu Phòng khám.
6. Gói khám.
7. Giá dịch vụ.
8. Kỹ thuật bệnh viện.
9. Hướng dẫn khám.
10. Dẫn đường.
11. Sơ đồ tầng tĩnh.
12. Hotline.
13. Báo lỗi/góp ý.
14. Kiosk mode.
15. QR tự sinh.

Không thêm chức năng khác cho đến khi V1 chạy ổn.

---

# 23. Những gì tuyệt đối không làm ở V1

- Chatbot AI.
- Bản đồ động.
- AR.
- BLE.
- Wi-Fi positioning.
- UWB.
- HIS.
- App native.
- Đăng nhập bệnh nhân.
- Đặt lịch phức tạp.
- Thanh toán.
- Dashboard phức tạp.
- Dijkstra/graph phức tạp.
- CMS riêng.
- Backend riêng.
- Database riêng.

---

# 24. Tiêu chí thành công

Hệ thống được coi là thành công nếu:

1. Bệnh nhân quét QR và hiểu ngay mình đang ở đâu.
2. Người lớn tuổi vẫn sử dụng được.
3. Bệnh nhân tìm được ít nhất các điểm phổ biến.
4. Nhân viên giảm việc trả lời câu hỏi đường.
5. Nhân viên bệnh viện tự sửa được:
   - tên khoa;
   - giá;
   - gói khám;
   - kỹ thuật;
   - giờ làm việc;
   - số điện thoại;
   - nội dung hướng dẫn.
6. Không phải gọi lập trình viên cho các thay đổi nội dung thông thường.
7. Không phải quản trị server.
8. Hệ thống có thể bàn giao cho một cán bộ biết Excel/Google Sheets.
9. QR vẫn dùng được khi nội dung thay đổi.
10. Có backup và phục hồi đơn giản.

---

# 25. Kiến trúc cuối cùng

```text
                       WEBSITE BỆNH VIỆN
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
    Thông tin                Dịch vụ              Tìm đường
        │                      │                      │
  Giới thiệu              Gói khám             QR vị trí
  Khoa/phòng              Giá                  Chọn đích
  Liên hệ                 Kỹ thuật             Hướng dẫn chữ
  Hướng dẫn               Chuẩn bị             Sơ đồ tĩnh
        │                      │                      │
        └──────────────────────┼──────────────────────┘
                               │
                         GOOGLE SHEETS
                               │
                   Nhân viên bệnh viện sửa
```

Người dùng có thể truy cập theo hai cách:

### Cách 1: Website bình thường

```text
benhvienabc.vn
```

→ xem thông tin bệnh viện.

### Cách 2: Quét QR

```text
benhvienabc.vn/?p=PKHAM
```

→ website biết vị trí hiện tại.

Từ bất kỳ trang nào:

```text
[DẪN ĐƯỜNG ĐẾN ĐÂY]
```

---

# 26. Kết luận

Dự án không nên được coi là một “phần mềm bệnh viện lớn”.

Nó nên được triển khai như một:

> **Cổng thông tin bệnh viện mini + hệ thống QR xác định vị trí + hướng dẫn đường đi đơn giản.**

Cấu trúc tối ưu:

```text
1 website tĩnh
+
1 Google Sheet
+
15–25 QR
+
sơ đồ tầng tĩnh
+
Google Form phản hồi
+
backup định kỳ
```

Đây là lựa chọn phù hợp với bệnh viện nhỏ vì:

- gần như miễn phí;
- không cần server;
- không cần HIS;
- không cần database phức tạp;
- không cần app;
- dễ sửa;
- dễ đào tạo;
- dễ bàn giao;
- dễ mở rộng khi cần;
- không phụ thuộc nhiều vào lập trình viên.

Nguyên tắc cuối cùng:

> **90% thay đổi phải sửa được bằng Google Sheets; code chỉ dùng cho phần giao diện và logic nền rất ít khi thay đổi.**
