# Prototype — T1 Y tế · Nhóm TyDo

**File:** `index.html`. Mở trực tiếp bằng trình duyệt, không cần cài đặt, không cần đăng nhập, không có backend.

- **Công cụ:** HTML, CSS và JavaScript thuần, viết trong một file.
- **Lời gọi AI thật:** không có. Việc phân luồng chạy bằng luật từ khoá trong trình duyệt, theo thứ tự 4 nhánh ở §1 và danh sách dấu hiệu đỏ ở §6.
- **Phần mock:** sản phẩm (sản phẩm mẫu, không dùng tên thuốc thật), màn hình phía dược sĩ, bản đồ bệnh viện, gửi ảnh đơn thuốc, đặt hàng.

## Cách bấm

| Nút trên đầu trang | Ca | Thấy gì |
| --- | --- | --- |
| **KB1** | Bé 2 tuổi sốt kèm co giật (ca demo bắt buộc) | **Màn hình 1:** cảnh báo đỏ, nút 115, khoá giỏ hàng, ca ưu tiên đỏ ở phía dược sĩ, đồng hồ 60 giây để dược sĩ gọi lại |
| **KB2** | Bầu 3 tháng đòi kháng sinh | Thẻ cam từ chối, **không có nút bán**, chuyển dược sĩ kèm tóm tắt |
| **Ca thường · Đau họng** | Chưa rõ ai dùng thuốc | **Màn hình 2:** sàng lọc 1 chạm, rồi tuỳ lựa chọn mà ra thẻ thuốc (người lớn khoẻ) hoặc chuyển dược sĩ (nhóm nguy cơ) |
| **KB11** | Chữ "lơ mơ" nằm lẫn trong đơn mua khẩu trang | Dấu hiệu đỏ được xét **trước** mọi yêu cầu khác |
| **KB9** | Hỏi tồn kho nước muối | AI nói rõ không xem được tồn kho, không bịa "còn hàng" |

Có thể **gõ một ca lạ** vào ô chat để thử phân luồng ngay khi demo.

**Mở thẳng một cảnh** (để chụp ảnh): `index.html#kb1`, `#kb2`, `#screen2`, `#screen2-ok`.

## Ảnh đánh số

Bật ô "Hiện số chú thích" để thấy các số [1]–[6]. Bảng ý nghĩa từng số nằm trong `spec.md` §5 và ở khung "Chú thích" ngay trên trang.

| Ảnh | Nội dung | Các số thấy trong ảnh |
| --- | --- | --- |
| `anh-kb1.png` | Màn hình 1 · KB1 cấp cứu | [1] nhãn AI và banner đỏ có căn cứ · [2] Gọi 115 · [3] Tìm khoa Cấp cứu · [4] thanh khoá đặt thuốc |
| `anh-kb2.png` | KB2 · từ chối thuốc kê đơn | [1] · thẻ cam có căn cứ · không có nút bán |
| `anh-screen2.png` | Màn hình 2 · sàng lọc 1 chạm | [5] |
| `anh-screen2-ok.png` | Màn hình 2 · kết quả sau sàng lọc | [6] căn cứ an toàn · Sửa lựa chọn · ô đồng ý lưu hồ sơ (mặc định không tick) |
