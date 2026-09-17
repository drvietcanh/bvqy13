WEBSITE BVQY13 V1

1. Chạy thử:
- Giải nén thư mục.
- Có thể mở index.html để xem giao diện; để tránh hạn chế trình duyệt, nên dùng một static server đơn giản.

2. Test QR/vị trí:
- Mở: index.html?p=CONG
- Hoặc: index.html?p=B_T4_TM
- Hoặc: index.html?p=C_T1_TM
Website sẽ nhận vị trí và cho chọn khoa/phòng.

3. Dữ liệu:
- assets/data.js hiện chứa dữ liệu mẫu lấy từ V3.
- assets/config.js chứa tên BV, địa chỉ, hotline và chỗ để URL Google Sheets CSV.
- Khi đưa Google Sheets lên, bước tiếp theo là thay data.js bằng bộ đọc CSV/API công khai.

4. Không sửa ID QR sau khi đã in.
5. Chưa in QR chính thức trước khi có URL website ổn định.
6. Các câu rẽ trái/phải, số mét, cửa cụ thể chưa được tự suy đoán; cần khảo sát thực địa.
