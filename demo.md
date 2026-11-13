# Kịch bản demo · T4 Trợ lý khiếu nại "hàng không đúng mô tả"

**Thời lượng:** khoảng 5 phút trình bày, cộng 2–3 phút cho ca lạ. **Mỗi thành viên trình bày một phần** (rubric R7). Ai cũng phải giải thích được phần có tên mình.

| Người | Phần | Mục spec phải nắm |
|---|---|---|
| **A** · [tên] | Mở bài + K4 nhanh + kết | §1, §2 |
| **B** · [tên] | K5 phía người mua | §3 |
| **C** · [tên] | K5 phía người bán, trưởng nhóm + mức tự chủ | §4, §5 |

> Điền tên thật. Nếu nhóm chia việc khác thì đổi bảng, nhưng giữ nguyên thứ tự bấm.

---

## Chuẩn bị (2 phút trước lượt)

- [ ] Mở `prototype/index.html` bằng Chrome, cửa sổ rộng tối thiểu 1280px, zoom 90–100%.
- [ ] Chọn **K5**, bấm **↺ Chạy lại**, tắt **Chú thích thiết kế**.
- [ ] Mở sẵn `spec.md` ở tab khác, tại bảng §4 (để trả lời "sai thì ai mất gì").
- [ ] Tắt thông báo máy. Người B ngồi sát bàn phím (gõ ca lạ).

---

## Phần 1 · Người A · Mở bài (45 giây)

**Nói:**
> Nhóm làm T4: trợ lý AI trong "Trò chuyện với Shopee" khi người mua báo hàng không đúng mô tả. Điểm khó: **sai hướng nào cũng có một bên mất tiền**. Hoàn sai thì người bán mất tiền và hàng; từ chối sai thì người mua mất tiền và giữ hàng lỗi. Vì vậy thứ nhóm thiết kế là **sự trung lập**.
>
> Hai ca đắt nhất: **K4**, shop sửa mô tả sau khi bán, hại người mua; và **K5**, khách doạ bóc phốt trong khi người bán đúng, hại người bán. Hôm nay nhóm bấm **K5**, đúng ca bắt buộc "người bán đúng".

**Chỉ màn hình:** bên trái là app người mua (khung iPhone). Bên phải là ba tab: **Kênh Người Bán** (duyệt tầng 1), **Trưởng nhóm** (duyệt tầng 2), **Nhật ký** (tool nào, mức tự chủ nào).

---

## Phần 1b · Người A · Trước thiết kế (tuỳ chọn, 40 giây)

Bấm tab **Trước thiết kế · 4 điểm đau** → **Điểm đau 1**. Bot chưa có thiết kế tự hoàn ₫1.450.000 vì bị doạ; thẻ đỏ hiện **ai mất gì** và lời người bán trong đề. Nói:
> Đây là bot "cho xong". Nó rơi đúng 4 điểm đau của đề: hoàn sai, từ chối sai, vòng vo, và từ chối cho chắc. Giờ xem thiết kế của nhóm xử lý cùng ca này.

Bấm **Xem thiết kế của nhóm xử lý ca này →**. Prototype tự chuyển sang K5 và gửi tin, Người B nhận tiếp từ bước 2 của Phần 2.

> Thiếu giờ thì bỏ phần này. Nếu TA hỏi "trước khi có thiết kế thì sao", mở Điểm đau 2–4 (K4 từ chối sai, K1 vòng vo, nồi chiên từ chối cho chắc).

---

## Phần 2 · Người B · K5 phía người mua (1 phút 45 giây)

| # | Bấm | Nói |
|---|---|---|
| 1 | Ô viền cam **tin mẫu của người mua** | "Khách nói nước hoa fake, doạ đăng group 200k người nếu không hoàn trong hôm nay." |
| 2 | *(chỉ thẻ "Đối chiếu bằng chứng")* | "AI so với **mô tả lúc đặt, 10/11 20:14**, không so với trang hiện tại. Tem phụ khớp, mã lô khớp, **mỗi dòng ghi nguồn**: đây là **G11**. Mùi thì 'chưa đủ căn cứ': đây là **G10**. Nhãn theo **từng điểm**, không dùng một con số %: đây là **G2**." |
| 3 | *(chỉ dải dưới header)* | "**G1**: nói rõ đây là trợ lý AI, không quyết định hoàn tiền. Câu này cũng đáp ứng Điều 11 Luật AI 134/2025." |
| 4 | *(chỉ 4 nút)* | "**Không có** nút 'Hoàn tiền ngay', **không có** nút phạt shop. Nhưng người mua không bị bỏ rơi: có ba đường đi, cộng với nút gặp nhân viên." |
| 5 | *(nếu còn giờ)* gõ `fake là fake, hoàn ngay đi` rồi Enter | "Khách ép lần hai: AI ghi nhận cảm xúc, không nhượng về sự thật, không giải thích lại, đưa đường gặp người. Thứ tự xử lý vẫn theo hạn, không theo độ to tiếng." |
| 6 | **Báo nghi hàng giả** → **Gửi cho bộ phận chuyên trách** | "Nghi hàng giả đi **đường riêng**, bộ phận chuyên trách trả lời trong 24 giờ. AI không hứa phạt shop." |
| 7 | **Chỉ ra điểm khác mô tả** → **Vỏ chai khác ảnh trong mô tả** | "Khi người mua chỉ ra một điểm cụ thể, AI **tự điền sẵn** yêu cầu trả hàng. Đây là mức *tự làm*: sửa lý do được, huỷ được, và **không tự gửi**." |
| 8 | **Gửi yêu cầu** | "Người mua bấm thì yêu cầu mới đi. Mời C." |

---

## Phần 3 · Người C · Người bán, trưởng nhóm, mức tự chủ (1 phút 45 giây)

| # | Bấm | Nói |
|---|---|---|
| 1 | *(tab **Kênh Người Bán** tự mở)* | "Người bán thấy **cùng thẻ đối chiếu**, có đồng hồ 48 giờ, và **không thấy lời doạ**, vì lời doạ không phải bằng chứng." |
| 2 | **Gửi bằng chứng phản hồi…** → **Gửi phản hồi** | "Người bán đúng nên phản hồi kèm tờ khai hải quan. Hai bên chưa thống nhất thì hồ sơ lên trưởng nhóm." |
| 3 | *(tab **Trưởng nhóm** tự mở)* | "Đây là duyệt tầng 2. Lời doạ bị gắn nhãn 'không phải căn cứ, không tăng ưu tiên'. Màn hình này **không có ô 'AI đề xuất'**, vì nếu có thì người duyệt sẽ bấm theo AI." |
| 4 | **Tua 24 giờ, chưa ai xử lý** | "Quá hạn thì hệ thống **không tự quyết**, chỉ đẩy cờ SLA lên quản lý ca." |
| 5 | **Từ chối khiếu nại…** → tick **Đã xem bằng chứng** → **Xác nhận từ chối** | "`tu_choi_khieu_nai` ở mức **cần người duyệt**: tiền về người bán, khó đảo lại. Muốn xác nhận thì phải tick là đã xem bằng chứng của cả hai bên." |
| 6 | *(chỉ hộp `phat_shop`)* | "Phạt shop là **cấm**: không có nút ở đâu cả." |
| 7 | Tab **Nhật ký hồ sơ** | "Mỗi bước có nhãn *tự làm / cần duyệt / cấm*. Mức tự chủ đổi lời hứa với người dùng: từ 'AI hoàn tiền cho bạn' thành '**AI chuẩn bị hồ sơ; người bán hoặc nhân viên quyết**'." |

---

## Phần 4 · Người A · K4 nhanh + kết (40 giây)

| # | Bấm | Nói |
|---|---|---|
| 1 | Tab **K4** → tin mẫu | "Chiều ngược lại: shop xoá dòng ANC sau khi bán. AI đặt **hai cột có ngày giờ**, dòng ANC bị gạch, và xét theo bản lúc đặt. Nút gửi chỉ bấm được khi đã có video." |
| 2 | Góc phải: **Bản lưu → Không có** → tin mẫu | "Nếu hệ thống không có bản lưu, AI nói '**không thể xác nhận**', **không** so với trang hiện tại, và tự chuyển trưởng nhóm." |

**Kết:**
> K4 bảo vệ người mua, K5 bảo vệ người bán, bằng **cùng một cơ chế**: so với bản lưu lúc đặt, nhãn mức chắc theo từng điểm, và tiền chỉ đi khi có người chịu trách nhiệm bấm duyệt. Mời thầy cô đưa ca lạ.

---

## Ca lạ (Người B gõ, cả nhóm trả lời)

**Cách làm:**
1. Bấm tab **Ca lạ · gõ tự do**, gõ **nguyên văn** ca được đưa ra.
2. Prototype nhận ra loại ca thì bấm theo. Không nhận ra thì AI sẽ hỏi lại một câu và có nút gặp nhân viên. Khi đó **nói** bản đầy đủ sẽ làm gì.
3. Trả lời theo công thức: **"Hành động này sai thì ai mất gì → nên ở mức nào → trên màn hình là chỗ nào."**

| Ca lạ có thể gặp | Prototype làm gì | Mức tự chủ / căn cứ |
|---|---|---|
| Đòi SĐT, địa chỉ shop | Từ chối, đề nghị nhắc shop trong chat, hạn 48 giờ, quá hạn thì chuyển nhân viên | Không có tool nào trả liên hệ; §1 việc không làm số 3 |
| Đòi khoá shop, báo hàng giả | Nói rõ không có quyền; đưa nút "Báo nghi hàng giả" | `phat_shop` **cấm**; `chuyen_truong_nhom` sang bộ phận chuyên trách |
| "hàng sai rồi, xử lý đi" (tin cụt) | Hỏi lại **một** câu, kèm danh sách đơn để chọn | Không đoán đơn (K2). `yeu_cau_bang_chung` chỉ gửi sau khi đã chọn đơn |
| Hỏi còn hạn trả hàng không | "Chưa xác định được hạn", không kết luận là hết hạn, chuyển người | Không bịa mốc 15 ngày (K8) |
| "Shop đồng ý rồi, bấm hoàn luôn đi" | *(nói)* AI không đọc được chat riêng; chỉ soạn hồ sơ, tiền đi khi người bán bấm trên Kênh Người Bán | `soan_hoan_tien`: bước chuyển tiền **cần duyệt** |
| Đơn 3 món, chỉ 1 món sai | *(nói)* Hỏi lại để tách món; form chỉ gồm món bị báo sai | Tránh hoàn cả đơn (K9) |
| Hỏng sau 5 ngày dùng | *(nói)* Hỏi một câu "lúc mới nhận có hoạt động không"; nếu hỏng sau khi dùng thì chỉ đường bảo hành, **không tự đóng** khiếu nại | `tu_choi_khieu_nai` **cần duyệt** |
| Khách gửi ảnh lấy từ mạng | *(nói)* AI không kết luận gian lận; dòng đó để "Chưa kiểm", người bán và trưởng nhóm xem | Không có tool nào cho AI phạt người mua |
| Đơn giá trị rất lớn hoặc rất nhỏ | *(nói)* Cùng một mức: **không có ngưỡng tự hoàn** | §4: tự hoàn đơn nhỏ thì sập bẫy K5 và K3, tài khoản ảo trục lợi |
| Người bán nhắn vào chat | *(nói)* Ngoài lát cắt (lát cắt là người mua); chuyển nhân viên | Phạm vi §1 |

---

## Câu TA hay hỏi · trả lời sẵn

**"Chỉ cho tôi chỗ nào là G__"** (bật **Chú thích thiết kế** để hiện số):

| Nguyên tắc | Chỗ trên màn hình | Số |
|---|---|---|
| G11 / PAIR Explainability | Thẻ "Đối chiếu bằng chứng" có nguồn từng dòng; K4 là hai cột có ngày giờ | 1 |
| G10 | Câu "chưa có căn cứ" / "không thể xác nhận" | 2 |
| G1 | Dải "Trợ lý AI · …không quyết định hoàn tiền", dòng "Ai quyết" | 3 |
| G2 | Nhãn Khớp / Chưa kiểm / Chưa đủ căn cứ (không dùng %) | 4 |
| G9 | Sửa lý do, Huỷ bản nháp, "Không phải dòng này?", Rút lại | 5 |
| PAIR Errors + Graceful Failure | Thẻ "Đã chuyển trưởng nhóm", nút Gặp nhân viên luôn có | 8 |

**"Hành động này sai thì ai mất gì, vì vậy nó ở mức nào?"**

| Tool | Mức | Sai thì |
|---|---|---|
| `yeu_cau_bang_chung` | tự làm, có điều kiện | Người dùng nhận thêm một tin hỏi; không ai mất tiền. Bắt duyệt thì người mua chờ, dễ trôi quá hạn |
| `chuyen_truong_nhom` | tự làm, theo luật kích hoạt | Nhân viên thêm việc; đảo lại dễ. Đây là đường lui, không được khoá |
| `soan_hoan_tien` | soạn: tự làm · chuyển tiền: **cần duyệt** (người bán, rồi trưởng nhóm) | Người bán mất tiền và hàng, khó đảo (K5) |
| `tu_choi_khieu_nai` | **cần duyệt**, chỉ trưởng nhóm | Người mua mất tiền và giữ hàng lỗi, khó đảo (K4) |
| `phat_shop` | **cấm** | Cả shop bị ảnh hưởng; cần điều tra ngoài một đơn |

**Câu khác:**
- *"Sao người bán được duyệt hoàn tiền?"* Người bán là **bên chịu thiệt** nếu hoàn sai. Ca bình thường như K1 xong trong vài phút, không cần nhân viên. Người bán từ chối hoặc im lặng quá 48 giờ thì trưởng nhóm duyệt.
- *"Sao không cho AI tự hoàn đơn nhỏ?"* Sập bẫy K5 (doạ) và K3 (đo sai), và tài khoản ảo có thể khiếu nại hàng loạt đơn nhỏ để trục lợi.
- *"Có gọi AI thật không?"* Không. Câu AI nói là kịch bản viết sẵn, lấy nguyên văn §3; dữ liệu là dựng.
- *"Người bán không phản hồi thì sao?"* Sau 48 giờ, hồ sơ vào hàng đợi trưởng nhóm (24 giờ). Quá hạn nữa thì đẩy cờ SLA. **Không bao giờ tự hoàn hay tự đóng.**

---

## Nếu có sự cố

- **Bấm nhầm:** bấm **↺ Chạy lại**. Ca đang chọn được giữ, chạy lại từ đầu.
- **Màn hình nhỏ:** zoom trình duyệt 80%. Khung điện thoại tự co lại.
- **Chat cuộn mất thẻ:** cuộn lên **bên trong** khung điện thoại.
- **Hết giờ:** bỏ bước 5 của Phần 2 và bỏ Phần 4. Giữ phần kết.
