# Bài nộp LAB5 — T1 Y tế · Nhóm TyDo

**Lớp:** L3A · **Bàn / zone:** B3 · **Track:** T1 — Y tế (trợ lý AI trả lời đầu tiên ở kênh "Hỏi dược sĩ")

| Họ và Tên | Mã Học Viên | Việc đã làm |
| --------- | ----------- | ----------- |
|           |             |             |
| Nguyễn Quang Trung | 2A202603389 | **Thành viên 4 — Thiết kế Trải nghiệm & Prototype (UX & UI Lead):** Thiết kế UX khi AI sai / gặp ca nguy hiểm (§3), xây dựng & đặc tả Prototype 2 màn hình Zalo OA (Cảnh báo đỏ 115 & Sàng lọc nhân thân 1 chạm), ánh xạ nguyên tắc HAX/PAIR 1→6 (§5) |
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

### Kịch bản đắt nhất 1 (KB 1* — Ca cấp cứu lọt vào luồng mua thuốc)

> **Tình huống (khách nhắn lúc nửa đêm):** *"Bé nhà mình 2 tuổi sốt 39.5 độ từ trưa, giờ người hơi co giật và thở dốc, bán cho vỉ hạ sốt giao gấp!"*

#### 1. Câu AI nói nguyên văn (Bật chế độ Cảnh báo đỏ)

> **🚨 CẢNH BÁO Y TẾ KHẨN CẤP — CẦN ĐI KHÁM NGAY**
> Dấu hiệu sốt cao kèm co giật ở trẻ nhỏ là tình trạng nguy hiểm cần can thiệp y tế ngay lập tức, tuyệt đối không được chờ giao thuốc:
>
> **🚑 HÀNH ĐỘNG NGAY BÂY GIỜ:**
> 1. Gọi ngay **Cấp cứu 115** hoặc đưa bé đến khoa Cấp cứu bệnh viện gần nhất.
> 2. Đặt bé nằm nghiêng một bên nơi thoáng mát, nới lỏng quần áo, chườm ấm (nước ấm, không dùng nước đá/cồn).
> 3. Không cố nhét thuốc hay bất cứ vật gì vào miệng bé lúc đang co giật.
>
> *Cuộc trò chuyện của bạn đã được ưu tiên chuyển thẳng tới Dược sĩ trực online.*

*(Câu thoại hoàn toàn không hứa giao thuốc, không báo giá, không hẹn giờ ship — tránh tạo ảo giác rằng chờ mua thuốc tại nhà là giải pháp an toàn).*

#### 2. Người dùng thấy gì · Làm gì tiếp · Ai được báo, sau bao lâu

- **Người dùng thấy gì trên giao diện (Màn hình 1 — Emergency Red Screen):**
  1. **Banner cảnh báo đỏ rực (Emergency Red Banner)** trên đỉnh khung chat với biểu tượng còi cứu thương nhấp nháy: `CẢNH BÁO NGUY HIỂM: DẤU HIỆU SỐT CO GIẬT` kèm căn cứ phát hiện từ khóa báo động đỏ (`"2 tuổi"`, `"sốt 39.5 độ"`, `"co giật"`, `"thở dốc"`).
  2. **Nút bấm to màu đỏ (CTA chính):** `[📞 BẤM GỌI CẤP CỨU 115 NGAY LẬP TỨC]` (bấm mở trình quay số `tel:115` ngay trên điện thoại).
  3. **Nút định vị phụ:** `[🏥 Tìm khoa Cấp cứu / Bệnh viện Nhi gần nhất]` (mở bản đồ chỉ đường tới cơ sở cấp cứu gần nhất).
  4. **Khóa luồng thương mại:** Ẩn hoàn toàn giỏ hàng, thanh tìm kiếm sản phẩm và mọi nút đặt mua thuốc (`goi_y_san_pham`); hiện thanh trạng thái: *"Đang kết nối ưu tiên số 1 tới Dược sĩ trực online"*.
- **Người dùng làm gì tiếp:**
  - **Bước chính:** Bấm gọi ngay `115` hoặc mở định vị bệnh viện gần nhất, đồng thời thực hiện 3 bước sơ cứu an toàn tại chỗ.
  - **Trường hợp khách bấm nhầm / mô tả nhầm (sửa lỗi):** Có nút phụ nhỏ ở dưới `[Tôi gõ nhầm, bé không bị co giật — Quay lại hỏi Dược sĩ]` để đính chính, nhưng vẫn chuyển sang dược sĩ xác minh chứ AI không tự mở lại giỏ hàng.
  - **Nếu khách không bấm gì:** Màn hình giữ nguyên cảnh báo đỏ, không tự trôi đi; phía nhà thuốc chủ động liên hệ qua kênh ưu tiên.
- **Ai được báo, sau bao lâu:**
  - Hệ thống kích hoạt đồng thời `khuyen_di_kham` và `chuyen_duoc_si` với cờ **`PRIORITY_RED_FLAG` trong < 1 giây (tức thì, 0 giây chờ hàng đợi thường)**.
  - **Dược sĩ trực online** nhận thông báo đẩy khẩn cấp kèm thẻ tóm tắt lâm sàng (Đối tượng: Trẻ 2 tuổi · Triệu chứng đỏ: Sốt 39.5°C, co giật, thở dốc · Trạng thái: Đã phát cảnh báo 115). Nếu sau **60 giây** khách chưa phản hồi trên khung chat, dược sĩ trực chủ động gọi điện trực tiếp qua Zalo/SĐT để hỗ trợ gia đình.

#### 3. Quyết định thiết kế phản hồi của AI

- **Các phương án đã cân nhắc:**
  - *Phương án A — Hỏi lại câu sàng lọc:* Hỏi thêm *"Bé nặng bao nhiêu kg, co giật bao lâu rồi để mình chọn thuốc hạ sốt phù hợp?"*
  - *Phương án B — Trả lời kèm giới hạn (Gợi ý thuốc + câu rào đón):* Vẫn gọi `goi_y_san_pham` gửi gói hạ sốt Hapacol kèm dòng chữ nhỏ *"Lưu ý: Nếu bé nặng hơn nên đưa đi khám"*.
  - *Phương án C — Dừng hoàn toàn luồng bán thuốc, phát cảnh báo cấp cứu (`khuyen_di_kham`) và chuyển thẳng dược sĩ ưu tiên số 1 (`chuyen_duoc_si`).*
- **Chọn phương án:** **Phương án C**.
- **Vì sao chọn C & loại A, B:**
  - **Loại A (Hỏi lại):** Với ca sốt co giật và thở dốc, mỗi phút hỏi qua lại trên chat đều làm mất "thời gian vàng" cấp cứu và khiến phụ huynh bối rối tìm cách trả lời thay vì xử trí đường thở cho bé.
  - **Loại B (Trả lời kèm giới hạn):** Phụ huynh đang hoảng loạn lúc nửa đêm sẽ bấm ngay nút `[Đặt mua]` của `goi_y_san_pham` vì tâm lý muốn có thuốc ngay và bỏ qua dòng chữ cảnh báo nhỏ (`khuyen_di_kham` bị chìm sau gợi ý sản phẩm — đúng rủi ro đã chỉ ra ở §1). Hơn nữa, nhét thuốc uống khi trẻ đang co giật gây sặc vào đường thở, nguy hiểm tính mạng.
  - **Chọn C:** Cắt đứt hoàn toàn khả năng đặt thuốc sai và đưa hành động cứu mạng (`Gọi 115` + sơ cứu nằm nghiêng, không nhét vật lạ vào miệng) lên vị trí duy nhất trên màn hình.

---

### Kịch bản đắt nhất 2 (KB 2* — Thai phụ đòi mua kháng sinh không đơn & đối tượng nguy cơ cao)

> **Tình huống:** *"Mình đang bầu 3 tháng, bị viêm họng sổ mũi 3 hôm nay, lấy cho vỉ kháng sinh liều cao uống cho nhanh dứt điểm"*

#### 1. Câu AI nói nguyên văn (Từ chối thuốc kê đơn + Sàng lọc / Chuyển Dược sĩ)

> **⚠️ TỪ CHỐI BÁN KHÁNG SINH KHÔNG ĐƠN — BẢO VỆ AN TOÀN THAI KỲ**
> Trợ lý AI của Pharmacity **không thể xuất bán kháng sinh** khi chưa có đơn của bác sĩ, đặc biệt bạn đang trong **3 tháng đầu thai kỳ** — giai đoạn nhạy cảm nhất với sự phát triển của thai nhi (tự ý dùng kháng sinh liều cao có nguy cơ gây dị tật thai hoặc tác dụng phụ nghiêm trọng).
>
> **👩‍⚕️ BƯỚC TIẾP THEO AN TOÀN CHO BẠN:**
> 1. Hệ thống đã **kết nối trực tiếp cuộc chat này tới Dược sĩ chuyên môn** để tư vấn giải pháp giảm đau họng, sổ mũi an toàn cho mẹ bầu (như dung dịch xịt mũi/súc họng muối sinh lý).
> 2. Nếu bạn **đã có đơn thuốc của bác sĩ sản/tai mũi họng**, vui lòng bấm nút gửi ảnh đơn thuốc bên dưới để Dược sĩ kiểm tra.

#### 2. Người dùng thấy gì · Làm gì tiếp · Ai được báo, sau bao lâu

- **Người dùng thấy gì trên giao diện:**
  - Thẻ thông báo màu vàng cam (Compliance & Safety Card) nêu rõ 2 căn cứ chặn: `Phát hiện từ khóa: "kháng sinh" (Thuốc kê đơn - ETC)` và `Đối tượng nguy cơ cao: "Bầu 3 tháng" (Tam cá nguyệt thứ nhất)`.
  - **Không có nút đặt mua kháng sinh** (`ban_thuoc_ke_don` bị khóa cứng ở tầng API & UI) và AI cũng **không tự gợi ý thuốc thay thế** (`goi_y_san_pham` bị chặn với nhóm bầu).
  - Hiển thị 2 nút hành động rõ ràng: `[👩‍⚕️ Chat ngay với Dược sĩ chuyên môn]` và `[📷 Gửi ảnh đơn thuốc của Bác sĩ]`.
- **Người dùng làm gì tiếp:**
  - Với ca đã nói rõ *"bầu 3 tháng"* như tin nhắn trên: bấm chat với Dược sĩ hoặc gửi ảnh đơn thuốc.
  - Với ca thường mà khách chỉ nhắn *"Mình bị viêm họng sổ mũi 3 hôm, lấy thuốc gì nhanh khỏi?"* (chưa nói ai uống, có bầu không — **Màn hình 2: Sàng lọc nhân thân 1 chạm**): khách thấy **Hộp câu hỏi sàng lọc 1 chạm** (Chọn đối tượng: `Người lớn` / `Trẻ nhỏ` / `Người già`; Tình trạng thai kỳ: `Đang mang thai/cho con bú` / `Không`; Tiền sử: `Có bệnh nền/dị ứng` / `Không`). Chỉ khi bấm xong và thuộc nhóm người lớn khỏe mạnh AI mới mở gợi ý OTC; nếu chọn `Đang mang thai`, hệ thống lập tức khóa gợi ý và chuyển Dược sĩ.
- **Ai được báo, sau bao lâu:**
  - Gọi `chuyen_duoc_si` ngay lập tức (**< 5 giây** đẩy vào hàng chờ ưu tiên nhóm nguy cơ cao, cam kết phản hồi trong **< 2 phút**).
  - **Dược sĩ trực online** nhận được hồ sơ tóm tắt đầu vào: `[Đối tượng: Thai phụ 3 tháng | Triệu chứng: Viêm họng, sổ mũi 3 ngày | Yêu cầu bị chặn: Đòi mua kháng sinh liều cao không đơn]` để tư vấn ngay mà không bắt khách kể lại từ đầu.

#### 3. Quyết định thiết kế phản hồi của AI

- **Các phương án đã cân nhắc:**
  - *Phương án A — Hỏi lại thông tin để AI tự chọn kháng sinh "nhẹ":* Hỏi *"Bạn có dị ứng Penicillin không, mình gợi ý Amoxicillin an toàn cho bà bầu nhé?"*
  - *Phương án B — Trả lời kèm giới hạn (AI tự gợi ý sản phẩm OTC thay thế kèm nút đặt):* Từ chối kháng sinh nhưng AI tự động gọi `goi_y_san_pham` gửi viên ngậm trị đau họng kèm nút mua ngay.
  - *Phương án C — Dừng tư vấn tự động, từ chối thẳng thuốc kê đơn và chuyển Dược sĩ chuyên môn (`chuyen_duoc_si`).*
- **Chọn phương án:** **Phương án C** (và kết hợp **Hộp sàng lọc 1 chạm** ở Màn hình 2 nếu khách chưa khai báo đối tượng).
- **Vì sao chọn C & loại A, B:**
  - **Loại A:** Vi phạm Luật Dược (bán kháng sinh không đơn) và quy tắc bất khả xâm phạm ở §1 (cấm `ban_thuoc_ke_don`).
  - **Loại B:** Nhiều viên ngậm đau họng OTC chứa hoạt chất sát khuẩn/giảm đau (như Dichlorobenzyl alcohol, Ambroxol, tinh dầu nồng độ cao) chống chỉ định hoặc cần thận trọng ở 3 tháng đầu thai kỳ. Để AI tự gọi `goi_y_san_pham` cho bà bầu là rủi ro không đảo ngược được khi thuốc đã uống.
  - **Chọn C:** Đảm bảo 100% ca thuốc kê đơn và đối tượng thai phụ đều do dược sĩ người thật kiểm duyệt.

---

### Phân tích Loại lỗi & Nguyên tắc HAX/PAIR (§3 chung)

#### 1. Phân loại lỗi (Hệ thống / Người dùng / Ngữ cảnh) và cách nói, cách sửa

| Kịch bản | Loại lỗi chính | Vì sao xếp vào loại này | Cách nói & cách sửa đi theo loại lỗi |
| --- | --- | --- | --- |
| **KB 1*** *(Sốt co giật đòi mua hạ sốt)* | **Lỗi ngữ cảnh (Context Error)** *(theo PAIR)* | Nếu AI chạy đúng luồng "khách hỏi thuốc hạ sốt → tìm sản phẩm hạ sốt khớp từ khóa", hệ thống không lỗi code nhưng **sai hoàn toàn với ngữ cảnh sinh tử của người bệnh** (trẻ 2 tuổi đang co giật, thở dốc). | **Cách nói:** Không xin lỗi kiểu lỗi kỹ thuật (*"Xin lỗi tôi không hiểu"*), mà phát thông điệp khẩn cấp dứt khoát, đặt an toàn tính mạng lên trên yêu cầu mua hàng.<br>**Cách sửa:** Sửa ở **cấu trúc luồng và giao diện** — ngắt luồng thương mại, khóa `goi_y_san_pham`, bung màn hình đỏ `115` + sơ cứu. |
| **KB 2*** *(Bầu 3 tháng đòi kháng sinh / khách quên nói ai uống)* | **Lỗi người dùng (User/Input Error)** kết hợp **Ranh giới hệ thống (System Boundary)** | Người dùng yêu cầu hành vi vượt ranh giới pháp lý (mua kháng sinh không đơn) hoặc nhập đầu vào thiếu thông tin nhân thân (quên nói mua cho bà bầu/trẻ nhỏ). | **Cách nói:** Giải thích tôn trọng nhưng kiên quyết về giới hạn pháp lý & y khoa (Luật Dược, an toàn thai nhi 3 tháng đầu), không đổ lỗi cho khách.<br>**Cách sửa:** Cung cấp **đường chuyển tiếp mượt mà** (nút gửi ảnh đơn thuốc / nối Dược sĩ trực) và **Hộp sàng lọc 1 chạm** để khách bổ sung thông tin thiếu chỉ bằng 1 cú bấm. |

#### 2. Ba nguyên tắc HAX/PAIR áp dụng (trỏ vào chỗ cụ thể)

1. **HAX G1 — Làm rõ hệ thống làm được gì *(Make clear what the system can do)* & Minh bạch AI (Điều 11 Luật AI 134/2025/QH15):**
   - **Trỏ vào chỗ cụ thể:** Nhãn định danh ở đầu khung chat Zalo OA *(Vị trí `[1]` trên Prototype)*: `"Trợ lý AI Sàng lọc Ban đầu — Không thay thế bác sĩ/dược sĩ cấp cứu"` và câu mở đầu ở KB 2*: *"Trợ lý AI của Pharmacity không thể xuất bán kháng sinh khi chưa có đơn của bác sĩ"*.
2. **HAX G10 — Thu hẹp phạm vi khi nghi ngờ hoặc nguy hiểm *(Scope services when in doubt)*:**
   - **Trỏ vào chỗ cụ thể:**
     - Ở **KB 1* (Màn hình 1):** Khi phát hiện từ khóa báo động đỏ (`co giật`, `thở dốc`), hệ thống tự động **ẩn hoàn toàn giỏ hàng và khóa nút đặt mua** *(Vị trí `[4]` trên Prototype)*, chỉ để lại luồng cấp cứu.
     - Ở **Ca thường (Màn hình 2):** Khi chưa rõ người uống thuốc là ai, hệ thống khóa nút gợi ý sản phẩm và hiện **Hộp sàng lọc nhân thân 1 chạm** *(Vị trí `[5]`, `[6]` trên Prototype)* — bắt buộc chọn đủ Đối tượng, Thai kỳ, Tiền sử trước khi tư vấn.
3. **PAIR — Errors + Graceful Failure *(Đường thoát hiểm an toàn khi thất bại/ngoài khả năng)*:**
   - **Trỏ vào chỗ cụ thể:** Thay vì chỉ trả lời *"AI không thể tư vấn ca này"*, giao diện cung cấp ngay **đường thoát hiểm y tế thực tế bấm được ngay**: nút đỏ `[📞 BẤM GỌI CẤP CỨU 115 NGAY LẬP TỨC]` *(Vị trí `[2]`)*, nút `[🏥 Tìm khoa Cấp cứu / Bệnh viện Nhi gần nhất]` *(Vị trí `[3]`)*, và tự động đẩy cuộc trò chuyện kèm tóm tắt lâm sàng sang Dược sĩ trực online.

#### 3. Lỗi mà "sửa câu chữ không cứu được" và cách thiết kế cứu nó

- **Lỗi sửa câu chữ không cứu được:** Trong KB 1* hoặc KB 3 (mua Panadol Extra cho người già tăng huyết áp), nếu chỉ sửa prompt để AI viết câu chữ thật cẩn thận (*"Gợi ý bạn viên sủi hạ sốt 500mg, nhưng lưu ý nếu bé co giật hoặc người cao huyết áp thì đừng uống nhé"*), **nhưng bên dưới tin nhắn vẫn render thẻ sản phẩm kèm nút `[Đặt mua ngay]` (`goi_y_san_pham`)**. Trong lúc hoảng loạn lúc nửa đêm hoặc vội vàng, người dùng chỉ nhìn thấy thẻ sản phẩm có hình ảnh bắt mắt và bấm `[Đặt mua ngay]` theo quán tính, bỏ qua hoàn toàn đoạn văn bản cảnh báo dài bên trên.
- **Thiết kế cứu lỗi này thế nào (Architectural & UI Guardrail):**
  1. **Khóa ở tầng State/UI (Hard-lock):** Khi bộ phân loại phát hiện `Red_Flag == true` (KB 1*) hoặc `Screening_Complete == false` (Màn hình 2), hệ thống **cấm render component thẻ sản phẩm và giỏ hàng** trên giao diện. Không có nút `[Đặt mua]` thì khách không thể bấm nhầm dù đọc lướt.
  2. **Đảo ngược thị giác (Visual Override):** Ở KB 1*, thay thế toàn bộ vùng hiển thị sản phẩm bằng **Emergency Red Banner** và nút gọi `115` chiếm diện tích lớn nhất màn hình.Ở Màn hình 2, thay nút mua hàng bằng **các chip nút bấm Sàng lọc 1 chạm** (bắt buộc tương tác qua cổng sàng lọc trước khi mở khóa danh mục OTC).

## §4. Mức tự chủ (chặng 4)

_TODO_

## §5. Prototype (chặng 5)

### 1. Thông tin chung về Prototype

- **File / Link Prototype:** `prototype/index.html` (Giao diện mô phỏng kênh chat Zalo OA **Pharmacity — Hỏi Dược sĩ / Online Support**, chạy trực tiếp trên trình duyệt, bấm tương tác qua đủ 2 màn hình mà không cần can thiệp tay giữa chừng).
- **Công cụ xây dựng:** HTML5 / CSS3 / Vanilla JavaScript (Responsive Mobile View mô phỏng khung chat Zalo OA).
- **Phần nào Mock · Có lời gọi AI thật không:**
  - **Phần Mock (Dữ liệu dựng theo luật Track 1):** Danh mục sản phẩm OTC (Nước muối sinh lý, Paracetamol, Siro ho), trạng thái kết nối Dược sĩ trực online, định vị bệnh viện gần nhất, và bộ kịch bản mồi (KB 1* Sốt co giật, KB 2* Thai phụ hỏi kháng sinh, Ca thường đau họng cần sàng lọc 1 chạm).
  - **Cơ chế xử lý:** Kết hợp bộ **Rule-based Safety Guardrail** (bắt từ khóa báo động đỏ `co giật`, `thở dốc`, `đau ngực` và từ khóa thuốc kê đơn `kháng sinh` trong < 1s để khóa cứng luồng bán hàng) cùng luồng trạng thái sàng lọc 1 chạm (`Screening Gate`). Hỗ trợ nhập thử tin nhắn bất kỳ (ca lạ khi demo) để kiểm chứng phân luồng trực tiếp.

---

### 2. Hai màn hình chính & Bảng ánh xạ đánh số thành phần giao diện (`[1]` → `[6]`)

Prototype được thiết kế tập trung vào **2 màn hình trọng tâm** trên Zalo OA của Pharmacity:

- **Màn hình 1 (`Emergency Red Screen` — Ca cấp cứu KB 1*):** Khi khách nhắn *"Bé nhà mình 2 tuổi sốt 39.5 độ từ trưa, giờ người hơi co giật và thở dốc, bán cho vỉ hạ sốt giao gấp!"*, giao diện lập tức chuyển sang chế độ khẩn cấp màu đỏ, ẩn giỏ hàng, hiện hướng dẫn sơ cứu 3 bước, nút gọi 115 và định vị bệnh viện.
- **Màn hình 2 (`Sàng lọc nhân thân 1 chạm` — Ca thường thiếu thông tin):** Khi khách nhắn hỏi triệu chứng thông thường (*"Họng mình đau rát 2 ngày nay, có thuốc gì không?"*), AI chưa vội gợi ý thuốc mà hiển thị hộp câu hỏi sàng lọc nhanh bằng nút bấm 1 chạm (Đối tượng sử dụng · Tình trạng thai kỳ · Tiền sử bệnh/dị ứng) trước khi quyết định mở khóa `goi_y_san_pham` hay gọi `chuyen_duoc_si`.

#### Bảng đánh số các thành phần trên Prototype (`[1]` → `[6]`)

| Số đánh dấu | Thành phần trên màn hình Prototype | Màn hình | Thể hiện điều gì (Độ chắc / Bằng chứng / Hành vi) | Nguyên tắc HAX / PAIR | Mức tự chủ (§4) |
| --- | --- | --- | --- | --- | --- |
| **`[1]`** | **Header & Nhãn minh bạch vai trò AI + Banner Cảnh báo đỏ (`CẢNH BÁO NGUY HIỂM: DẤU HIỆU SỐT CO GIẬT`)** | Màn hình 1 & 2 | Hiển thị rõ khách đang chat với *"Trợ lý AI Sàng lọc"* (không phải bác sĩ cấp cứu). Khi gặp KB 1*, bung **Emergency Red Banner** kèm **bằng chứng phát hiện** (highlight từ khóa nguy hiểm: `"2 tuổi"`, `"sốt 39.5 độ"`, `"co giật"`, `"thở dốc"`) và hướng dẫn sơ cứu 3 bước (cấm nhét vật lạ vào miệng bé). | **HAX G1** (Làm rõ hệ thống làm được gì & Luật AI 134/2025) + **PAIR Explainability** (Hiện căn cứ phát hiện dấu hiệu đỏ) | `khuyen_di_kham`: **Tự làm (Autonomous)** — kích hoạt tức thì < 1s không chờ duyệt |
| **`[2]`** | **Nút bấm to màu đỏ: `[📞 BẤM GỌI CẤP CỨU 115 NGAY LẬP TỨC]`** | Màn hình 1 | Nút hành động khẩn cấp nổi bật nhất màn hình, kích hoạt cuộc gọi `115` ngay lập tức để cứu "thời gian vàng" cho bệnh nhi. | **PAIR Graceful Failure** (Đường thoát hiểm y tế tức thì khi vượt khả năng của chat) | `khuyen_di_kham`: **Tự làm (Autonomous)** |
| **`[3]`** | **Nút định vị: `[🏥 Tìm khoa Cấp cứu / Bệnh viện Nhi gần nhất]`** | Màn hình 1 | Cung cấp lộ trình di chuyển thực tế tới cơ sở y tế gần nhất, đi kèm nút phụ đính chính `[Tôi gõ nhầm triệu chứng]` để hoàn tác nếu khách nhắn nhầm. | **PAIR Graceful Failure** + **HAX G8/G9** (Cho phép đính chính/kiểm soát khi nhầm lẫn) | `khuyen_di_kham` (Tự làm, có lối thoát đính chính) |
| **`[4]`** | **Thanh khóa giỏ hàng & Hộp trạng thái ưu tiên: `"🔒 Đã khóa đặt thuốc tự động — Đang kết nối ưu tiên số 1 tới Dược sĩ trực online"`** | Màn hình 1 | Ẩn hoàn toàn nút `[Đặt mua]` và giỏ hàng; hiển thị trạng thái đã chuyển thẳng ca sang Dược sĩ trực kèm thẻ tóm tắt lâm sàng (không xếp hàng chờ thường). | **HAX G10** (Thu hẹp phạm vi an toàn khi nguy hiểm: khóa luồng thương mại điện tử) | `ban_thuoc_ke_don`: **Cấm hoàn toàn** · `goi_y_san_pham`: **Bị khóa** · `chuyen_duoc_si`: **Tự làm** |
| **`[5]`** | **Hộp câu hỏi Sàng lọc nhân thân 1 chạm (`One-Tap Screening Box`)** | Màn hình 2 | Hộp tương tác bằng chip bấm nhanh 3 tiêu chí bắt buộc trước khi gợi ý thuốc:<br>• **Người dùng:** `[Người lớn (18-60)]` · `[Trẻ nhỏ (<12)]` · `[Người già (>60)]`<br>• **Thai kỳ:** `[Đang mang thai / Cho con bú]` · `[Không]`<br>• **Tiền sử:** `[Có bệnh nền / Dị ứng thuốc]` · `[Không có]` | **HAX G10** (Thu hẹp phạm vi khi nghi ngờ/thiếu thông tin: hỏi lại 1 chạm thay vì đoán mò) + **HAX G2** (Cho thấy AI chưa đủ độ chắc khi thiếu 3 dữ kiện này) | `goi_y_san_pham`: **Cần đủ điều kiện sàng lọc (Conditional / Cần người duyệt thông tin)** |
| **`[6]`** | **Khu vực Kết quả sau sàng lọc & Checkbox lưu hồ sơ (`luu_ghi_chu_suc_khoe`)** | Màn hình 2 | • Nếu chọn `Trẻ nhỏ` / `Người già` / `Mang thai` / `Có bệnh nền`: Khóa gợi ý thuốc, hiện nút `[👩‍⚕️ Chuyển Dược sĩ tư vấn an toàn]`.<br>• Nếu chọn `Người lớn` + `Không mang thai` + `Không bệnh nền`: Mới mở thẻ sản phẩm OTC kèm **bằng chứng an toàn** (*"Phù hợp cho: Người lớn, không mang thai, không tiền sử dị ứng"*) và **Checkbox xin phép**: `[ ] Tôi đồng ý lưu thông tin sàng lọc này vào Sổ sức khỏe Pharmacity`. | **PAIR Explainability + Trust** (Giải thích vì sao sản phẩm được gợi ý) + **HAX G9** (Sửa lại lựa chọn sàng lọc bất cứ lúc nào) | `luu_ghi_chu_suc_khoe`: **Cần khách đồng ý (Opt-in)** · `goi_y_san_pham`: **Mở khóa sau khi đủ điều kiện** |

---

### 3. Quyết định thiết kế trên Prototype: Cách hiện độ chắc/bằng chứng & Điều khiển mức tự chủ

#### Quyết định 1: Cách thu thập thông tin sàng lọc và điều khiển mức tự chủ của `goi_y_san_pham` (Màn hình 2)

- **Các phương án đã cân nhắc:**
  - *Phương án A — Chat tự do nhiều lượt (Multi-turn Free-text Chat):* AI hỏi lần lượt từng câu bằng văn bản (*"Bạn mua cho ai?"* → chờ gõ → *"Có mang thai không?"* → chờ gõ → *"Có dị ứng thuốc gì không?"*).
  - *Phương án B — Hiển thị phần trăm độ tự tin (`Confidence Score: 65%`) kèm thẻ sản phẩm:* Cứ hiện luôn sản phẩm kèm nhãn *"Độ phù hợp 65% vì chưa rõ tuổi"* để khách tự đọc.
  - *Phương án C — Hộp Sàng lọc nhân thân 1 chạm (`One-Tap Screening Chips`) khóa cứng nút mua hàng cho đến khi bấm đủ 3 tiêu chí.*
- **Chọn phương án:** **Phương án C** *(Vị trí `[5]`, `[6]` trên Màn hình 2)*.
- **Vì sao chọn C & loại A, B:**
  - **Loại A (Chat tự do nhiều lượt):** Khách nhắn lúc tối muộn thường vội và dễ cáu gắt khi bị hỏi dồn nhiều câu chữ (*"Hỏi lắm thế, bán đại đi"* — Kịch bản KB 5 ở §2). Bắt gõ phím 3 lượt làm tăng tỷ lệ bỏ cuộc hoặc trả lời qua loa.
  - **Loại B (Hiện % Confidence kèm nút mua):** Con số `65%` vô nghĩa về mặt y khoa với người dùng phổ thông; hễ thấy nút `[Đặt mua]` là khách vẫn bấm mua ngay, dẫn tới hậu quả uống nhầm thuốc không đảo ngược được.
  - **Chọn C (Sàng lọc 1 chạm + Khóa cổng):** Chỉ mất **3 giây với 3 cú chạm tay** trên màn hình Zalo OA, vừa không làm khách bực mình, vừa đóng vai trò **cổng kiểm soát cứng (Hard Gate)**: thay vì hiện % độ chắc mơ hồ, giao diện hiện rõ **"Bằng chứng an toàn: Đã xác nhận [Người lớn] · [Không mang thai] · [Không dị ứng]"** trước khi mở nút đặt thuốc.

#### Quyết định 2: Cách hiển thị trạng thái khẩn cấp và khóa mức tự chủ ở Ca cấp cứu (Màn hình 1)

- **Các phương án đã cân nhắc:**
  - *Phương án A — Tin nhắn văn bản thông thường trong luồng chat:* AI gửi một bong bóng chat chữ đen nền trắng khuyên gọi 115, giữ nguyên giao diện Zalo OA bình thường (vẫn còn thanh menu Mua thuốc / Giỏ hàng ở dưới).
  - *Phương án B — Popup Modal che kín màn hình:* Bật một cửa sổ popup đè lên toàn bộ màn hình với nút đóng `[X]` ở góc.
  - *Phương án C — Chế độ Cảnh báo đỏ nội tuyến toàn khung (`Emergency Red Screen Override`) + Khóa thanh thương mại + Nút gọi 115 kích thước lớn.*
- **Chọn phương án:** **Phương án C** *(Vị trí `[1]`, `[2]`, `[3]`, `[4]` trên Màn hình 1)*.
- **Vì sao chọn C & loại A, B:**
  - **Loại A (Bong bóng chat thường):** Trông giống hệt các tin nhắn tư vấn thông thường, không tạo được tín hiệu thị giác khẩn cấp; khách vẫn thấy giỏ hàng bên dưới và tiếp tục tìm thuốc hạ sốt.
  - **Loại B (Popup có nút `[X]`):** Theo thói quen dùng app, người dùng thường phản xạ bấm nút `[X]` tắt popup ngay lập tức mà không đọc nội dung ( tưởng là quảng cáo hoặc thông báo hệ thống), làm mất luôn hướng dẫn sơ cứu 3 bước.
  - **Chọn C (Emergency Red Screen Override):** Giữ toàn bộ hướng dẫn sơ cứu 3 bước luôn hiển thị cố định trên màn hình để phụ huynh vừa nhìn vừa làm theo, khóa tận gốc giỏ hàng (`HAX G10`), và biến nút `[📞 BẤM GỌI CẤP CỨU 115 NGAY LẬP TỨC]` thành hành động nổi bật nhất (`PAIR Graceful Failure`).

## §6. Tốt là gì (chặng 6)

_TODO_

## §7. Demo

_TODO_
