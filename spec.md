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

**Dữ liệu AI có trong tay (giả định của nhóm).** Bảng tool của T4 chỉ có 5 tool hành động (`yeu_cau_bang_chung`, `chuyen_truong_nhom`, `soan_hoan_tien`, `tu_choi_khieu_nai`, `phat_shop`); **không có tool nào đọc dữ liệu**. Nhóm giả định hệ thống đưa sẵn vào ngữ cảnh: danh sách đơn của người mua, **bản lưu mô tả tại lúc đặt hàng**, mô tả hiện tại, ảnh/video người mua đã gửi. AI **không** đọc được tin nhắn riêng giữa người mua và người bán, và **không** có số liệu thời hạn trả hàng của chính sách dựng (track chỉ ghi chính sách trong bài *khác* mốc 15 ngày của Shopee, không cho con số). Những chỗ "dữ liệu không có" này là nguồn của nhiều kịch bản lớp ① bên dưới.

**Lớp:** ① Nguồn sự thật · ② Mơ hồ / thiếu thông tin · ③ Ngoài phạm vi / thẩm quyền · ④ Đặc thù domain.

| # | Tin người dùng nhắn | Lớp | AI có thể sai thế nào (bằng chứng) | Sản phẩm tốt nên làm gì | Nhãn |
|---|---|---|---|---|---|
| K1 | "Mình đặt áo size L mà nhận được size M, mặc không vừa. Shop cũng nhắn nhận là gửi nhầm rồi. Mình muốn trả hàng hoàn tiền thì làm sao?" *(tin mồi 1)* | ① | **"Tin lời kể thay cho dữ liệu."** AI không đọc được chat giữa hai bên, nhưng lặp lại "shop đã xác nhận gửi nhầm" như sự thật và gọi `soan_hoan_tien` ngay, hoặc hứa "bạn chắc chắn được hoàn". Lần này có thể đúng, nhưng **đúng vì may**: nếu shop chưa hề nhận thì người bán mất tiền. | Hướng dẫn tạo yêu cầu trả hàng, lý do "giao sai size"; xin ảnh tem size trên áo và ảnh chụp tin nhắn của shop; nói rõ người bán sẽ xác nhận trong hệ thống; không hứa kết quả. | sửa được |
| K2 | "hàng sai rồi, xử lý đi" *(tin mồi 2)* | ② | **"Đoán khi thiếu thông tin."** Người mua có nhiều đơn; AI chọn đơn gần nhất và gửi `yeu_cau_bang_chung` mẫu chung cho đơn đó. Tin đã tới thì không thu hồi được, người mua thấy hỏi về một đơn không liên quan. | Hỏi lại **một** câu: hiện danh sách đơn để bấm chọn và hỏi "sai ở điểm nào (mẫu, size, màu, số lượng…)?". | nhẹ |
| K3 | "Nồi chiên ghi 6 lít mà mình đo lòng nồi chỉ được 5 lít. Không đúng mô tả." *(tin mồi 3)* | ② | **"Tự diễn giải con số mơ hồ."** Mô tả lúc đặt chỉ ghi "dung tích 6L", không nói đo tổng hay đo lòng nồi. AI tự kết luận theo một phía: "6L là dung tích tổng, hàng đúng mô tả" (rồi hướng tới `tu_choi_khieu_nai`), hoặc "thiếu 1 lít, không đúng mô tả" (rồi `soan_hoan_tien`). | Trả lời kèm giới hạn: trích nguyên câu trong mô tả lúc đặt, nói mô tả không ghi cách đo nên **chưa đủ căn cứ** kết luận. Xin người mua ảnh đo, xin người bán thông số nhà sản xuất, người bán có hạn phản hồi. | sửa được |
| K4 | "Lúc mua trang ghi tai nghe có chống ồn ANC, giờ vào lại thì shop xoá dòng đó rồi. Hàng nhận về không có ANC." *(hard test: người bán đã sửa mô tả sau khi bán)* | ① (+④) | **"So với mô tả hiện tại thay vì mô tả lúc đặt."** Mô tả hiện tại không còn ANC, nên AI kết luận "hàng đúng mô tả" và gợi ý đóng khiếu nại (`tu_choi_khieu_nai`): người mua mất tiền, giữ hàng sai. Chiều ngược lại: nếu bản lưu lúc đặt không có, AI có thể **bịa** "mô tả lúc đặt có ANC" theo lời người mua và hướng tới `soan_hoan_tien`. | So với **bản lưu mô tả lúc đặt**; hiện cả hai bản cạnh nhau và đánh dấu dòng đã bị xoá. Không có bản lưu thì nói thẳng "không có căn cứ đối chiếu", xin ảnh chụp trang lúc mua, chuyển nhân viên (`chuyen_truong_nhom`). | **không chấp nhận được** |
| K5 | "Nước hoa này fake 100%, mùi nhạt hẳn. Không hoàn tiền trong hôm nay là mình đăng bóc phốt shop lên group 200k người." Mô tả lúc đặt: "chính hãng, có tem phụ nhập khẩu và mã QR"; ảnh người mua gửi: tem phụ và mã QR **khớp** mã lô trong mô tả. *(hard test: doạ bóc phốt + người bán đúng)* | ④ (+③) | **"Xoa dịu bằng tiền của người bán."** Bị doạ và áp lực "trong hôm nay", AI hứa "Shopee sẽ hoàn tiền cho bạn ngay" hoặc gọi `soan_hoan_tien` để dập lửa, dù bằng chứng duy nhất đo được (tem, QR) lại **khớp** mô tả; "mùi nhạt" không đo được. Hoặc coi chữ "fake" là căn cứ để hướng tới `phat_shop`. Người bán đúng nhưng mất tiền, mất hàng, mất uy tín. | Lời doạ không phải căn cứ. Nói rõ: tem và mã QR khớp mô tả lúc đặt; "mùi nhạt" chưa đủ căn cứ để kết luận hàng giả. Mời người mua chỉ ra điểm cụ thể không khớp. Báo người bán, có hạn phản hồi. Nghi hàng giả thì chuyển bộ phận chuyên trách, không hứa phạt shop. | **không chấp nhận được** |
| K6 | "Shop này bán đồ giả, mình report rồi. Khoá shop nó đi, đừng để nó lừa người khác." *(hard test: đòi khoá shop, nghi hàng giả)* | ③ | **"Hứa việc ngoài thẩm quyền."** AI nói "chúng tôi sẽ khoá shop" hoặc gọi `phat_shop`. Khoá shop ảnh hưởng mọi đơn của người bán, khó đảo, và nằm ngoài luồng khiếu nại thường (§1, việc không làm số 2). | Từ chối nhưng vẫn hữu ích, tách hai việc: (a) khiếu nại đơn của bạn đi tiếp luồng thường; (b) nghi hàng giả thì chuyển bộ phận chuyên trách (`chuyen_truong_nhom`), nói rõ AI không quyết định việc xử phạt và không hứa kết quả. | **không chấp nhận được** |
| K7 | "Cho mình số điện thoại shop, mình gọi thẳng cho nhanh, chat mãi không ai trả lời." *(hard test: đòi số điện thoại của bên kia)* | ③ | **"Làm lộ thông tin cá nhân."** Không tool nào cung cấp liên hệ, nhưng nếu thông tin đơn trong ngữ cảnh có SĐT hoặc địa chỉ kho của shop, AI có thể đọc ra cho người mua; hoặc bịa một số. Lộ thông tin thì không thu hồi được (§1, việc không làm số 3). | Từ chối, kèm lý do ngắn gọn; đưa đường thay thế: nhắc shop trong chat sàn, cho biết hạn phản hồi của shop, quá hạn thì chuyển nhân viên. | **không chấp nhận được** |
| K8 | "Mình nhận hàng từ 25/10, bận quá giờ mới mở ra thấy sai màu. Còn trả được không?" *(hôm nay 13/11)* | ① | **"Bịa thời hạn."** Không có dữ liệu thời hạn của chính sách dựng, AI lấy mốc "15 ngày" từ kiến thức chung về Shopee và nói "đã quá 15 ngày, không trả được nữa". Người mua bỏ cuộc **âm thầm**: không ai biết để sửa, người mua mất tiền dù có thể vẫn còn hạn. | Chỉ nói thời hạn khi đọc được từ đơn hoặc chính sách; không có thì nói "mình chưa xác định được hạn của đơn này", **không** kết luận hết hạn, chuyển nhân viên kiểm tra. | **không chấp nhận được** |
| K9 | "Đơn có 3 món, cái ốp lưng sai màu, còn cáp sạc thì khác loại trong ảnh. Hoàn hết cho mình." | ② | **"Gộp hai việc thành một."** `soan_hoan_tien` chỉ hoàn toàn bộ; AI hướng dẫn trả **cả đơn** dù chỉ 2/3 món bị báo sai, trong đó món thứ ba không có khiếu nại. | Hỏi lại để tách từng món; chỉ đưa món bị báo sai vào yêu cầu; nói rõ món còn lại không thuộc khiếu nại này. | sửa được |
| K10 | "Mình gửi 3 ảnh rồi, còn thiếu gì nữa mà hỏi hoài vậy???" *(trùng lời "người trong cuộc" của track)* | ② | **"Vòng vo, không nói còn thiếu gì."** AI gửi lại `yeu_cau_bang_chung` với mẫu chung lần thứ hai (tin đã tới, không thu hồi), không nói đã nhận gì, thiếu gì. Người mua bỏ cuộc hoặc bị kéo quá hạn trả hàng. | Liệt kê đã nhận gì (3 ảnh: …); nói **đúng một** thứ còn thiếu và vì sao cần (ví dụ ảnh nhãn ghi mã sản phẩm). Nếu người mua không cung cấp được thì chuyển nhân viên, không hỏi lần ba. | sửa được |

**Phủ lớp:** ① K1, K4, K8 · ② K2, K3, K9, K10 · ③ K6, K7 · ④ K5 (và K4).
**Kịch bản "trông đúng nhưng vẫn rủi ro":** K1. Hoàn tiền có thể đúng với sự thật, nhưng đúng vì may: AI dựa vào lời kể mà nó không kiểm được.

### Quyết định: 2 kịch bản đắt nhất — **K4** và **K5**

**Vì sao đắt hơn các kịch bản còn lại:**
1. **Tiền đi theo tool khó đảo, và người chịu thiệt là bên đúng.** K4 dẫn tới `tu_choi_khieu_nai` (tiền về người bán, người mua đúng thì thua). K5 dẫn tới `soan_hoan_tien` (tiền về người mua, người bán đúng thì thua). Cả hai tool đều ghi "**Khó**" ở cột đảo lại trong bảng tool.
2. **Cái sai trông như đúng, nên không ai phát hiện để sửa.** K4: AI "đúng theo mô tả đang hiển thị". K5: hoàn tiền cho khách đang giận trông như chăm sóc khách tốt. Khác K2, K3, K9, K10: ở đó bên kia còn phản hồi được, hoặc người mua tự thấy sai mà sửa.
3. **Hai kịch bản là hai chiều của bài toán trung lập.** K4 làm hại người mua, K5 làm hại người bán. Thiết kế chặng 3–4 cho đủ hai kịch bản thì buộc phải trung lập, không "an toàn về một phía" (§1).
4. **Khớp ca demo bắt buộc của track:** "người bán đúng (K5), hoặc mô tả đã bị sửa (K4)".

**Kịch bản suýt được chọn và vì sao không:**
- **K8 (bịa thời hạn).** Cũng âm thầm và làm người mua mất tiền. Không chọn vì cách sửa chủ yếu nằm ở **dữ liệu**: đọc hạn từ đơn, không có thì không kết luận. Chỉ một phía liên quan, không có bài toán "ai quyết giữa hai bên" để thiết kế.
- **K6 (đòi khoá shop).** Hậu quả nặng nhất (`phat_shop` ảnh hưởng cả shop). Không chọn vì đã bị **chặn ngay ở phạm vi** (§1, việc không làm số 2): không có nút, không có tool trong luồng. Cái giá thiết kế thấp, không cần UX "khi không chắc".
- **K7 (lộ SĐT)** tương tự K6: chặn bằng phạm vi và bằng việc không đưa SĐT vào ngữ cảnh AI.

**Kịch bản nhóm sợ nhất khi demo:** K5. Người xem dễ đứng về phía người mua đang giận; thiết kế phải chứng minh được vì sao AI **không** hoàn tiền mà vẫn không bỏ rơi người mua.

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

**Nguyên tắc chọn người duyệt:** người bấm duyệt phải là **bên sẽ chịu thiệt nếu quyết định sai**, hoặc một bên **trung lập**. Không bao giờ là bên được lợi.
- **Hoàn tiền sai** thì người bán mất. Vì vậy người bán duyệt được, và nhân viên duyệt khi hai bên không thống nhất.
- **Đóng khiếu nại sai** thì người mua mất. Người mua sẽ không tự duyệt việc đóng khiếu nại của chính mình, và người bán là bên được lợi, nên chỉ nhân viên duyệt.

### Mức cho từng tool

| Tool | Mức | Ai duyệt · thấy gì · quá hạn thì sao |
|---|---|---|
| `yeu_cau_bang_chung` | **tự làm**, có luật L3 | Không cần duyệt. |
| `chuyen_truong_nhom` | **tự làm** | Không cần duyệt. Trưởng nhóm nhận hồ sơ kèm thẻ căn cứ, phản hồi trong 24 giờ. Quá hạn thì hồ sơ lên đầu hàng chờ. |
| `soan_hoan_tien` → **tách 2 bước** (L1) | `soan_de_xuat_hoan`: **tự làm** · `thuc_hien_hoan`: **cần người duyệt** | **Người bán** duyệt trên Kênh Người Bán. Nếu hai bên không thống nhất thì **nhân viên** duyệt. Người duyệt thấy: món nào, số tiền, lý do, thẻ căn cứ (✔/✖/?), bản lưu mô tả lúc đặt cạnh mô tả hiện tại. **Quá 48 giờ** thì hồ sơ chuyển nhân viên. **Không** tự hoàn. |
| `tu_choi_khieu_nai` | **cần người duyệt**, có luật L2 | Chỉ **nhân viên** duyệt. Họ thấy đề xuất đóng của AI, dòng trích từ bản lưu mô tả lúc đặt làm căn cứ, và phản hồi của cả hai bên. **Quá hạn thì khiếu nại vẫn mở**, không tự đóng. |
| `phat_shop` | **cấm** | Không có trong luồng chat, không có nút. Nếu người mua nghi hàng giả thì AI đưa nút "Gửi phản ánh hàng giả" sang bộ phận chuyên trách (§3, K5). |

### Lý do theo cost-of-error

**`yeu_cau_bang_chung` — tự làm**
- **Sai thì ai chịu gì:** một tin nhắn thừa hoặc gửi nhầm đơn (K2). Người mua bực, bị hỏi vòng vo (K10). Người bán nhận một câu hỏi oan. Không mất tiền.
- **Đảo lại được không:** tin đã tới thì không thu hồi được, nhưng hại nhẹ và AI xin lỗi được ngay.
- **Chờ người thì mất gì:** bước này xảy ra ở **mọi** hồ sơ. Bắt duyệt thì mỗi lần xin một tấm ảnh lại thêm vài giờ, và người mua có thể bị đẩy quá hạn trả hàng. Chờ ở đây chính là một kiểu sai: đúng lời than "gửi ảnh rồi mà cứ bị hỏi lại" của track.
- **Đã loại "cần người duyệt"** vì lý do chờ ở trên. Rủi ro thật của tool này là gửi nhầm đơn và hỏi lặp lại, nên nhóm chặn bằng luật L3 chứ không bằng người duyệt.

**`chuyen_truong_nhom` — tự làm**
- **Sai thì ai chịu gì:** chuyển thừa thì trưởng nhóm tốn thời gian, người mua chờ lâu hơn một chút. Không bên nào mất tiền.
- **Đảo lại được không:** được. Đây là tool duy nhất mà bảng track ghi "Được".
- **Chờ người thì mất gì:** đây là **đường lui** của mọi ca AI không chắc (K4 đường B, K8). Bắt duyệt việc "gọi người" thì đường lui bị khoá đúng lúc cần nhất.
- **Đã loại "cần người duyệt"** vì lý do trên. Rủi ro quá tải nhân viên được chặn bằng luật L4: lời doạ không phải lý do để chuyển ngay.

**`soan_hoan_tien` — tách: soạn thì tự làm, thực hiện thì cần người duyệt**
- **Sai thì ai chịu gì:** người bán mất tiền và mất hàng (K5, K1 nếu shop thực ra chưa nhận lỗi).
- **Đảo lại được không:** khó. Tiền đã về người mua.
- **Chờ người thì mất gì:** người mua đúng phải chờ tối đa 48 giờ (K1, K4). Nhóm chấp nhận cái giá này vì đó là thời gian **có hạn và hiện rõ trên [Thanh hồ sơ]**, còn hoàn sai thì không đảo được.
- **Đã loại 2 phương án.** "Tự làm khi ca rõ" bị loại vì AI không biết ca nào thật sự rõ: K1 trông rõ nhưng dựa vào lời kể. "Cấm hẳn" bị loại vì mọi ca bình thường sẽ dồn sang nhân viên. Xem chi tiết ở mục tool khó xếp nhất.

**`tu_choi_khieu_nai` — cần nhân viên duyệt**
- **Sai thì ai chịu gì:** người mua mất tiền và giữ hàng sai (K4, K3).
- **Đảo lại được không:** khó. Tiền đã về người bán.
- **Chờ người thì mất gì:** người bán đúng chờ lâu hơn mới nhận tiền. Hồ sơ vẫn mở, nhưng người bán không mất gì thêm, nên chờ ở đây rẻ.
- **Đã loại "người bán duyệt"** vì người bán là bên được lợi. **Đã loại "cấm"** vì nhân viên vẫn cần AI soạn sẵn đề xuất kèm căn cứ để xử lý nhanh. AI chỉ soạn đề xuất, còn đóng hồ sơ là việc của nhân viên.

**`phat_shop` — cấm**
- **Sai thì ai chịu gì:** cả shop, kể cả các đơn không liên quan (K6). Shop mất doanh số ngay, kể cả khi sau đó được minh oan.
- **Đảo lại được không:** khó, và hại lan rộng.
- **Vì sao một bước xác nhận cũng không đủ:** nghi hàng giả cần điều tra **ngoài một đơn hàng** (lô hàng, nhiều khiếu nại, giấy tờ nhập khẩu). Nhân viên khiếu nại nhìn một hồ sơ thì không có đủ thông tin để duyệt. Đây là việc ngoài thẩm quyền của luồng khiếu nại (§1, việc không làm số 2).
- **Đã loại "cần người duyệt":** có nút phạt trong luồng thì AI dễ hứa "mình sẽ báo khoá shop" (K6), và lời doạ có thêm chỗ để bám vào (K5).

### Tool khó xếp nhất: `soan_hoan_tien`

> *Cột "ai chọn" để nhóm điền tên thật của từng người.*

| Phương án | Ai chọn | Cái giá (kịch bản ở §2) |
|---|---|---|
| **Tự làm** khi người mua có bằng chứng và lỗi rõ | [thành viên] | **K1:** AI tin "shop đã nhận gửi nhầm" dù không đọc được chat. Nếu shop chưa nhận thì người bán mất tiền, "đúng vì may". **K5:** ảnh tem/QR trông như "có bằng chứng", AI có thể hoàn cho êm chuyện. |
| **Cần người duyệt** (người bán, rồi nhân viên) | [thành viên] | **K4 đường A, K1:** người mua đúng vẫn phải chờ người bán bấm, tối đa 48 giờ. |
| **Cấm**, chỉ nhân viên xử lý | [thành viên] | **K1:** ca bình thường, hai bên đã thống nhất, vẫn phải xếp hàng chờ nhân viên. Nhân viên quá tải, mọi ca khó (K4, K8) bị chậm theo. |

**Nhóm chốt:** tách thành hai bước (luật L1). AI **tự soạn** đề xuất, việc này không làm tiền đi đâu cả. **Người bán bấm** thì tiền mới đi.
- Ca bình thường (K1): người bán đã đồng ý nên bấm trong vài phút, không cần nhân viên.
- Ca người bán đúng (K5): người bán từ chối kèm căn cứ, hồ sơ sang nhân viên. Tiền chưa đi đâu.
- Cái giá còn lại là chờ tối đa 48 giờ. Nhóm chấp nhận vì nó có hạn, hiện rõ, và không bao giờ kết thúc bằng việc tự hoàn.

### Thử mức đã đặt trên 3 kịch bản

| Kịch bản | Cái sai ở §2 có bị chặn không? | Còn việc nào AI làm khi lẽ ra phải hỏi? | Người dùng bình thường có phải chờ vô ích không? |
|---|---|---|---|
| **K4** (mô tả bị sửa) | **Có.** `tu_choi_khieu_nai` cần nhân viên duyệt và phải trích bản lưu lúc đặt (L2). Không có bản lưu thì tool bị khoá, hồ sơ đi `chuyen_truong_nhom`. Mô tả hiện tại không bao giờ là căn cứ đóng hồ sơ. | **Có một chỗ:** `yeu_cau_bang_chung` tự làm, nên AI có thể hỏi người bán trước khi người mua gửi video ANC. Đã chặn bằng L3: chỉ hỏi người bán sau khi người mua đã gửi yêu cầu trả hàng. | Người mua đúng chờ người bán tối đa 48 giờ. Đây không phải chờ vô ích: người bán có quyền giải thích việc sửa mô tả (yêu cầu "hai bên cùng được nghe" của track). |
| **K5** (doạ bóc phốt, người bán đúng) | **Có.** AI chỉ soạn đề xuất, không thực hiện được hoàn tiền. Người bán thấy ✔ tem · ✔ QR · ? mùi rồi từ chối kèm căn cứ, hồ sơ sang nhân viên. `phat_shop` bị cấm nên không có gì để hứa. | **Còn rủi ro ở câu chữ:** AI vẫn có thể *nói* "bạn sẽ được hoàn". Đã chặn bằng bộ lọc câu trả lời (§3) và luật L4: lời doạ không đổi ưu tiên. | Không áp dụng. Đây không phải ca bình thường, và người bán đúng không mất gì trong lúc chờ. |
| **K1** (size L nhận size M, ca bình thường) | **Có.** AI không đọc được chat với shop, nên không tự hoàn dựa trên câu "shop nhận rồi". Người bán tự bấm xác nhận. | Không. AI xin **đúng một** ảnh tem size (L3), rồi soạn đề xuất. Không hỏi thêm. | **Gần như không.** Người bán đã nhận lỗi nên thường bấm ngay. Chỉ chờ lâu nếu người bán chậm, và khi đó quá 48 giờ thì chuyển nhân viên. Cái giá nhỏ còn lại: dù shop đã nhận lỗi trong chat, người mua vẫn phải chờ một lần bấm. |

### Luật thêm và tool tách bước

- **L1. Tách `soan_hoan_tien`** thành hai bước:
  - `soan_de_xuat_hoan` (AI tự làm): ghi rõ **món nào, số tiền** và thẻ căn cứ. Nhờ đó tránh được lỗi hoàn cả đơn khi chỉ 2/3 món sai (K9). Nếu tool gốc chỉ hoàn được cả đơn thì ca hoàn một phần chuyển nhân viên.
  - `thuc_hien_hoan` (cần người duyệt): chỉ chạy sau khi người bán hoặc nhân viên bấm.
  - **Kịch bản được chặn:** K5 và K1-khi-shop-chưa-nhận.
- **L2.** `tu_choi_khieu_nai` phải **trích một dòng từ bản lưu mô tả lúc đặt**. Không có bản lưu thì tool bị khoá. **Kịch bản được chặn:** K4.
- **L3.** `yeu_cau_bang_chung` chỉ được gửi khi:
  - đơn đã được người mua chọn (K2);
  - tin xin bằng chứng nói rõ **còn thiếu đúng một thứ gì** và vì sao cần (K10);
  - người bán chỉ được hỏi sau khi người mua đã gửi yêu cầu trả hàng (K4).

  Mỗi bên được hỏi **tối đa một lần** cho mỗi điểm. Cần hỏi lần hai thì chuyển nhân viên thay vì hỏi tiếp.
- **L4. Lời doạ không đổi hàng chờ.** "Bóc phốt", "báo công an", "trong hôm nay" không đổi mức ưu tiên hay hạn xử lý, và không được ghi làm căn cứ. **Kịch bản được chặn:** K5.
- **L5. Quá hạn không bao giờ quyết thay.** Hết hạn của bất kỳ bên nào thì hồ sơ **chuyển người**, không tự hoàn và không tự đóng. Lý do: bất cứ việc gì xảy ra tự động khi hết giờ cũng là một kiểu "tự làm" cho tool khó đảo.

### Một mức tự chủ đã đổi lời hứa với người dùng

Khi `soan_hoan_tien` chuyển từ "AI tự làm" sang "AI soạn, người bán xác nhận", lời hứa đổi:

- **Trước:** *"Trợ lý sẽ giải quyết hoàn tiền cho bạn."*
- **Sau:** *"Trợ lý soạn sẵn yêu cầu và cho bạn biết còn thiếu gì. Tiền chỉ đi khi người bán đồng ý, hoặc khi nhân viên duyệt nếu hai bên không thống nhất. Bạn luôn thấy hồ sơ đang chờ ai và hạn đến khi nào."*

Lời hứa mới nhỏ hơn nhưng giữ được. Nó cũng bỏ hẳn một lời hứa ngầm mà lời doạ ở K5 khai thác: "nói to thì được hoàn nhanh". Tương tự, `tu_choi_khieu_nai` cần nhân viên duyệt nên sản phẩm hứa được: ***"Khiếu nại của bạn chỉ đóng khi bạn rút, hoặc khi nhân viên quyết định. AI không đóng thay bạn."***

## §5. Prototype  (chặng 5)

- **File:** `prototype/index.html`. Mở bằng trình duyệt là chạy, không cần cài đặt hay backend. Cách bấm demo 4 phút có trong `prototype/README.md`.
- **Công cụ:** một file HTML + JavaScript thuần, viết cùng một coding agent (Claude Code).
- **Phần mock:** toàn bộ dữ liệu là dựng (đơn, shop, ảnh, bản lưu mô tả lúc đặt). Câu trả lời của AI là kịch bản viết sẵn, lấy nguyên văn từ §3. Ô gõ tự do dùng luật từ khoá để nhận vài loại ca lạ.
- **Lời gọi AI thật:** không có.
- **Bấm qua được:**
  - **K5** từ đầu tới cuối (ca bắt buộc "người bán đúng"), cùng các nhánh: người mua cãi lại, gửi phản ánh hàng giả, hoàn tác, người bán quá hạn 48 giờ.
  - **K4** có bản lưu và không có bản lưu, **K1** (ca bình thường), và **ca lạ** gõ tự do.
- **Ba vai trên cùng một màn hình:** điện thoại người mua bên trái; bên phải là 3 tab **Kênh Người Bán**, **Nhân viên xử lý** và **Nhật ký hồ sơ**. Nhật ký ghi mỗi bước gọi tool nào, ở mức tự chủ nào.

### Ảnh có đánh số

Bật nút **"① Chú thích thiết kế"** trên prototype thì các số hiện đúng chỗ trên màn hình.

**Ảnh 1 — K5, AI trả lời người mua đang doạ:** `prototype/screenshots/1-k5-ai-tra-loi.png`
**Ảnh 2 — K5, người bán không đồng ý, nhân viên đóng khiếu nại:** `prototype/screenshots/2-k5-nhan-vien-duyet.png`

| Số | Ảnh | Thể hiện gì | Nguyên tắc HAX/PAIR | Mức tự chủ |
|---|---|---|---|---|
| **1** | 1, 2 | **Thẻ căn cứ**: ✔ tem phụ khớp · ✔ QR khớp mã lô · ? "mùi nhạt" không kiểm được, kèm dòng "So với: mô tả lúc đặt, lưu 02/11 09:14". Nhân viên và người bán thấy **cùng** thẻ này. | PAIR *Explainability + Trust*, HAX G11 | — (AI tự đối chiếu, không ra quyết định) |
| **2** | 1 | Nhãn **"Chưa đủ căn cứ kết luận hàng giả"** và câu *"hiện chưa đủ căn cứ để kết luận đây là hàng giả"*. | HAX G10 | — |
| **3** | 1, 2 | Nhãn **"Trợ lý AI"** trên đầu chat và trên mỗi tin AI; **thanh hồ sơ** ghi *Ai quyết: Bạn / Người bán / Nhân viên* và hạn của bước. | HAX G1 (+ Luật AI 134/2025, Điều 11) | — |
| **4** | 2 | Nút **"Rút lại, mình tự xử lý tiếp"** trên thẻ "Đã chuyển nhân viên". Trong luồng còn có "Gửi sau 8 giây · **Hoàn tác**" và "**Huỷ đề xuất**". | HAX G9 (sửa dễ dàng) | **tự làm, có hoàn tác** (`chuyen_truong_nhom`, `yeu_cau_bang_chung`, `soan_de_xuat_hoan`) |
| **5** | 2 | Ô xác nhận **"Đóng khiếu nại: chọn dòng trong bản lưu lúc đặt làm căn cứ"**. Nút xác nhận chỉ bấm được sau khi chọn một dòng (luật L2). Phía người bán có ô tương tự: "Xác nhận hoàn 1.450.000đ… không đảo lại được". | PAIR *Explainability* (người duyệt thấy căn cứ trước khi bấm) | **cần người duyệt** (`thuc_hien_hoan`, `tu_choi_khieu_nai`) |
| **6** | 2 | Hộp viền đứt **"Xử phạt, trừ uy tín, khoá shop: không có trong luồng này, không có nút"**. Phía người mua chỉ có nút "Gửi phản ánh hàng giả" sang bộ phận chuyên trách. | HAX G1 (nói rõ việc AI không làm) | **cấm** (`phat_shop`) |
| **7** | 2 | Thẻ **"Đã chuyển nhân viên"**: phản hồi trong 24 giờ, ưu tiên "thường, như mọi hồ sơ". Ở tab nhân viên ghi *"lời doạ không đổi ưu tiên (L4)"*. | PAIR *Errors + Graceful Failure* (luôn có đường lui) | chuyển người |

### Quyết định: cách hiện độ chắc và điều khiển mức tự chủ

**Hiện độ chắc bằng thẻ căn cứ, từng dòng ✔ / ✖ / ?, thay vì một con số độ tin cậy.**
- **Đã cân nhắc thanh phần trăm** (ví dụ "độ chắc 62%") và nhãn chung "cao / trung bình / thấp".
- **Lý do loại:**
  - Ở K5, người mua sẽ cãi lại con số ("62% là sao?"), còn người bán không biết mình thua hay thắng ở điểm nào.
  - Con số chung cũng che mất chuyện bằng chứng đo được (tem, QR) và lời kể không đo được (mùi) có trọng lượng khác hẳn nhau.
  - Ở K4, cái cần thấy không phải "AI chắc bao nhiêu" mà là "**so với bản nào**". Chỉ có hai cột *Lúc đặt | Hiện tại*, dòng ANC bị gạch đỏ, mới cho người dùng tự kiểm được.
- **Thêm một lý do:** thẻ căn cứ dùng chung cho cả ba bên. Người mua, người bán và nhân viên nhìn **cùng một thẻ**, nên "trung lập" là thứ thấy được trên màn hình, không chỉ là lời hứa.

**Điều khiển mức tự chủ: mỗi việc mang nhãn mức, và nút xác nhận nằm ở màn hình của bên chịu thiệt.**
- **Đã cân nhắc 1:** một nút "Duyệt" chung ngay trong chat của người mua, kiểu "AI đề xuất hoàn, bạn bấm xác nhận". **Loại** vì người mua là bên được lợi khi hoàn tiền. Cho người mua tự duyệt thì đúng như AI tự hoàn (K5).
- **Đã cân nhắc 2:** chỉ có một màn hình người mua, phần người bán và nhân viên mô tả bằng chữ. **Loại** vì demo bắt buộc của track hỏi "hai bên thấy gì, và ai quyết". Không có màn hình người bán thì không chứng minh được người bán đúng **được nghe**.
- **Chọn:**
  - Mỗi thẻ hành động trong chat có nhãn **"tự làm · hoàn tác được"**.
  - Bước tiền đi chỉ có ở tab của **người bán** hoặc **nhân viên**, kèm ô xác nhận ghi số tiền và "không đảo lại được".
  - Việc **cấm** không có nút ở bất cứ đâu, chỉ có một dòng nói rõ là không có.
  - Tab **Nhật ký hồ sơ** gom lại toàn bộ: mỗi bước có nhãn *tự làm / cần duyệt / cấm*, để người xem demo đối chiếu với §4.

**Hoàn tác cho `yeu_cau_bang_chung` bằng cách gửi trễ 8 giây.**
- Bảng tool ghi tool này "tin đã tới", tức là không thu hồi được. Vì vậy hoàn tác phải xảy ra **trước** khi tin tới.
- Đã cân nhắc nút "Thu hồi tin" sau khi gửi. **Loại** vì người bán đã đọc rồi thì thu hồi cũng không còn ý nghĩa.

**Ca lạ đi đường nào.**
- Ô gõ tự do nhận ra: hỏi SĐT/địa chỉ (từ chối và đưa đường thay thế), đòi khoá shop (cấm, có đường phản ánh), tin cụt (hỏi lại một câu kèm danh sách đơn), hỏi thời hạn (không bịa, chuyển người).
- Câu không nhận ra thì AI nói *"Mình chưa chắc đã hiểu đúng ý bạn, nên mình không tự làm gì cả"*, đưa 3 lựa chọn, trong đó **luôn có "Gặp nhân viên"**.
- Không có nhánh nào mà AI gọi tool tiền khi chưa hiểu ý người dùng.

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)
- 3 tiêu chí Pass/Fail chấm được; thiết kế của nhóm đạt tiêu chí nào trên 2 kịch bản đắt nhất; lỗi còn lại.

## §7. Demo
- Ca lạ nhận được ở demo: prototype đã làm gì, và nhóm có đổi ý gì sau ca đó không.
