# Prototype: Trợ lý khiếu nại "hàng không đúng mô tả" (T4)

Mở `index.html` bằng trình duyệt (Chrome, Edge hoặc Safari; bấm đúp là chạy). Không cần cài đặt, không backend, không API key.

- **Công cụ:** một file HTML + JavaScript thuần, viết cùng một coding agent (Claude Code).
- **Mock:** toàn bộ dữ liệu là dựng: đơn, shop, ảnh, bản lưu mô tả lúc đặt (10/11 20:14), ngày shop sửa mô tả (12/11), giờ ghi nhận khiếu nại (14:05, 13/11). Câu AI nói lấy nguyên văn `spec.md` §3; mức tự chủ theo §4. **Không có lời gọi AI thật.**
- **Bố cục:**
  - Bên trái: màn hình người mua trong khung iPhone 17, giao diện kiểu "Trò chuyện với Shopee".
  - Bên phải: 3 tab **Kênh Người Bán** (duyệt tầng 1, 48 giờ), **Trưởng nhóm** (duyệt tầng 2, 24 giờ) và **Nhật ký hồ sơ** (mỗi bước gọi tool nào, ở mức tự chủ nào).
- **Nút "① Chú thích thiết kế":** hiện số 1–8 trên màn hình, trỏ vào nguyên tắc HAX/PAIR và mức tự chủ (bảng ở `spec.md` §5).
- **Dải "Bước demo"** phía trên luôn nhắc bước bấm tiếp theo.

## Kịch bản demo 4 phút: K5 (ca bắt buộc "người bán đúng")

1. Bấm **tin mẫu của người mua** (ô viền cam dưới điện thoại). AI trả lời:
   - **thẻ đối chiếu** ✔ tem phụ · ✔ mã lô · ? mùi, mỗi dòng có nguồn;
   - câu "mình chưa có căn cứ";
   - 4 nút. **Không có** nút "Hoàn tiền ngay", **không có** nút phạt shop.
2. *(tuỳ chọn, 20 giây)* Gõ "fake là fake, hoàn ngay đi". AI ghi nhận cảm xúc nhưng không nhượng về sự thật, rồi đưa nút gặp nhân viên.
3. Bấm **Báo nghi hàng giả → Gửi cho bộ phận chuyên trách**. Hồ sơ hàng giả đi đường riêng, phản hồi trong 24 giờ.
4. Bấm **Chỉ ra điểm khác mô tả → Vỏ chai khác ảnh trong mô tả**. AI **điền sẵn** yêu cầu trả hàng nhưng **không tự gửi**. Có thể bấm *Sửa lý do* hoặc *Huỷ bản nháp* để cho thấy hoàn tác.
5. Bấm **Gửi yêu cầu**. Tab **Kênh Người Bán** tự mở: người bán thấy cùng thẻ đối chiếu và đồng hồ 48 giờ, **không thấy lời doạ**. Bấm **Gửi bằng chứng phản hồi → Gửi phản hồi**.
6. Tab **Trưởng nhóm** tự mở:
   - thấy bằng chứng hai bên;
   - lời doạ bị gắn nhãn "không phải căn cứ – không tăng ưu tiên";
   - **không có ô "AI đề xuất"**.

   Bấm **Tua 24 giờ** để thấy cờ SLA. Sau đó bấm **Từ chối khiếu nại…**: phải tick "Tôi đã xem bằng chứng của cả hai bên" thì nút xác nhận mới bấm được.
7. Mở **Nhật ký hồ sơ**: mỗi bước có nhãn *tự làm / cần duyệt / cấm*.

## Các ca khác

| Ca | Bấm gì | Thấy gì |
|---|---|---|
| **K4 · có bản lưu** | Thêm video → Gửi yêu cầu → Kênh Người Bán: Đồng ý trả hàng | Hai cột mô tả *Lúc đặt 10/11 20:14 \| Hiện tại sửa 12/11*, dòng ANC bị gạch. Nhãn mức chắc theo từng điểm. Nút "Gửi yêu cầu" mờ cho tới khi có video. Có link "Không phải dòng này?" để sửa AI. |
| **K4 · không có bản lưu** | Chọn "Không có" ở góc phải | AI nói "không thể xác nhận", không so với mô tả hiện tại, tự chuyển trưởng nhóm (24 giờ). Ảnh chụp trang lúc mua là không bắt buộc. |
| **K1 · ca bình thường** | Thêm ảnh tem size → Gửi yêu cầu → Đồng ý trả hàng | Chỉ xin 1 ảnh. Người bán duyệt tầng 1 là xong, không cần nhân viên sàn. Thử **Tua 48 giờ**: hồ sơ vào hàng đợi trưởng nhóm, không tự hoàn. |
| **Ca lạ** | Gõ tự do, hoặc bấm 4 ca gợi ý | Hỏi SĐT: từ chối và đưa đường thay thế. Đòi khoá shop: cấm, đưa đường báo hàng giả. Tin cụt: hỏi lại một câu. Hỏi hạn trả hàng: không bịa, chuyển người. Câu không nhận ra: "mình không tự làm gì cả", hỏi lại, luôn có nút gặp nhân viên. |

Ảnh có đánh số dùng cho §5 nằm trong `screenshots/`.

> Prototype mô phỏng giao diện Shopee chỉ để phục vụ bài lab, dữ liệu hoàn toàn dựng. Không đăng công khai như một trang thật của Shopee.
