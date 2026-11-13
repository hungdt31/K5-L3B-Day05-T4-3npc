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
