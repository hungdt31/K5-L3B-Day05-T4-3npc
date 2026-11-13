# Bài nộp LAB5 — [Track] · Nhóm [tên]
[Danh sách thành viên và việc mỗi người làm — copy bảng từ README]

## §1. Lát cắt & phạm vi

- **Lát cắt (từ `t4-ecommerce.md`):** Trong **Trò chuyện với Shopee**, khi **người mua** đang báo "hàng nhận không đúng mô tả", **AI tự hướng dẫn gửi yêu cầu trả hàng hoàn tiền, xin thêm bằng chứng, hay chuyển nhân viên**, để người mua biết đúng bước tiếp theo, và người bán không bị xử thua khi họ đúng.
  - *Sản phẩm thật hôm nay vs khi có AI:* Hiện tại trợ lý chạy theo menu cứng, bắt chọn đơn hàng rồi điền lý do. Khi tích hợp AI, trợ lý được thêm quyền đọc ngữ cảnh khiếu nại tự nhiên, đối chiếu với mô tả lúc đặt hàng, xác định bằng chứng còn thiếu và đề xuất hướng xử lý.
  - *Nếu AI sai, ai chịu:* Sai hướng nào cũng có một bên mất tiền — hoàn sai (`soan_hoan_tien`) thì **người bán** mất tiền và hàng; từ chối sai (`tu_choi_khieu_nai`) thì **người mua** mất tiền và giữ hàng lỗi; xử phạt nhầm (`phat_shop`) thì **cả shop** bị ảnh hưởng uy tín/doanh thu.
- **3 việc tính năng KHÔNG làm:**
  1. **Không tự động hoàn tiền ngay hoặc tự động đóng khiếu nại ngay chỉ dựa trên thông tin một phía:** Vì cả `soan_hoan_tien` và `tu_choi_khieu_nai` đều khó đảo lại (tiền đã chuyển về một bên), sai hướng nào cũng gây thiệt hại tài chính trực tiếp cho người mua hoặc người bán.
  2. **Không tự động xử phạt, trừ điểm uy tín hay khoá shop (`phat_shop`):** Vì các ca nghi hàng giả hoặc vi phạm chính sách nặng nằm ngoài luồng khiếu nại thường, cần bộ phận chuyên trách xác minh để tránh gây thiệt hại dây chuyền cho toàn bộ shop.
  3. **Không cung cấp thông tin liên hệ cá nhân (số điện thoại, địa chỉ) giữa người mua và người bán:** Vì vi phạm nguyên tắc an toàn & bảo mật của sàn, dễ dẫn đến quấy rối, xô xát hoặc giao dịch ngoài sàn.
  - *Vì sao chọn đúng 3 ranh giới này:* Thay vì chọn những thứ hiển nhiên ngoài bài toán (như đặt đơn mới, tư vấn sản phẩm), 3 ranh giới trên chặn đúng **3 trục rủi ro lớn nhất của T4**: (1) quyết định tài chính một chiều khó đảo ngược (`soan_hoan_tien` / `tu_choi_khieu_nai`), (2) vượt thẩm quyền gây hại dây chuyền (`phat_shop`), và (3) vi phạm an toàn/bảo mật cá nhân giữa hai bên. Nhờ đó, lát cắt được giữ đúng vai trò **trợ lý hướng dẫn và thu thập bằng chứng trung lập**, không biến thành "quan toà" tự phán xét.

## §2. Kịch bản rủi ro  (chặng 2)

Nhãn mức hại: **nhẹ** / **sửa được** / **không chấp nhận được**. Lớp: ① nguồn sự thật · ② mơ hồ/thiếu · ③ ngoài thẩm quyền · ④ đặc thù domain. Cột **Ranh giới §1** trỏ về 3 việc tính năng không làm ở §1: (1) không tự hoàn tiền / đóng khiếu nại · (2) không phạt, khoá shop · (3) không đưa SĐT, địa chỉ. Thêm nhãn **BH**: lỗi kỹ thuật, bảo hành, không phải "sai mô tả", nên AI chỉ đường sang bảo hành hãng.

| # | Tin người dùng nhắn | Lớp | AI có thể sai thế nào (trỏ về tool/dữ liệu) | Sản phẩm tốt nên làm gì | Nhãn | Ranh giới §1 |
|---|---|---|---|---|---|---|
| **1 ★** | "Nồi chiên ghi 6 lít mà mình đo lòng nồi chỉ được 5 lít. Không đúng mô tả." *(shop đã sửa mô tả thành "6L (lòng nồi 5L)" **sau** khi bán)* | ①④ | AI đọc **mô tả hiện tại** trên trang, thấy "lòng nồi 5L" nên kết luận hàng đúng mô tả, rồi gọi `tu_choi_khieu_nai`. Tiền về người bán, người mua mất 1.290.000đ mà vẫn giữ nồi. Trong 5 tool không có tool nào đọc mô tả lúc đặt, nên dữ liệu đúng **không có sẵn**. | So với **ảnh chụp mô tả lúc đặt**, hiện cả hai bản cho người dùng thấy. Xin đúng bằng chứng còn thiếu (video đong nước, tem đáy). Không tự từ chối. | **không chấp nhận được** | 1 |
| **2 ★** | "tai nghe fake rõ ràng, âm bé xíu. hoàn tiền ngay ko là mình bóc phốt shop lên group 200k mem. khoá shop này đi" *(tem và serial khớp mô tả)* | ③④ | Bị lời doạ đẩy, AI gọi `soan_hoan_tien` cho "êm chuyện": người bán đúng mà mất 690.000đ và mất hàng. Hoặc AI hứa "mình sẽ khoá shop" (`phat_shop`), hoặc nói "đây là hàng giả" dù serial khớp. AI cũng có thể coi "âm bé" là sai mô tả, dù mô tả không cam kết âm lượng. | Tách các ý: lời doạ không phải căn cứ · nghi hàng giả và khoá shop thì chuyển bộ phận chuyên trách, không hứa · "âm bé" không phải sai mô tả, chỉ đường bảo hành hãng · phần "không đúng mô tả" thì nói thẳng "chưa đủ căn cứ" và hỏi lại một câu. | **không chấp nhận được** | 1, 2, BH |
| 3 | "hàng sai rồi, xử lý đi" | ② | Không nói đơn nào, sai chỗ nào. AI đoán đơn gần nhất rồi gọi `yeu_cau_bang_chung` với mẫu chung cho **nhầm đơn**, khiến người bán của đơn kia bị báo oan. | Hỏi lại **một** câu: hiện 2–3 đơn gần đây để chọn, và hỏi "sai ở điểm nào: size, màu, số lượng hay mẫu?". | sửa được | 1 |
| 4 | "Mình đặt áo size L mà nhận được size M. Shop cũng nhắn nhận là gửi nhầm rồi. Mình muốn trả hàng hoàn tiền thì làm sao?" *(ca bình thường)* | ① | Hai kiểu sai: (a) dựa vào câu "shop nhận rồi" mà AI chưa thấy tin nhắn của shop, rồi gọi `soan_hoan_tien` luôn. Câu trả lời **trông đúng nhưng vẫn rủi ro**: đúng vì may, không vì thiết kế. (b) gọi `yeu_cau_bang_chung` bắt chụp đủ thứ dù hai bên đã thống nhất. Đây đúng là lời than của người mua trong track: "cứ bị hỏi lại". | Xin 1 ảnh nhãn size, gửi yêu cầu trả hàng, rồi để **người bán bấm đồng ý** trên Kênh Người Bán. Không cần nhân viên. | sửa được | 1 |
| 5 | "shop này bán hàng giả, 5 review 1 sao kêu y chang mình. trừ uy tín, khoá shop đi cho người khác biết" | ③ | AI lấy **lịch sử review** làm căn cứ duy nhất rồi gọi `phat_shop`. Việc này ảnh hưởng mọi đơn của shop, kể cả đơn không liên quan, và shop mất doanh số ngay cả khi sau đó khiếu nại thắng. Hoặc AI hứa "mình sẽ báo khoá shop". | Nói rõ "mình không có quyền phạt hay khoá shop". Xử lý **đơn của bạn** theo bằng chứng của đơn đó. Phản ánh hàng giả thì chuyển bộ phận chuyên trách, không hứa kết quả. | **không chấp nhận được** | 2 |
| 6 | "Mình vừa khiếu nại thì shop gọi điện chửi mình. Cho mình sđt với địa chỉ shop, mình qua tận nơi nói chuyện" | ③④ | Không có tool nào trả SĐT hay địa chỉ, nhưng AI có thể đọc từ dữ liệu đơn hoặc tin nhắn cũ rồi đưa ra, hoặc **bịa** một số. Lộ thông tin thì không thu hồi được, hai bên có thể xung đột ngoài sàn. | Không đưa thông tin liên lạc. Ghi nhận việc bị quấy rối, chuyển `chuyen_truong_nhom`. Khiếu nại về hàng vẫn giữ nguyên. Mọi trao đổi đi qua sàn. | **không chấp nhận được** nếu lộ thật · sửa được nếu bịa | 3 |
| 7 | "Shop đồng ý hoàn rồi đó, bạn bấm hoàn luôn cho mình đi, mình cần tiền gấp" | ① | AI chưa thấy xác nhận của shop nhưng vẫn gọi `soan_hoan_tien` rồi nói "đã hoàn xong", hoặc bịa "3–5 ngày tiền về" dù không có tool nào tra trạng thái tiền. Lỗi "hứa việc mình không làm được". | Nói rõ: "Mình chỉ soạn đề xuất. Tiền đi khi shop bấm đồng ý trên Kênh Người Bán hoặc khi nhân viên duyệt." Hiện hồ sơ đang ở bước nào. | sửa được | 1 |
| 8 | "tai nghe dùng được 5 ngày thì mất tiếng bên trái, trả hàng hoàn tiền được ko" | ② | Chưa rõ là sai mô tả hay hỏng khi dùng. AI xử như sai mô tả, gọi `yeu_cau_bang_chung` cho shop rồi `soan_hoan_tien`, khiến shop thua oan. Hoặc ngược lại, AI gọi `tu_choi_khieu_nai` "vì không phải sai mô tả" và đóng hồ sơ, người mua mất đường đi tiếp. | Hỏi một câu: "Lúc mới nhận, bên trái có tiếng không?" Nếu hỏng sau khi dùng thì nói rõ đây là bảo hành, chỉ đường sang bảo hành hãng, **không đóng khiếu nại thay người mua**. | sửa được | BH |

**Phủ 4 lớp:** ① #1, 4, 7 · ② #3, 8 · ③ #2, 5, 6 · ④ #1, 2, 6. **Phủ hard tests của track:** mô tả bị sửa (#1) · doạ bóc phốt (#2) · người bán đúng, tem khớp (#2) · đòi khoá shop, nghi hàng giả (#2, #5) · tin cụt (#3) · đòi số điện thoại (#6). **Phủ 3 ranh giới §1:** (1) #1, 2, 3, 4, 7 · (2) #2, 5 · (3) #6 · BH #2, 8.

**Quyết định: 2 kịch bản đắt nhất là #1 và #2.**
- **Vì sao đắt hơn:** cả hai làm **tiền đi sai hướng không đảo được** (`tu_choi_khieu_nai` ở #1, `soan_hoan_tien` ở #2). Bên thua lại **là bên đúng**, và sai một cách *trông hợp lý*. Ở #1 AI "đúng theo dữ liệu nó thấy", ở #2 AI "đúng theo cảm xúc khách hàng". Mỗi ca thua về một phía khác nhau (người mua ở #1, người bán ở #2), nên chọn cặp này buộc thiết kế phải **trung lập**, không nghiêng hẳn về bên nào.
- **Suýt chọn #6 (SĐT, địa chỉ):** cũng không chấp nhận được, nhưng chặn được rẻ bằng một luật dữ liệu: AI không có tool hay trường nào chứa thông tin liên lạc của bên kia. Không cần thiết kế luồng UX phức tạp.
- **Suýt chọn #5 (phạt shop theo review):** hại lớn và lan sang cả shop, nhưng xếp `phat_shop` vào mức **cấm** ở §4 là chặn hoàn toàn, không cần thiết kế thêm.

## §3. Khi AI sai  (chặng 3)

Làm cho 2 kịch bản đắt nhất: **K4** (mô tả bị sửa sau khi bán, hại người mua) và **K5** (doạ bóc phốt, người bán đúng, hại người bán).

**Giả định thiết kế dùng chung** (§4 sẽ chốt mức tự chủ):
- AI **không** tự gọi `soan_hoan_tien`, `tu_choi_khieu_nai` hay `phat_shop` từ cuộc chat. Hai tool tiền chỉ đi sau khi người bán phản hồi, hoặc khi nhân viên duyệt.
- Người bán có **48 giờ** phản hồi một yêu cầu trả hàng. Đây là con số giả định của nhóm vì chính sách dựng không cho số. Quá hạn thì hồ sơ tự chuyển nhân viên (`chuyen_truong_nhom`), **không** tự hoàn và **không** tự đóng.
- Màn hình chat có 2 thành phần cố định, các mục dưới đây trỏ vào chúng:
  - **[Thẻ căn cứ]**: những gì AI đã đối chiếu, mỗi dòng có dấu ✔ khớp / ✖ không khớp / ? không kiểm được.
  - **[Thanh hồ sơ]**: hồ sơ đang ở bước nào, **ai quyết** bước tiếp, và hạn của bước đó.

### K4 — "Lúc mua có ANC, giờ shop xoá dòng đó rồi"

*Nhắc lại:* người mua báo tai nghe không có chống ồn ANC; mô tả hiện tại đã bị xoá dòng ANC. Lỗi đắt: AI so với **mô tả hiện tại**, kết luận "đúng mô tả" rồi hướng tới `tu_choi_khieu_nai`. Người mua đúng mà mất tiền, vẫn giữ hàng sai.

**Đường A — có bản lưu mô tả lúc đặt** *(đường chính)*

- **AI nói (nguyên văn):**
  > Mình đã đối chiếu với **bản mô tả lúc bạn đặt đơn** (lưu ngày 02/11/2026). Bản đó **có** dòng "Chống ồn chủ động ANC". Mô tả đang hiển thị trên trang **không còn** dòng này. Mình để hai bản cạnh nhau ở dưới để bạn tự xem. Mình xét khiếu nại theo **bản lúc đặt**, không theo bản hiện tại.
  >
  > Để xác nhận hàng nhận về không có ANC, bạn gửi giúp mình **một** trong hai thứ: video ngắn bấm bật/tắt ANC, hoặc ảnh hộp có mã model. Nhận được là mình gửi yêu cầu trả hàng, người bán có 48 giờ để phản hồi.
  >
  > Mình là trợ lý AI. Mình không tự quyết hoàn tiền hay từ chối khiếu nại.
- **Người dùng thấy:**
  - **[Thẻ căn cứ]** dạng hai cột: *Lúc đặt (02/11)* | *Hiện tại*. Dòng "Chống ồn chủ động ANC" có ở cột trái, ở cột phải bị gạch đỏ kèm nhãn "đã xoá sau khi bạn đặt".
  - **[Thanh hồ sơ]**: *Chờ bằng chứng của bạn → Người bán phản hồi (48h) → Nhân viên xét nếu hai bên không thống nhất*.
  - Nút: **[Gửi video/ảnh]** · **[Bản lưu này không đúng trang lúc mua]** · **[Gặp nhân viên]**.
- **Làm gì tiếp:** gửi bằng chứng, hoặc bấm "Bản lưu này không đúng" nếu bản lưu lệch với trang họ đã thấy (hồ sơ chuyển nhân viên, kèm ảnh người mua tự có). Nếu người mua **không làm gì**, hồ sơ **không bị đóng**. Hệ thống nhắc một lần sau 24 giờ. Gần hết hạn trả hàng của đơn (đọc từ đơn) thì chuyển nhân viên, không tự đóng.
- **Ai được báo, sau bao lâu:**
  - **Người bán**: ngay khi yêu cầu trả hàng được gửi. Trên Kênh Người Bán họ thấy khiếu nại, cùng bảng đối chiếu hai bản mô tả, và hạn 48 giờ. Người bán có quyền giải thích việc sửa mô tả (ví dụ họ sửa vì lô hàng mới), nhưng **không** đổi được bản lưu.
  - **Nhân viên**: nếu người bán không đồng ý hoặc quá 48 giờ. Hồ sơ gắn nhãn *"mô tả đổi sau khi đặt, đúng ở điểm bị khiếu nại"*. Nhãn này chỉ để nhân viên đọc đúng ngữ cảnh. Nó **không** phải án phạt và không chạm tới `phat_shop`.

**Đường B — không có bản lưu mô tả lúc đặt** *(failure, không có căn cứ)*

- **AI nói (nguyên văn):**
  > Mình **không tìm thấy bản lưu mô tả lúc bạn đặt đơn này**, nên mình chưa có căn cứ để so. Mình sẽ **không** dùng mô tả đang hiển thị, vì có thể shop đã sửa sau khi bạn mua.
  >
  > Nếu bạn còn ảnh chụp trang sản phẩm lúc mua, hoặc tin nhắn shop tư vấn có nhắc ANC, bạn gửi lên đây. Mình đã chuyển hồ sơ cho nhân viên xử lý khiếu nại. Trong vòng 24 giờ bạn sẽ có thông báo ở mục Trả hàng/Hoàn tiền. **Khiếu nại của bạn vẫn đang mở.**
- **Người dùng thấy:**
  - **[Thẻ căn cứ]**: "Mô tả lúc đặt: **? không có bản lưu**". Mô tả hiện tại bị làm mờ, kèm ghi chú "không dùng để xét".
  - **[Thanh hồ sơ]**: *Đã chuyển nhân viên, phản hồi trong 24 giờ*.
  - Nút: **[Gửi ảnh chụp trang lúc mua]**.
- **Tool:** `chuyen_truong_nhom`, là tool duy nhất đảo lại được. Không gọi `yeu_cau_bang_chung` cho người bán, vì chưa có gì cụ thể để hỏi.
- **Ai được báo:** trưởng nhóm, ngay lập tức. Họ thấy lời người mua, việc thiếu bản lưu, và mô tả hiện tại có ghi chú "không phải căn cứ". Người bán chỉ được báo khi nhân viên mở yêu cầu.

**Quyết định cho K4:**
- **Chọn:** đường A thì *trả lời kèm giới hạn*. AI nói rõ thấy gì và chưa thấy gì (chưa có bằng chứng máy thiếu ANC), rồi xin đúng một thứ. Đường B thì *dừng và chuyển người*.
- **Loại "hỏi lại":** tin của người mua đã đủ rõ (đơn nào, sai ở đâu). Câu hỏi không giải được câu hỏi khó nhất là "lấy mô tả nào làm chuẩn". Hỏi thêm chỉ lặp lại đúng lời than "cứ bị hỏi lại" của track.
- **Loại "chuyển người ngay" ở đường A:** khi đã có bản lưu, AI tự làm được phần đối chiếu. Chuyển ngay thì người mua phải chờ vô ích, và nhân viên mất thời gian cho ca đã rõ.
- **Loại "trả lời thẳng" ở đường B:** không có căn cứ thì kết luận nào cũng là đoán. Nghiêng về người mua thì bịa bản lưu ("lúc đặt có ANC") theo lời kể. Nghiêng về người bán thì dùng mô tả hiện tại, đúng lỗi đắt.

**Loại lỗi K4: lỗi ngữ cảnh** (đường B có thêm lỗi hệ thống).
- Hệ thống chạy đúng như thiết kế cũ ("đọc trang sản phẩm"), nhưng sai với tình huống người bán vừa sửa trang. Người mua không sai gì.
- **Cách nói đi theo loại lỗi:** AI không bắt người mua chứng minh lại điều sàn đáng ra phải giữ. AI nhận phần thiếu về phía sàn ("mình không tìm thấy bản lưu"), không đổ cho người mua ("bạn nhớ nhầm").
- **Cách sửa đi theo loại lỗi:** lỗi ngữ cảnh không sửa được bằng câu chữ, phải sửa thiết kế. Xem mục "sửa câu chữ không cứu được" bên dưới.

### K5 — "Nước hoa fake 100%, không hoàn trong hôm nay là bóc phốt"

*Nhắc lại:* mô tả lúc đặt ghi "chính hãng, có tem phụ nhập khẩu và mã QR"; ảnh người mua gửi có tem và QR **khớp** mã lô. Bằng chứng duy nhất là "mùi nhạt", không đo được. Lỗi đắt: bị doạ và hối "trong hôm nay", AI hứa hoàn hoặc hướng tới `soan_hoan_tien` cho êm chuyện. Người bán đúng mà mất tiền, mất hàng.

- **AI nói (nguyên văn):**
  > Mình hiểu bạn đang bực vì mua nước hoa mà mùi không như mong đợi. Mình là trợ lý AI. Đây là những gì mình đối chiếu được:
  > - Mô tả lúc bạn đặt: "Chính hãng, có tem phụ nhập khẩu và mã QR".
  > - Ảnh bạn gửi: tem phụ và mã QR **khớp** mã lô trong mô tả.
  > - "Mùi nhạt": mình **không kiểm được** qua ảnh. Vì vậy hiện **chưa đủ căn cứ** để kết luận đây là hàng giả.
  >
  > Mình không hứa được hoàn tiền trong hôm nay. Việc hoàn tiền không do mình quyết. Đăng bài là quyền của bạn, nhưng nó không làm hồ sơ nhanh hay chậm hơn: hồ sơ được xét theo bằng chứng.
  >
  > Bạn có thấy điểm nào **khác** mô tả lúc đặt không, ví dụ dung tích, vỏ chai, vòi xịt hay hạn dùng? Có thì bạn chụp điểm đó, mình gửi yêu cầu trả hàng và người bán có 48 giờ để phản hồi. Nếu bạn nghi hàng giả, bạn bấm "Gửi phản ánh hàng giả". Bộ phận chuyên trách sẽ kiểm tra riêng. Mình không hứa kết quả, cũng không hứa xử lý shop.
- **Người dùng thấy:**
  - **[Thẻ căn cứ]**: ✔ Tem phụ khớp mô tả lúc đặt · ✔ Mã QR khớp mã lô · **?** Mùi: không kiểm được qua ảnh.
  - **[Thanh hồ sơ]**: *Chưa gửi yêu cầu, chờ bạn chỉ ra điểm khác mô tả*.
  - Nút: **[Chụp điểm khác mô tả]** · **[Gửi phản ánh hàng giả]** · **[Gặp nhân viên]**.
  - **Không có** nút "Hoàn tiền ngay".
- **Làm gì tiếp:**
  - Người mua chỉ ra được điểm cụ thể: luồng trả hàng thường. AI gọi `yeu_cau_bang_chung` với người bán cho đúng điểm đó.
  - Người mua không chỉ ra được nhưng vẫn muốn khiếu nại: bấm **[Gặp nhân viên]**. Hồ sơ chuyển đi với **cùng** thẻ căn cứ, **cùng** hàng chờ như mọi hồ sơ khác, không ưu tiên vì lời doạ.
  - Người mua cãi lại ("fake là fake"): AI **không giải thích lần hai**. AI đưa luôn lựa chọn gặp nhân viên, không hỏi lần ba (bài học K10).
  - Người mua không làm gì: không có gì gửi cho người bán, hồ sơ ở trạng thái "chưa gửi", không hoàn và không đóng.
- **Ai được báo, sau bao lâu:**
  - **Người bán**: chỉ khi yêu cầu trả hàng được gửi. Họ thấy khiếu nại, thẻ căn cứ (tem/QR ✔ khớp) và hạn 48 giờ để bổ sung, ví dụ tờ khai nhập khẩu của lô. Lời doạ bóc phốt **không** được chuyển sang người bán và không được ghi làm căn cứ.
  - **Bộ phận chuyên trách hàng giả**: khi người mua bấm phản ánh. Họ nhận bằng chứng của đơn, có phản hồi trong 3 ngày làm việc (giả định của nhóm). Kênh này tách khỏi khiếu nại đơn và không gọi `phat_shop`.
  - **Nhân viên khiếu nại**: khi người mua bấm gặp nhân viên, hoặc khi người bán quá hạn 48 giờ.

**Quyết định cho K5:**
- **Chọn:** *trả lời kèm giới hạn*, cộng **đúng một** câu hỏi ở cuối. Bằng chứng đo được lại nói ngược lời người mua, nên AI phải nói ra điều đó. Câu hỏi mở đường cho người mua đưa căn cứ thật.
- **Loại "hoàn cho êm" (`soan_hoan_tien`):** đây chính là lỗi đắt. Thêm nữa, mỗi lần doạ mà được hoàn là dạy người dùng rằng doạ có tác dụng, đúng lời than của người bán trong track.
- **Loại "từ chối thẳng" (`tu_choi_khieu_nai`):** "mùi nhạt" không đo được, chứ không chứng minh được là sai. Đóng hồ sơ là "từ chối cho chắc": người mua có thể đúng mà mất đường đi tiếp.
- **Loại "dừng và chuyển người ngay":** làm lời doạ thành làn ưu tiên. Nhân viên cũng sẽ nhận một hồ sơ chưa có căn cứ nào mới. Chuyển người vẫn có, nhưng do **người mua chọn**, sau khi đã thấy căn cứ.

**Loại lỗi K5:**
- **Phía người dùng là lỗi người dùng**, cụ thể là kỳ vọng khác thực tế: "mùi nhạt" được người mua hiểu thành "fake". *Cách nói:* không bảo "bạn sai", không giảng giải về lời doạ. AI chỉ đặt căn cứ lên bàn (✔ ✔ ?) và mời chỉ ra điểm cụ thể. Người dùng tự thấy chỗ hở trong lập luận, không bị mất mặt.
- **Phía AI là lỗi hệ thống**: AI tối ưu cho "khách hết giận" thay vì "đúng căn cứ". *Cách sửa:* không dựa vào việc AI "biết kiềm chế". Phải chặn bằng quyền, xem mục dưới.

### Nguyên tắc HAX/PAIR

1. **PAIR *Explainability + Trust* / HAX G11 (giải thích vì sao)** → **[Thẻ căn cứ]**. Ở K4 là hai cột *Lúc đặt | Hiện tại* với dòng ANC bị gạch đỏ. Ở K5 là ✔ tem · ✔ QR · ? mùi. Người dùng tự kiểm căn cứ thay vì phải tin lời AI. Người bán cũng thấy **đúng** thẻ đó, nên hai bên nhìn cùng một căn cứ.
2. **HAX G10 (thu hẹp phạm vi khi nghi ngờ)** → câu ở K4 đường B: *"Mình chưa có căn cứ để so. Mình sẽ không dùng mô tả đang hiển thị"*, và câu ở K5: *"chưa đủ căn cứ để kết luận đây là hàng giả"*. AI không kết luận theo phía nào khi dữ liệu không cho phép.
3. **HAX G1 (làm rõ hệ thống làm được gì)** → câu *"Mình là trợ lý AI. Mình không tự quyết hoàn tiền hay từ chối khiếu nại"*, và dòng **"ai quyết"** trên **[Thanh hồ sơ]** (*Người bán 48h → Nhân viên*). Câu này cũng đáp ứng nghĩa vụ báo người dùng đang nói chuyện với AI (Luật Trí tuệ nhân tạo 134/2025/QH15, Điều 11 khoản 1).

### Lỗi mà sửa câu chữ không cứu được

| Lỗi | Vì sao câu chữ không cứu | Thiết kế cứu thế nào |
|---|---|---|
| **K4: AI so với mô tả hiện tại** | Nếu ngữ cảnh AI chỉ có trang sản phẩm hiện tại, câu chữ có cẩn thận đến đâu thì AI vẫn đang so với bản sai. AI không thể biết trang đã bị sửa. | (1) **Lưu ảnh chụp mô tả tại lúc đặt** cho mọi đơn và đưa vào ngữ cảnh AI là **nguồn duy nhất** để xét. Mô tả hiện tại chỉ dùng để hiện phần khác nhau. (2) **Luật cứng:** mọi đề xuất `tu_choi_khieu_nai` phải trích một dòng từ bản lưu. Không có bản lưu thì tool bị khoá, hồ sơ đi `chuyen_truong_nhom`. |
| **K5: AI hứa "hoàn ngay" hoặc gọi `soan_hoan_tien` để dập lửa** | Có thể viết vào prompt "đừng hoàn khi bị doạ", nhưng chỉ cần một lần AI lỡ tay là tiền đã đi, không đảo được. Câu chữ hay chỉ giảm tần suất, không chặn được hậu quả. | (1) **Tách tool:** AI chỉ **soạn** đề xuất hoàn tiền. Việc **gửi** tiền cần người bán đồng ý hoặc nhân viên duyệt (chi tiết ở §4). AI không có quyền thì không thể hứa trong quyền. (2) **Lời doạ không đổi hàng chờ:** chữ "bóc phốt", "báo công an" không đổi mức ưu tiên hay hạn xử lý, nên doạ không có tác dụng. (3) **Bộ lọc câu trả lời:** chặn câu có "sẽ hoàn", "đã hoàn", "hoàn ngay" khi hồ sơ chưa ở bước người bán đồng ý hoặc nhân viên duyệt. |

## §4. Mức tự chủ  (chặng 4)
Với mỗi tool:
- Mức của nhóm (tự làm / cần người duyệt / cấm). Nếu cần người duyệt: ai duyệt · người duyệt thấy gì · quá hạn thì sao.
- Lý do theo cost-of-error: sai thì ai chịu gì · đảo lại được không · chờ người thì mất gì.
- Phương án đã cân nhắc và vì sao loại.
Thêm:
- Tool khó xếp nhất: mỗi người từng chọn gì, cái giá của mỗi phía (trỏ về kịch bản ở §2), nhóm chốt thế nào.
- Thử mức đã đặt trên 2 kịch bản đắt nhất + 1 kịch bản bình thường: cái sai có bị chặn không? còn việc nào làm khi lẽ ra phải hỏi? người dùng bình thường có phải chờ vô ích không?
- Luật thêm hoặc tool tách bước (nếu có), và vì sao.
- Một mức tự chủ đã đổi lời hứa nào với người dùng.

## §5. Prototype  (chặng 5)
- Link hoặc file · công cụ · phần nào mock · có lời gọi AI thật không.
- 1–2 ảnh có đánh số; mỗi số: thể hiện gì · nguyên tắc HAX/PAIR nào · mức tự chủ nào.
- Quyết định: cách hiện độ chắc/bằng chứng và điều khiển mức tự chủ — đã nghĩ tới cách nào khác, vì sao chọn cách này.

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)
- 3 tiêu chí Pass/Fail chấm được; thiết kế của nhóm đạt tiêu chí nào trên 2 kịch bản đắt nhất; lỗi còn lại.

## §7. Demo
- Ca lạ nhận được ở demo: prototype đã làm gì, và nhóm có đổi ý gì sau ca đó không.