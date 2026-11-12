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

**Mở thẳng một cảnh** (để chụp ảnh): `index.html#kb1`, `#kb2`, `#screen2`, `#screen2-ok`, hoặc `#q=<tin nhắn>` để chạy một tin bất kỳ.

## Luật phân luồng (hàm `classify()` trong `index.html`)

Xét theo thứ tự, gặp nhánh nào thì dừng ở nhánh đó:

1. **Dấu hiệu đỏ** (danh sách ở §6, gồm cả quá liều như "uống 10 viên", "uống cả vỉ", và sốt ở trẻ dưới 3 tháng) → cảnh báo đỏ và nút 115. Bước sơ cứu thay đổi theo loại dấu hiệu: co giật, ngộ độc, dị ứng, hay loại khác.
2. **Thuốc kê đơn hoặc danh xưng tự khai** ("tôi là bác sĩ", "nhân viên Pharmacity") → từ chối, không gợi ý thuốc thay thế, chuyển dược sĩ.
3. **Bệnh tế nhị** → chuyển dược sĩ, **không lưu** hồ sơ.
4. **Đã tự dùng thuốc mà không đỡ** (≥ 7 ngày, "không đỡ", "mạnh hơn") → khuyên đi khám và chuyển dược sĩ.
5. **Chỉ hỏi vật tư** → gợi ý ngay. Nếu hỏi tồn kho, AI nói rõ là không có dữ liệu tồn kho.
6. **Nhóm nguy cơ** đã lộ ra trong tin (trẻ nhỏ, người cao tuổi, mang thai hoặc cho con bú, bệnh nền) → chuyển dược sĩ.
7. **Còn lại** → sàng lọc 1 chạm.

Bộ kiểm thử 39 ca (12 kịch bản ở §2, các ca lạ ở §7, và các ca dễ nhận nhầm) đều ra đúng nhánh.

**Giới hạn đã biết:**
- Luật dựa trên từ khoá, nên một cách diễn đạt lạ vẫn có thể lọt. Đây là lỗi còn lại số 1 ở §6.
- Câu phủ định như "bé **không** co giật" vẫn ra cảnh báo đỏ. Nhóm chấp nhận vì cảnh báo thừa rẻ hơn bỏ sót.

## Ảnh đánh số

Bật ô "Hiện số chú thích" để thấy các số [1]–[6]. Bảng ý nghĩa từng số nằm trong `spec.md` §5 và ở khung "Chú thích" ngay trên trang.

| Ảnh | Nội dung | Các số thấy trong ảnh |
| --- | --- | --- |
| `anh-kb1.png` | Màn hình 1 · KB1 cấp cứu | [1] nhãn AI và banner đỏ có căn cứ · [2] Gọi 115 · [3] Tìm khoa Cấp cứu · [4] thanh khoá đặt thuốc |
| `anh-kb2.png` | KB2 · từ chối thuốc kê đơn | [1] · thẻ cam có căn cứ · không có nút bán |
| `anh-screen2.png` | Màn hình 2 · sàng lọc 1 chạm | [5] |
| `anh-screen2-ok.png` | Màn hình 2 · kết quả sau sàng lọc | [6] căn cứ an toàn · Sửa lựa chọn · ô đồng ý lưu hồ sơ (mặc định không tick) |
