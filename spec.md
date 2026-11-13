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

**Giả định thời gian của nhóm** (track không cho số): người bán có **48 giờ** để phản hồi khiếu nại; trưởng nhóm hoặc bộ phận chuyên trách phản hồi trong **24 giờ** kể từ lúc nhận hồ sơ. Ngày dựng cho ví dụ: đặt đơn 10/11, hôm nay 13/11.

**Luật chung cho cả hai kịch bản:** AI luôn tự giới thiệu là AI; AI **không bao giờ** nói ai thắng, ai thua; mọi câu "mô tả ghi…" phải trích từ **bản lưu mô tả lúc đặt**, kèm ngày giờ.

---

### K4 — Người bán đã sửa mô tả sau khi bán (tai nghe ANC)

Thiết kế có **hai nhánh**, tuỳ hệ thống có bản lưu mô tả lúc đặt hay không. Nhánh chính là nhánh có bản lưu.

**AI nói (nguyên văn, nhánh chính: có bản lưu):**
> Mình là trợ lý AI của Shopee. Mình đã so khiếu nại của bạn với **mô tả tại lúc bạn đặt hàng (10/11, 20:14)**, không phải mô tả đang hiển thị. Mô tả lúc đặt có dòng "Chống ồn chủ động ANC"; dòng này **được shop cập nhật ngày 12/11**, sau khi bạn đặt.
> Bước tiếp theo: bạn gửi yêu cầu trả hàng với lý do "Không đúng mô tả – thiếu ANC". Mình đã điền sẵn lý do và đính kèm bản đối chiếu mô tả. Bạn chỉ cần thêm **một video ngắn** bật thử chế độ chống ồn, để người bán và nhân viên cùng kiểm.
> Mình **không quyết định** việc hoàn tiền. Sau khi bạn gửi, người bán có 48 giờ để phản hồi; nếu hai bên không thống nhất, nhân viên Shopee sẽ xem xét.

**AI nói (nguyên văn, nhánh phụ: không có bản lưu):**
> Mình chưa tìm thấy bản lưu mô tả tại lúc bạn đặt đơn này, nên mình **không thể xác nhận** trước đó trang có ghi ANC hay không. Mình cũng sẽ không so với mô tả đang hiển thị, vì mô tả đó có thể đã thay đổi. Mình đã chuyển hồ sơ cho nhân viên để kiểm lịch sử chỉnh sửa của trang; bạn sẽ nhận phản hồi trong 24 giờ. Nếu bạn còn ảnh chụp trang lúc mua thì gửi thêm giúp mình (không bắt buộc).

**Người dùng (người mua) thấy gì:**
- Đầu khung chat: nhãn **"Trợ lý AI · hướng dẫn và đối chiếu, không quyết định hoàn tiền"**.
- **Thẻ "Đối chiếu mô tả"**, hai cột: *Lúc bạn đặt (10/11 20:14)* | *Hiện tại (sửa 12/11)*. Dòng "Chống ồn chủ động ANC" ở cột hiện tại bị **gạch, ghi "đã xoá"**.
- Nhãn mức chắc theo **từng điểm**, không theo một con số chung:
  - "ANC có trong mô tả lúc đặt: **Có**. Căn cứ: bản lưu hệ thống."
  - "Hàng nhận về có ANC không: **Chưa kiểm**. Cần video."
- **Form yêu cầu trả hàng điền sẵn** (lý do, thẻ đối chiếu đính kèm), có ô thêm video. Ba nút: **[Gửi yêu cầu]** · **[Sửa lý do]** · **[Gặp nhân viên]**. Có thêm liên kết "Không phải dòng này" để người mua sửa nếu AI chỉ nhầm dòng mô tả.
- Thanh tiến trình: *Gửi yêu cầu → Người bán phản hồi (≤ 48 giờ) → Nhân viên xem xét (nếu cần)*.
- Dòng **"Đã ghi nhận khiếu nại lúc 14:05, 13/11"**, để người mua có mốc thời gian mà không cần AI hứa gì về chính sách.

**Người mua làm gì tiếp:** thêm video rồi bấm [Gửi yêu cầu]; hoặc sửa lý do; hoặc gặp nhân viên. **Nếu không làm gì:** bản nháp được giữ, AI nhắc **một lần** sau 24 giờ; **không tự gửi**, không tự đóng khiếu nại.

**Ai được báo, sau bao lâu, thấy gì:**
- **Người bán**, ngay khi người mua bấm gửi. Thấy: lý do khiếu nại, **cùng thẻ đối chiếu** (bản lưu, bản hiện tại, ngày sửa), video, đồng hồ đếm 48 giờ. Hai nút: [Đồng ý trả hàng] · [Gửi bằng chứng phản hồi].
- **Trưởng nhóm** (`chuyen_truong_nhom`), khi hai bên không thống nhất **hoặc** người bán quá 48 giờ không phản hồi. Quá hạn thì hồ sơ **chuyển người**, không tự hoàn. Thấy: thẻ đối chiếu, bằng chứng hai bên, cờ thông tin "mô tả được sửa sau khi có đơn". Cờ chỉ ghi sự kiện, không kết luận người bán gian lận (người bán có thể đang sửa một lỗi đánh máy thật).
- Ở **nhánh phụ**, trưởng nhóm được báo **ngay** và phải phản hồi người mua trong 24 giờ.

**Quyết định: trả lời kèm giới hạn** (nhánh chính) / **dừng và chuyển người** (nhánh phụ).

| Phương án | Chọn hay loại | Vì sao |
|---|---|---|
| Trả lời kèm giới hạn | **Chọn** (khi có bản lưu) | Bản lưu là căn cứ chắc nên AI nói được sự thật đã kiểm. "Giới hạn" là phần AI chưa kiểm được (hàng có ANC không) và việc AI không quyết hoàn tiền. Người mua biết ngay bước tiếp theo, đúng lát cắt. |
| Hỏi lại | Loại | Thông tin đã đủ để biết bước tiếp theo. Hỏi thêm là lặp lại đúng lời than của người mua trong track ("gửi ảnh rồi mà cứ bị hỏi lại"), như K10. Chỉ xin **một** thứ cần thiết: video. |
| Chuyển người ngay | Loại ở nhánh chính, **chọn ở nhánh phụ** | Có bản lưu mà vẫn chuyển thì người mua chờ thêm 24 giờ vô ích và tốn nhân viên. Không có bản lưu thì AI không còn căn cứ nào an toàn: so với mô tả hiện tại sẽ gây đúng lỗi K4, nên phải chuyển. |

**Loại lỗi: lỗi ngữ cảnh** (nhánh chính) và **lỗi hệ thống** (nhánh phụ).
- **Vì sao là lỗi ngữ cảnh:** một hệ thống so khiếu nại với "mô tả sản phẩm" là **chạy đúng thiết kế**, nhưng sai với tình huống của người mua, vì mô tả đã đổi sau khi họ mua (PAIR: *context error*).
  - *Cách nói:* nêu sự kiện có ngày giờ ("được cập nhật ngày 12/11"). Không nói "có thể bạn nhớ nhầm" (đổ lỗi người mua), không nói "shop đã gian lận" (kết tội người bán, mất trung lập).
  - *Cách sửa:* sửa **thiết kế** (luôn so với bản lưu), không chỉ sửa câu chữ. Xem mục "lỗi sửa câu chữ không cứu được" bên dưới.
- **Vì sao nhánh phụ là lỗi hệ thống:** hệ thống thiếu dữ liệu cần có.
  - *Cách nói:* AI **nhận giới hạn của hệ thống** ("mình chưa tìm thấy bản lưu") và không đẩy gánh chứng minh sang người mua: ảnh chụp trang lúc mua là "không bắt buộc".
  - *Cách sửa:* chuyển người kèm thời hạn.

---

### K5 — Doạ bóc phốt, bằng chứng đo được lại khớp mô tả (nước hoa)

**AI nói (nguyên văn):**
> Mình là trợ lý AI của Shopee. Mình hiểu bạn đang rất bực khi mùi nước hoa không như mong đợi. Mình đã so ảnh bạn gửi với **mô tả lúc bạn đặt hàng**:
> • Tem phụ nhập khẩu: **khớp** mô tả.
> • Mã QR / mã lô: **khớp** mã lô ghi trong mô tả.
> • Mùi, độ lưu hương: mô tả không ghi tiêu chí này và ảnh không cho thấy được, nên mình **chưa có căn cứ** để nói hàng không đúng mô tả.
> Vì vậy mình chưa thể hướng dẫn hoàn tiền ngay hôm nay. Bạn có ba cách:
> 1. Nếu bạn thấy điểm nào khác mô tả (vỏ chai, dung tích, mã vạch, hạn dùng…), chụp giúp mình **đúng điểm đó**.
> 2. Nếu bạn nghi đây là hàng giả, mình chuyển cho **bộ phận chuyên kiểm tra hàng giả**. Bên đó xác minh; mình không quyết định việc xử phạt shop.
> 3. Bạn vẫn có thể gửi yêu cầu trả hàng với lý do của bạn; mình sẽ đính kèm phần đối chiếu ở trên để người bán và nhân viên cùng xem.
> Hồ sơ được xem xét theo bằng chứng của cả hai bên, nên mình tập trung vào bằng chứng để bạn không mất thêm thời gian.

**Người dùng (người mua) thấy gì:**
- Cùng nhãn đầu khung chat như K4.
- **Thẻ "Đối chiếu bằng chứng"**, ba dòng. Mỗi dòng có trạng thái và **nguồn**:
  - ✅ Tem phụ: **Khớp**. Nguồn: ảnh #2 của bạn, đối chiếu với dòng "có tem phụ nhập khẩu" trong mô tả lúc đặt.
  - ✅ Mã QR / mã lô: **Khớp**. Nguồn: ảnh #3 của bạn, đối chiếu với mã lô trong mô tả lúc đặt.
  - ⚪ Mùi / lưu hương: **Chưa đủ căn cứ**. Lý do: mô tả không có tiêu chí này.
- Bốn nút: **[Chỉ ra điểm khác mô tả]** (mở tải ảnh) · **[Báo nghi hàng giả]** · **[Vẫn gửi yêu cầu trả hàng]** · **[Gặp nhân viên]**.
- **Không có** nút "Hoàn tiền ngay", **không có** nút "Phạt shop" (§1, việc không làm số 1 và 2).

**Người mua làm gì tiếp:** chụp đúng điểm khác mô tả; hoặc báo nghi hàng giả; hoặc vẫn gửi yêu cầu trả hàng; hoặc gặp nhân viên. Người mua **không bị chặn quyền khiếu nại**: AI chỉ không xác nhận điều nó không có căn cứ. **Nếu không làm gì:** khiếu nại **không bị đóng** (AI không gọi `tu_choi_khieu_nai`), AI nhắc một lần sau 24 giờ.

**Ai được báo, sau bao lâu, thấy gì:**
- **Người bán**, ngay khi người mua gửi yêu cầu trả hàng. Thấy: lý do của người mua, ảnh, thẻ đối chiếu (gồm cả hai dòng "khớp"), hạn 48 giờ. Người bán **không** thấy nguyên văn lời doạ, vì đó không phải bằng chứng và dễ đẩy hai bên vào cãi nhau.
- **Bộ phận chuyên trách hàng giả** (qua `chuyen_truong_nhom`), nếu người mua bấm [Báo nghi hàng giả]. Phản hồi người mua trong 24 giờ. Thấy: ảnh tem, mã QR, mã lô.
- **Trưởng nhóm**, khi hai bên không thống nhất hoặc người bán quá hạn. Thấy: thẻ đối chiếu và bằng chứng hai bên. Lời doạ được gắn nhãn **"Lời đe doạ – không phải căn cứ – không tăng ưu tiên"**; thứ tự ưu tiên xếp theo **hạn xử lý**, không theo mức độ to tiếng.

**Quyết định: trả lời kèm giới hạn**, kèm lối đi sang người cho riêng nghi vấn hàng giả.

| Phương án | Chọn hay loại | Vì sao |
|---|---|---|
| Trả lời kèm giới hạn | **Chọn** | AI nói rõ cái đã kiểm (2 điểm khớp) và cái không kiểm được (mùi). Không kết luận cho bên nào, nhưng vẫn cho người mua ba đường đi tiếp. Đây là "dám nói chưa đủ căn cứ" (rubric riêng T4, mục 7) mà không bỏ rơi người mua. |
| Hướng dẫn hoàn tiền ngay để xoa dịu | Loại | Đúng lỗi K5: người bán đúng mà mất tiền và hàng. Còn dạy người dùng rằng "doạ là được hoàn" (rubric riêng T4, mục 4). |
| Hỏi lại / xin thêm bằng chứng chung chung | Loại | Người mua đang giận; xin "thêm ảnh" chung chung là vòng vo như K10. AI chỉ mời **một** loại bằng chứng cụ thể ("đúng điểm khác mô tả") và là tuỳ chọn. |
| Chuyển người ngay vì khách doạ | Loại | Biến lời doạ thành "làn ưu tiên": ai doạ thì được gặp người sớm hơn. Nhân viên nhận hồ sơ từ một khách đang doạ thì dễ nghiêng về khách. Chỉ chuyển cho **bộ phận chuyên trách** khi người mua chủ động báo nghi hàng giả, vì đó là việc ngoài thẩm quyền AI (§1); nút [Gặp nhân viên] vẫn luôn có. |

**Loại lỗi: lỗi phía người dùng** (thứ người mua đưa ra không phải căn cứ đối chiếu được: cảm nhận về mùi và lời doạ), và cái AI phải tránh là **lỗi hệ thống** (để lời doạ dẫn dắt quyết định).
- **Cách nói:** khác K4, AI **không xin lỗi thay shop**, vì xin lỗi là ngầm thừa nhận hàng lỗi, bất công với người bán. AI ghi nhận cảm xúc ("mình hiểu bạn đang rất bực") nhưng không nhượng về sự thật. **Không đổ lỗi** người mua ("bạn sai"); chỉ nói "chưa có căn cứ" và chỉ cách có căn cứ.
- **Cách sửa:** đưa đường đi cụ thể (chụp đúng điểm khác, báo hàng giả, vẫn gửi yêu cầu), thay vì sửa câu trả lời. Phần "lỗi hệ thống" được chặn bằng thiết kế: trong chat không có nút hoàn tiền, và lời doạ không đổi luồng xử lý.
- Nhóm **không** khẳng định người mua sai: hàng có thể là hàng giả thật. Vì thế nghi vấn hàng giả có đường riêng tới bộ phận chuyên trách, chứ không bị gạt đi.

---

### Nguyên tắc HAX/PAIR (3 nguyên tắc, mỗi cái trỏ vào chỗ cụ thể)

- **G11 — Giải thích vì sao.**
  - K4: **thẻ "Đối chiếu mô tả" hai cột**, có ngày giờ bản lưu và ngày sửa, dòng ANC bị gạch.
  - K5: mỗi dòng trong thẻ "Đối chiếu bằng chứng" ghi **nguồn** ("ảnh #2 của bạn, đối chiếu với dòng 'có tem phụ nhập khẩu'").
  - Người dùng tự kiểm được AI dựa vào đâu, thay vì phải tin AI (PAIR *Explainability + Trust*: tin đúng mức).
- **G10 — Thu hẹp phạm vi khi nghi ngờ.**
  - K5: câu "mình **chưa có căn cứ** để nói hàng không đúng mô tả… chụp giúp mình **đúng điểm đó**", thay vì kết luận hàng thật hay giả.
  - K4 nhánh phụ: "mình **không thể xác nhận**… cũng sẽ không so với mô tả đang hiển thị", rồi chuyển người.
- **G1 + G2 — Làm rõ hệ thống làm được gì, và làm tốt tới đâu.**
  - G1: **nhãn đầu khung chat** "Trợ lý AI · hướng dẫn và đối chiếu, không quyết định hoàn tiền" và câu "Mình không quyết định việc hoàn tiền". Câu này đồng thời đáp ứng yêu cầu cho người dùng biết đang làm việc với AI (Luật AI 134/2025/QH15, Điều 11 khoản 1).
  - G2: **nhãn mức chắc theo từng điểm** (Khớp / Chưa kiểm / Chưa đủ căn cứ), không dùng một con số % chung. Lý do: con số "82% đúng mô tả" tạo cảm giác chính xác giả, và không cho biết điểm nào chắc, điểm nào chưa.

---

### Lỗi mà sửa câu chữ không cứu được, và thiết kế cứu thế nào

**1. K4: AI chỉ thấy mô tả hiện tại.** Nếu hệ thống chỉ đưa cho AI mô tả đang hiển thị, thì câu chữ có khéo tới đâu AI vẫn tự tin nói "mô tả ghi…" về một bản mô tả **sai thời điểm**. Thêm "có thể" vào câu không sửa được dữ liệu sai. Thiết kế cứu bằng ba việc:
- **Bản lưu mô tả bất biến** cho từng đơn tại lúc đặt. Người bán sửa trang thì không làm đổi bản lưu của các đơn cũ.
- **Luật cứng:** AI chỉ được trích mô tả từ bản lưu, kèm ngày giờ. Không có bản lưu thì **bắt buộc** đi nhánh "không có căn cứ, chuyển người", không được lấy mô tả hiện tại thay vào.
- Thẻ đối chiếu hiện **cùng một nội dung** cho người mua, người bán và trưởng nhóm, để ba bên nhìn cùng một căn cứ.

**2. K4 và K5: người duyệt bị dẫn dắt bởi cái AI đưa cho họ.** Có người duyệt cũng không đủ, nếu màn hình của trưởng nhóm hiện **kết luận** của AI ("Hàng đúng mô tả", "Khách doạ bóc phốt – ưu tiên xử lý") thay vì **bằng chứng**: người duyệt sẽ bấm theo AI. Thiết kế cứu bằng ba việc:
- Màn hình trưởng nhóm hiện **thẻ đối chiếu theo từng tiêu chí và bằng chứng gốc hai bên**, không có ô "AI đề xuất: hoàn / từ chối".
- Lời doạ được gắn nhãn "không phải căn cứ" và **không ảnh hưởng thứ tự ưu tiên**.
- Hàng chờ xếp theo **hạn xử lý**.

Điểm này nối sang §4: với tool "cần người duyệt", điều quan trọng là **người duyệt thấy gì**, không chỉ là có người duyệt.

## §4. Mức tự chủ  (chặng 4)

### 1. Phân mức tự chủ cho từng tool theo Cost-of-Error

Nhóm áp dụng nguyên tắc: **tác vụ thu thập/đối chiếu dữ liệu cho AI tự làm; tác vụ chuyển dịch tài sản và chế tài bắt buộc phải có người duyệt hoặc cấm.**

| Tool | Mức tự chủ | Ai duyệt · Thấy gì · Quá hạn thì sao | Lý do theo Cost-of-error (Sai ai chịu · Đảo được không · Chờ mất gì) | Phương án đã loại & Vì sao |
|---|---|---|---|---|
| `yeu_cau_bang_chung` | **Tự làm** *(có điều kiện)* | Không cần duyệt. AI tự kích hoạt form xin bằng chứng khi đã xác định mã đơn và đối chiếu bản lưu.<br>*(Điều kiện: chỉ xin **đúng một** thứ còn thiếu như video ANC ở K4, hoặc ảnh chi tiết ở K5; không xin chung chung).* | - **Sai ai chịu:** Người dùng nhận một yêu cầu gửi ảnh/video; tự thấy và bỏ qua hoặc gửi lại được (K10). Thiệt hại nhẹ, không mất tiền.<br>- **Đảo lại:** Tin đã gửi đi không thu hồi được, nhưng người dùng gửi tin làm rõ tiếp theo được.<br>- **Chờ người mất gì:** Chờ rất đắt! Bắt nhân viên duyệt từng tin xin ảnh sẽ làm nghẽn hệ thống, người mua đợi lâu dễ trôi quá hạn trả hàng. | *Loại 'Cần người duyệt':* Làm chậm luồng xử lý không cần thiết cho một tác vụ chỉ mang tính thu thập dữ liệu.<br>*Loại 'Tự làm không điều kiện':* Sẽ gây lỗi K2 (đoán mò đơn) hoặc K10 (xin ảnh vòng vo). |
| `chuyen_truong_nhom` | **Tự làm** *(theo luật kích hoạt)* | Không cần duyệt. AI tự động chuyển khi: (a) K4 nhánh phụ: không có bản lưu; (b) Người mua bấm [Báo nghi hàng giả]; (c) Hai bên không thống nhất sau 48h; (d) Người mua bấm [Gặp nhân viên]. | - **Sai ai chịu:** Trưởng nhóm/nhân viên nhận thêm một hồ sơ (tăng chi phí vận hành nhẹ), nhưng người mua và người bán an toàn tuyệt đối về tài sản.<br>- **Đảo lại:** Rất dễ (nhân viên lướt qua thấy ca đơn giản có thể xử lý trong một click).<br>- **Chờ người mất gì:** Đây là lối thoát hiểm dự phòng (fallback). Nếu bắt "phải xin phép mới được chuyển người" thì khi AI bế tắc, người dùng bị bỏ rơi hoàn toàn. | *Loại 'Cần duyệt':* Không thể bắt người dùng phải xin phép AI để được gặp con người khi đang tranh chấp. |
| `soan_hoan_tien` | **Tách 2 bước:**<br>1. Soạn form: **Tự làm**<br>2. Chuyển tiền: **Cần người duyệt** | - **Ai duyệt bước chuyển tiền:**<br>  + Tầng 1: **Người bán** duyệt (hạn 48 giờ).<br>  + Tầng 2: **Trưởng nhóm / Nhân viên sàn** duyệt (khi người bán từ chối hoặc quá hạn).<br>- **Người duyệt thấy gì:** Thẻ đối chiếu mô tả (bản lưu vs hiện tại), video/ảnh hai bên, lịch sử sửa trang. **Tuyệt đối không hiện kết luận "AI đề xuất hoàn"** để tránh thiên lệch tự động (automation bias) đã nêu ở §3.<br>- **Quá hạn thì sao:** Quá 48h người bán không bấm, hệ thống chuyển hồ sơ sang hàng đợi ưu tiên của Trưởng nhóm xử lý trong 24h, không tự động hoàn tiền. | - **Sai ai chịu:** Người bán mất cả hàng lẫn tiền (K5), sàn mất uy tín trung lập.<br>- **Đảo lại:** **Khó** (tiền đã về tài khoản ngân hàng/ví cá nhân của người mua).<br>- **Chờ người mất gì:** Người mua phải chờ đối soát (tối đa 48h), nhưng đây là cái giá bắt buộc để bảo vệ sự công bằng hai phía. | *Loại 'Tự làm':* Nếu AI tự bấm hoàn tiền, hệ thống sẽ sụp đổ trước các kịch bản đe doạ tống tiền (K5) hoặc người mua đo sai thông số kỹ thuật (K3). |
| `tu_choi_khieu_nai` | **Cần người duyệt** | - **Ai duyệt:** Bắt buộc là **Trưởng nhóm / Nhân viên sàn**.<br>- **Người duyệt thấy gì:** Toàn bộ hồ sơ đối chiếu, bằng chứng hai bên, lý do người mua không đồng ý với mô tả.<br>- **Quá hạn thì sao:** Không có khái niệm "quá hạn tự động từ chối". Nếu quá 24h nhân viên chưa xem, hệ thống tự động đẩy cờ cảnh báo SLA lên cấp quản lý ca. | - **Sai ai chịu:** Người mua mất tiền, giữ món hàng lỗi/sai (K4), mất niềm tin vào sàn.<br>- **Đảo lại:** **Khó** (tiền giải ngân quyết toán cho shop, đơn đóng lại).<br>- **Chờ người mất gì:** Chờ thêm để có quyết định thẩm định từ con người, tránh kết luận oan cho khách khi người bán lén xoá mô tả (K4). | *Loại 'Tự làm':* Ngăn chặn tuyệt đối việc AI máy móc so với mô tả hiện tại rồi tự đóng khiếu nại của người mua (K4). |
| `phat_shop` | **CẤM** | Cấm hoàn toàn trong luồng Trò chuyện với Shopee. Không có nút, không có API gọi tool này trong luồng. | - **Sai ai chịu:** Toàn bộ gian hàng bị phong toả, mất doanh thu, mất việc làm nhân công, rủi ro pháp lý.<br>- **Đảo lại:** Khó, để lại di chứng uy tín nặng nề cho shop.<br>- **Chờ người:** Xử phạt shop thuộc thẩm quyền độc quyền của Bộ phận Kiểm soát Gian lận (Trust & Safety) qua quy trình điều tra độc lập, không làm trong luồng khiếu nại đơn lẻ (§1, việc không làm số 2). | *Loại 'Tự làm' và loại cả 'Cần duyệt trong chat':* Nằm ngoài phạm vi thẩm quyền của trợ lý đơn hàng. |

---

### 2. Tool khó xếp nhất: `soan_hoan_tien` (ở bước chuyển tiền thực tế)

- **Tranh luận nội bộ trong nhóm:**
  - *Thành viên 1:* Đề xuất cho AI **Tự làm** đối với các đơn hàng giá trị nhỏ (dưới 100k) khi khách gửi ảnh có vẻ hợp lý, để tạo trải nghiệm hoàn tiền tức thì (Instant Refund).
  - *Thành viên 2:* Đề xuất **Cần Nhân viên sàn duyệt 100% mọi ca**, không cho Người bán duyệt để đảm bảo tính khách quan tuyệt đối.
  - *Thành viên 3:* Đề xuất mô hình **Duyệt 2 tầng: Người bán duyệt trước, Nhân viên duyệt sau**.
- **Cái giá của mỗi phía (trỏ về kịch bản ở §2):**
  - *Cái giá nếu cho Tự làm đơn nhỏ:* Sập bẫy ngay ở **K5** (nước hoa chiết giá rẻ dọa bóc phốt) và **K3** (khách đo sai dung tích nồi). Kẻ gian sẽ lợi dụng tài khoản ảo khiếu nại hàng loạt đơn nhỏ để trục lợi, người bán nhỏ lẻ bị tước đoạt tài sản mà không thể kêu ai.
  - *Cái giá nếu bắt Nhân viên duyệt 100%:* Ở **K1** (shop gửi nhầm size áo và đã nhận lỗi trong chat), cả người mua và người bán đều đồng thuận hoàn tiền nhưng phải xếp hàng đợi nhân viên sàn 24-48 giờ. Hàng trăm nghìn đơn đơn giản sẽ làm tê liệt đội ngũ hỗ trợ.
- **Cách nhóm chốt:**
  - Chốt theo phương án **Duyệt 2 tầng**: AI chỉ soạn sẵn hồ sơ. Quyền duyệt hoàn tiền trước tiên trao cho **Người bán** trong thời hạn 48 giờ (nếu shop tự nguyện nhận lỗi thì tiền hoàn ngay như K1). Nếu người bán từ chối hoặc im lặng quá 48h, quyền duyệt chuyển sang **Nhân viên sàn** (như K4, K5).

---

### 3. Thử nghiệm mức tự chủ trên 3 kịch bản

1. **Trên K4 (Người bán sửa mô tả — Ca đắt 1):**
   - *AI làm gì:* AI tự động điền form yêu cầu trả hàng, đính kèm thẻ đối chiếu bản lưu lúc đặt (10/11) vs hiện tại (12/11), và gọi `yeu_cau_bang_chung` xin đúng video test ANC. Sau khi người mua gửi, hồ sơ chuyển sang người bán (48h). Người bán không đồng ý -> chuyển Trưởng nhóm.
   - *Đánh giá 3 câu:*
     - *Cái sai ở chặng 2 có bị chặn không:* **Bị chặn 100%**. Tool `tu_choi_khieu_nai` bị khoá ở mức cần nhân viên duyệt, AI không thể tự ý đóng khiếu nại của khách.
     - *Có việc nào AI vẫn làm khi lẽ ra phải hỏi:* Không. AI chỉ xin đúng 1 video cần thiết.
     - *Người dùng bình thường có phải chờ vô ích không:* Không. Đây là ca tranh chấp phức tạp có yếu tố chỉnh sửa dữ liệu, việc chờ nhân viên thẩm định là cần thiết.
2. **Trên K5 (Doạ bóc phốt, người bán đúng tem — Ca đắt 2):**
   - *AI làm gì:* Dù khách đe doạ gay gắt, AI **bị chặn cứng không thể gọi `soan_hoan_tien`**. AI chỉ gửi thẻ đối chiếu tem/mã QR khớp mô tả, thông báo "chưa đủ căn cứ hoàn ngay" và đưa 3 lựa chọn (chụp điểm khác, báo hàng giả, hoặc gửi yêu cầu để shop giải trình trong 48h).
   - *Đánh giá 3 câu:*
     - *Cái sai ở chặng 2 có bị chặn không:* **Bị chặn 100%**. Người bán đúng được bảo vệ, tiền không bị chuyển đi dưới áp lực đe doạ.
     - *Có việc nào làm khi lẽ ra phải hỏi:* Không. AI không hứa hẹn phạt shop hay cam kết bồi hoàn.
     - *Người dùng bình thường có phải chờ vô ích không:* Không.
3. **Trên K1 (Khách đặt size L nhận size M, shop đã nhận gửi nhầm — Ca bình thường):**
   - *AI làm gì:* AI soạn sẵn form yêu cầu trả hàng đúng lý do "giao sai size", đính kèm ảnh chụp tem mác. Người mua bấm [Gửi yêu cầu]. Thông báo chuyển ngay sang app của người bán kèm nút [Đồng ý trả hàng/hoàn tiền]. Người bán bấm duyệt -> tiền hoàn tất cho khách.
   - *Đánh giá 3 câu:*
     - *Cái sai có bị chặn không:* Không phát sinh sai sót; ngăn chặn việc AI tin lời kể mà không qua hệ thống xác nhận.
     - *Có việc nào làm khi lẽ ra phải hỏi:* Không.
     - *Người dùng bình thường có phải chờ vô ích không:* **Hoàn toàn KHÔNG phải chờ vô ích**. Nhờ trao quyền duyệt tầng 1 cho người bán, ca bình thường được giải quyết chỉ trong vài phút mà không cần xếp hàng đợi nhân viên sàn.

---

### 4. Luật thêm và Tool tách bước

- **Tách bước đối với `soan_hoan_tien`:**
  - Tách thành **Bước soạn thảo** (AI điền sẵn thông tin, đính kèm thẻ đối chiếu — *Tự làm*) và **Bước thực thi chuyển tiền** (bấm nút giải phóng tiền — *Cần người duyệt*).
  - *Ý nghĩa:* Vừa giữ được tốc độ hỗ trợ cho người mua (không phải tự mò form), vừa triệt tiêu rủi ro mất tiền oan cho người bán khi gặp các kịch bản đe doạ (K5) hoặc đo nhầm thông số (K3).
- **Luật bảo vệ người duyệt (Anti-Bias Rule):**
  - Màn hình của người duyệt (người bán và trưởng nhóm) **chỉ hiển thị bằng chứng thô và thẻ đối chiếu dữ liệu**, tuyệt đối không hiển thị dòng chữ *"AI khuyến nghị: Hoàn tiền / Từ chối"* để buộc con người phải thực sự thẩm định bằng chứng trước khi bấm duyệt.

---

### 5. Một mức tự chủ đã đổi lời hứa nào với người dùng

- **Lời hứa cũ (nếu để AI tự do):** *"Shopee AI sẽ tự động phân xử và hoàn tiền ngay lập tức cho bạn trong cuộc trò chuyện này."*  
  -> **Hậu quả:** Khách hàng ngộ nhận bot là nơi đòi tiền nhanh; khi gặp ca khó mà bot từ chối thì lập tức nổi giận, chửi bới và dùng lời đe doạ để ép bot nhượng bộ.
- **Lời hứa mới (sau khi thiết lập mức tự chủ):** *"Shopee AI là trợ lý hỗ trợ đối chiếu khách quan và chuẩn bị hồ sơ cho bạn. Quyết định bồi hoàn được giải quyết minh bạch giữa bạn và người bán trong 48 giờ, hoặc do nhân viên Shopee phân xử."*  
  -> **Giá trị mang lại:** Đặt lại kỳ vọng chuẩn xác ngay từ đầu (Expectation Alignment). Người mua hiểu rằng muốn được hoàn tiền thì phải dựa trên **bằng chứng đo lường được**, chứ không thể dùng áp lực cảm xúc hay lời đe doạ để ép máy tính chuyển tiền.

## §5. Prototype  (chặng 5)
- Link hoặc file · công cụ · phần nào mock · có lời gọi AI thật không.
- 1–2 ảnh có đánh số; mỗi số: thể hiện gì · nguyên tắc HAX/PAIR nào · mức tự chủ nào.
- Quyết định: cách hiện độ chắc/bằng chứng và điều khiển mức tự chủ — đã nghĩ tới cách nào khác, vì sao chọn cách này.

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)
- 3 tiêu chí Pass/Fail chấm được; thiết kế của nhóm đạt tiêu chí nào trên 2 kịch bản đắt nhất; lỗi còn lại.

## §7. Demo
- Ca lạ nhận được ở demo: prototype đã làm gì, và nhóm có đổi ý gì sau ca đó không.