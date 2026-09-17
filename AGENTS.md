# AGENTS.md — Hướng dẫn Codex cho dự án BVQY13

## 1. Vai trò và mục tiêu
Tiếp tục phát triển **Cổng thông tin & Hệ thống dẫn đường QR Bệnh viện Quân y 13**.

- Tên: **Bệnh viện Quân y 13**
- Địa chỉ: **54 An Dương Vương, phường Quy Nhơn Nam, tỉnh Gia Lai**
- Mục tiêu: đơn giản, chi phí thấp, dễ bàn giao; người quản trị nội dung có thể không chuyên CNTT.
- Một website duy nhất gồm: tìm khoa/phòng, dẫn đường QR, thông tin BV/khoa, tin bài, dịch vụ-kỹ thuật-giá-gói khám, hướng dẫn/FAQ, liên hệ/phản hồi.
- **Google Sheets là CMS/dữ liệu quản trị chính** để phần lớn thay đổi nội dung không cần sửa code.

## 2. Phải đọc trước khi sửa
1. `database/Cong_thong_tin_Dan_duong_BVQY13_V3.xlsx` — schema và dữ liệu V3.
2. `reference/PROJECT_ORIGINAL.md` — đặc tả ban đầu.
3. `bvqy13_web_v1/` — prototype V1.

Không tự thay đổi dữ kiện bệnh viện khi chưa có nguồn xác nhận.

## 3. Nguyên tắc bắt buộc
- Không hard-code khoa/phòng, giá, bài viết, kỹ thuật nếu dữ liệu đó thuộc CMS.
- Không đổi/tái sử dụng `QR_ID` đã phát hành. QR chứa URL ổn định + location ID, dạng `/?p=LOCATION_ID`.
- QR thang máy phân biệt tầng, ví dụ `B_T4_TM`.
- Không lưu dữ liệu bệnh nhân, bệnh án, thông tin sức khỏe cá nhân hay dữ liệu nội bộ nhạy cảm trong CMS công khai.
- Không tự suy đoán trái/phải, số mét, cửa, cầu thang hay lối đi chưa khảo sát. Tuyến chưa xác minh phải ghi `KHUNG`/cần khảo sát.
- Mobile-first; chữ/nút lớn; tương phản tốt; đơn giản cho người cao tuổi.
- Không thêm framework, database, server hay dependency phức tạp nếu HTML/CSS/JS thuần đủ dùng.
- Ưu tiên code dễ đọc, dễ bàn giao; có fallback khi nguồn Google Sheets lỗi.
- Không để một lỗi dữ liệu làm trắng toàn bộ website.

## 4. Kiến trúc mong muốn
Refactor V1 theo hướng mô-đun, có thể điều chỉnh nếu có lý do kỹ thuật rõ ràng:

```text
/
├── index.html
├── README.md
├── AGENTS.md
├── css/
│   ├── variables.css
│   ├── layout.css
│   └── components.css
├── js/
│   ├── config.js
│   ├── api.js
│   ├── router.js
│   ├── search.js
│   ├── navigation.js
│   ├── articles.js
│   └── app.js
├── assets/
│   ├── logo/
│   ├── icons/
│   ├── images/
│   └── maps/
├── docs/
│   ├── DATABASE.md
│   ├── QR_SYSTEM.md
│   ├── DEPLOYMENT.md
│   └── ADMIN_GUIDE.md
└── tests/
```
Không tạo file rỗng chỉ để giống cây thư mục.

## 5. Schema V3 cần giữ tương thích
`01_CAU_HINH`, `10_DIA_DIEM`, `11_DIEM_QR`, `12_TUYEN_DUONG`, `13_SO_DO`, `14_HUONG_DAN_DICH`, `20_KHOA_PHONG`, `21_NHAN_SU_LANH_DAO`, `22_THONG_TIN_BV`, `30_BAI_VIET`, `31_CHUYEN_MUC`, `40_DICH_VU`, `41_KY_THUAT`, `42_GIA_DICH_VU`, `43_GOI_KHAM`, `50_HUONG_DAN_BN`, `51_FAQ`, `60_LIEN_HE`, `90_PHAN_HOI`, `99_KIEM_TRA_DU_LIEU`.

Nếu cần migration schema, phải ghi rõ và tránh phá dữ liệu cũ.

## 6. Logic dẫn đường đã xác nhận
- Cánh B ↔ Cánh A ↔ Cánh C.
- Mỗi cánh A/B/C có thang máy ở khu trung tâm.
- Cánh C có lối hông phải sang khu N2/N1/N3.
- Từ Cổng có thể đi theo đường sang khu N2, N1, N3.
- N2: Khoa Nội thần kinh – Tâm thần, cả tòa, 2 tầng.
- N3: Khoa Nội truyền nhiễm – Dị ứng – Da liễu, cả tòa, 2 tầng.
- N1 tầng 1: Tài chính; Văn thư.
- Giai đoạn này dùng route text/predefined routes, không làm indoor positioning/Dijkstra phức tạp.

## 7. Workflow bài viết
Mục tiêu: `Google Form → Google Sheets → CHỜ DUYỆT → ĐÃ DUYỆT → Website`.
Website chỉ hiển thị bài `ĐÃ DUYỆT`.

## 8. Triển khai mục tiêu
`Codex/Git → GitHub → Cloudflare → Website`.
Ưu tiên deploy tự động từ Git. URL thử nghiệm không phải domain vĩnh viễn để in QR hàng loạt.

## 9. Thứ tự công việc
### Milestone 1 — Chuẩn hóa repository
- Đọc toàn bộ V1, V3, tài liệu gốc.
- Refactor source, giữ chức năng V1.
- Viết README/tài liệu kiến trúc.
- Không thay nghiệp vụ nếu không cần.

### Milestone 2 — Data adapter Google Sheets
- Tách fetch/parse/validation khỏi UI.
- Đọc dữ liệu công khai từ Google Sheets.
- Có cache/fallback dữ liệu mẫu.
- Tài liệu hóa cách publish/configure Sheets.

### Milestone 3 — Website chức năng
Trang chủ; tìm khoa/phòng; dẫn đường `?p=QR_ID`; khoa/phòng; tin bài; dịch vụ/kỹ thuật/giá/gói khám; hướng dẫn/FAQ; liên hệ.

### Milestone 4 — Quản trị không chuyên
Google Form đăng bài; quy trình duyệt; hướng dẫn sửa hotline/khoa/giá/kỹ thuật; cảnh báo lỗi dữ liệu thân thiện.

### Milestone 5 — Pilot QR
Test Cổng, thang máy A/B/C từng tầng, lối C→N, N1/N2/N3; viết checklist khảo sát thực địa; không bịa hướng trái/phải.

### Milestone 6 — Deploy
Chuẩn bị GitHub + Cloudflare + `docs/DEPLOYMENT.md`; chỉ khuyến nghị in QR chính thức sau khi domain ổn định.

## 10. Tiêu chí hoàn thành mỗi thay đổi
- Chạy test/lint nếu có.
- Console không có lỗi nghiêm trọng.
- Test mobile.
- Test URL không có `p`, `p` hợp lệ, `p` không hợp lệ.
- Không phá schema V3/QR ID.
- Cập nhật docs nếu đổi config/schema/deploy.
- Nêu rõ placeholder và phần cần khảo sát.

## 11. Cách báo cáo cho chủ dự án
Dùng tiếng Việt, nói rõ: **đã làm gì / người dùng cần làm gì / kiểm tra thế nào**. Không bắt người dùng sửa code cho tác vụ quản trị nội dung. Ưu tiên phương án đơn giản, miễn phí/chi phí thấp, dễ bàn giao.
