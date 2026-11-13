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
- ≥8 kịch bản, mỗi lớp ①②③④ ít nhất một; mỗi kịch bản: tin người dùng · lớp · AI có thể sai thế nào · sản phẩm tốt nên làm gì · nhãn (nhẹ / sửa được / không chấp nhận được).
- Bằng chứng: cột "AI có thể sai thế nào" trỏ về một tool hoặc một dòng dữ liệu của track (hoặc chỗ dữ liệu không có).
- Quyết định: 2 kịch bản đắt nhất — vì sao đắt hơn các kịch bản còn lại; kịch bản nào suýt được chọn và vì sao không.

## §3. Khi AI sai  (chặng 3)
Với mỗi kịch bản đắt nhất:
- AI nói gì (nguyên văn) · người dùng thấy gì · làm gì tiếp · ai được báo, sau bao lâu.
- Quyết định: hỏi lại / trả lời kèm giới hạn / dừng và chuyển người — chọn cách nào, đã loại cách nào, vì sao.
- Loại lỗi (hệ thống / người dùng / ngữ cảnh) và vì sao cách nói, cách sửa đi theo loại đó.
- 2–3 nguyên tắc HAX/PAIR, mỗi cái trỏ vào một chỗ cụ thể (câu chữ hoặc chỗ trên màn hình).
- Lỗi nào sửa câu chữ không cứu được (nếu có), và thiết kế cứu nó thế nào.

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