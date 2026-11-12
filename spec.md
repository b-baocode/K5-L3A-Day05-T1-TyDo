# Bài nộp LAB5 — T1 Y tế · Nhóm TyDo

**Lớp:** L3A · **Bàn / zone:** B3 · **Track:** T1 — Y tế (trợ lý AI trả lời đầu tiên ở kênh "Hỏi dược sĩ")

| Họ và Tên | Mã Học Viên | Việc đã làm |
| --------- | ----------- | ----------- |
|           |             |             |
|           |             |             |
|           |             |             |

---

## §1. Lát cắt & phạm vi

### Lát cắt (chép từ track)

> Trong **kênh chat Hỏi dược sĩ của Pharmacity**, khi **khách** đang nhắn hỏi "tôi (hoặc người nhà) bị X, có thuốc gì không", **AI tự trả lời và gợi ý sản phẩm, hỏi thêm câu sàng lọc, hay chuyển dược sĩ, khuyên đi khám ngay**, để người dùng thuốc không nhận sai sản phẩm và ca nguy hiểm không nằm trong hàng chờ.

| Sản phẩm | Một người dùng | Một việc | Một quyết định của AI | Một kết quả |
| --- | --- | --- | --- | --- |
| kênh chat Hỏi dược sĩ của Pharmacity | khách (thường hỏi thay người nhà, tối muộn) | nhắn hỏi "tôi (hoặc người nhà) bị X, có thuốc gì không" | tự trả lời và gợi ý sản phẩm, hỏi thêm câu sàng lọc, hay chuyển dược sĩ, khuyên đi khám ngay | người dùng thuốc không nhận sai sản phẩm và ca nguy hiểm không nằm trong hàng chờ |

### AI được thêm quyền gì so với hôm nay

- **Hôm nay** (mục "Sản phẩm thật hôm nay"): AI chỉ trả lời câu **thông tin**: giá, còn hàng, khuyến mãi, nhận diện qua ảnh bao bì. Mọi câu **chuyên môn** ("thuốc gì, dùng thế nào") đều chuyển cho dược sĩ. Ranh giới khá đơn giản: hễ là câu chuyên môn thì chuyển.
- **Khi tích hợp sâu hơn**, AI được thêm ba quyền mới:
  1. **Gợi ý sản phẩm kèm nút đặt** (`goi_y_san_pham`). AI bước vào vùng chuyên môn mà trước đây chỉ dược sĩ làm.
  2. **Tự quyết ca nào cần dược sĩ, ca nào AI tự xử, ca nào phải đi khám ngay.** AI trở thành **người phân luồng** (triage). Đây chính là chỗ trang công khai **chưa nói rõ**: "AI dựa vào dấu hiệu nào để chuyển dược sĩ, và khi chuyển thì khách thấy gì".
  3. **Hỏi câu sàng lọc và lưu câu trả lời** (`luu_ghi_chu_suc_khoe`). AI bắt đầu thu thập dữ liệu sức khoẻ.
- Hệ quả: cái sai nguy hiểm nhất **không còn là trả lời sai giá**, mà là **phân luồng sai**. Ca lẽ ra phải đến dược sĩ hoặc đi khám lại được AI tự xử, hoặc ca cấp cứu bị xếp vào hàng chờ như ca thường.

### Đọc bảng tool như người thiết kế

| Tool | Loại | Đảo lại được không | Ghi chú cho chặng 3–4 |
| --- | --- | --- | --- |
| `goi_y_san_pham` | Thay đổi: gửi gợi ý **kèm nút đặt** | **Không**, khi thuốc đã dùng | Có nút đặt nghĩa là từ gợi ý tới lúc thuốc vào miệng chỉ cách một cú bấm. Không có bước nào để dược sĩ kịp nhìn lại |
| `ban_thuoc_ke_don` | Thay đổi: bán thuốc | **Không** | Thuốc kê đơn cần đơn của bác sĩ. AI qua chat không kiểm được đơn thật hay giả |
| `luu_ghi_chu_suc_khoe` | Thay đổi: ghi vào hồ sơ | Xoá được, **nhưng đã có rủi ro lộ** | Đảo được trên giấy nhưng thực tế không: thông tin đã lộ thì không thu hồi được |
| `chuyen_duoc_si` | Thay đổi kênh | Được, tốn vài phút | Đường lui an toàn, **trừ ca cấp cứu**: với ca cấp cứu, vài phút chờ trong hàng là quá lâu |
| `khuyen_di_kham` | Hiện thông báo | Khách bỏ qua được | Rẻ khi hiện thừa, nhưng có thể chìm nếu hiện sau một gợi ý sản phẩm |

**Việc khách sẽ đòi mà không tool nào làm được:**
- **Tra tồn kho ở một cửa hàng cụ thể.** Tin mồi 1 hỏi "Pharmacity gần mình còn nước muối sinh lý không", nhưng bảng tool **không có** tool tồn kho. Nếu đáp ngay "còn ạ" là AI bịa.
- **Kiểm tra tương tác thuốc** với thuốc khách đang dùng.
- **Xác thực đơn thuốc.**
- **Gọi cấp cứu thay khách.**

Với những việc này, AI nói rõ mình không làm được và chỉ đường thay thế: chuyển dược sĩ, hoặc gọi 115.

### Nếu AI sai, ai chịu gì, và sai hướng nào đắt hơn

Đặc thù của T1: **người chịu hậu quả thường không phải người đang nhắn**. Theo dược sĩ trong track, khách "hay quên nói" người sẽ uống là ai: con nhỏ, cha mẹ già, bà bầu. AI chỉ nghe được người nhắn, còn người nhận thuốc thì im lặng.

| Hướng sai | Ví dụ | Ai chịu, chịu gì | Đảo được không |
| --- | --- | --- | --- |
| **AI tự xử khi lẽ ra phải chuyển** | Khách hỏi "ho có thuốc gì không" cho bố 70 tuổi đang uống thuốc huyết áp. AI không hỏi người dùng là ai và gửi luôn `goi_y_san_pham` | Người bố: tương tác thuốc, hại sức khoẻ | **Không**, thuốc đã uống |
| **Bỏ sót dấu hiệu cấp cứu** | "Mua giúp thuốc hạ sốt, bé nhà mình sốt từ chiều, giờ hơi lơ mơ". Dấu hiệu nguy hiểm nằm lẫn trong một câu hỏi mua thuốc | Đứa trẻ: chậm cấp cứu | **Không** |
| **Đẩy ca cấp cứu vào hàng chờ** | AI nhận ra là ca nặng nhưng chỉ gọi `chuyen_duoc_si`, khách ngồi chờ 10 phút | Người bệnh: mất thời gian vàng | **Không** |
| **Lưu dữ liệu nhạy cảm khi chưa hỏi** | Khách kể đang mang thai 3 tháng, AI tự gọi `luu_ghi_chu_suc_khoe` | Khách: lộ thông tin sức khoẻ; Pharmacity: rủi ro pháp lý | Xoá được, nhưng đã lộ |
| **Quá chậm hoặc hỏi quá nhiều** | Khách hỏi nước muối sinh lý (câu thông tin) cũng bị hỏi 5 câu sàng lọc rồi chờ dược sĩ | Khách gắt và bỏ đi tự mua ở chỗ khác, không có ai tư vấn ("đừng bắt mình chờ") | Được, chỉ mất khách |

**Kết luận chặng 1:** khác với các track về tiền, ở T1 **sai hướng "chậm" cũng có thể nguy hiểm, nhưng chỉ với đúng một loại ca là ca cấp cứu**. Vì vậy quyết định của AI không phải là chọn giữa "tự làm" và "hỏi người", mà có **ba đường**:
1. **Dấu hiệu nguy hiểm**: bảo đi khám hoặc gọi 115 ngay, **không qua hàng chờ** và không gợi ý thuốc.
2. **Chưa rõ người dùng thuốc là ai, hoặc người đó thuộc nhóm nguy cơ** (trẻ nhỏ, người già, bà bầu, có bệnh nền, đang dùng thuốc): chuyển dược sĩ.
3. **Câu thông tin, hoặc người lớn khoẻ mạnh với triệu chứng nhẹ, đã sàng lọc đủ**: AI mới được trả lời hoặc gợi ý.

Chuyển dược sĩ thừa chỉ tốn vài phút, còn gợi ý sai thì không đảo được. Vì vậy, khi không chắc, AI nghiêng về phía chuyển. Mức tự chủ cụ thể cho từng tool sẽ chốt ở §4.

### Việc tính năng KHÔNG làm

1. **Không nói liều dùng**, kể cả với thuốc không kê đơn (ví dụ "bé 5 tuổi uống mấy ml hạ sốt"). Liều phụ thuộc cân nặng, tuổi và thuốc đang dùng, mà AI qua chat không kiểm chứng được. Sai liều ở trẻ nhỏ thì hại ngay và không đảo được. AI chỉ dẫn khách đọc nhãn hoặc hỏi dược sĩ.
2. **Không bán, không tư vấn, không gợi ý "thuốc thay thế" cho thuốc kê đơn** (kháng sinh, thuốc huyết áp…). AI không xác thực được đơn. Gợi ý "loại tương tự" cũng là kê đơn trá hình. Vì vậy prototype **không có nút** cho `ban_thuoc_ke_don`.
3. **Không chẩn đoán bệnh** (không nói "bạn bị viêm họng do vi khuẩn"). AI chỉ **phân luồng** theo dấu hiệu. Nêu tên bệnh khiến khách tin là đã khám xong và không đi khám nữa.
4. **Không tự lưu thông tin sức khoẻ khi khách chưa đồng ý rõ.** Thông tin này dù xoá được thì đã có rủi ro lộ. Ngoài ra, đọc được câu trả lời để sàng lọc trong cuộc chat không có nghĩa là được quyền lưu vào hồ sơ.

> Prototype ở §5 không có nút nào cho 4 việc trên. Khi khách hỏi, AI nói rõ mình không làm được và hiện nút **Chuyển dược sĩ** (hoặc **Gọi 115** nếu có dấu hiệu nguy hiểm).

---

## §2. Kịch bản rủi ro (chặng 2)

_TODO_

## §3. Khi AI sai (chặng 3)

_TODO_

## §4. Mức tự chủ (chặng 4)

_TODO_

## §5. Prototype (chặng 5)

_TODO_

## §6. Tốt là gì (chặng 6)

_TODO_

## §7. Demo

_TODO_
