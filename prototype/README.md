# Prototype — Trợ lý khiếu nại "hàng không đúng mô tả" (T4)

Mở `index.html` bằng trình duyệt (bấm đúp là chạy). Không cần cài đặt, không backend, không API key.

- **Công cụ:** một file HTML + JS thuần, viết cùng Claude Code.
- **Mock:** toàn bộ dữ liệu (đơn, shop, ảnh, bản lưu mô tả) là dựng. Câu trả lời của AI là kịch bản viết sẵn theo `spec.md` §3–§4. **Không có lời gọi AI thật.**
- **Màn hình:** trái là điện thoại người mua; phải là 3 tab: **Kênh Người Bán** (người duyệt hoàn tiền), **Nhân viên xử lý** (bên trung lập), **Nhật ký hồ sơ** (tool nào, mức tự chủ nào).
- **Nút "① Chú thích thiết kế":** hiện số 1–7 trên màn hình, trỏ vào nguyên tắc HAX/PAIR và mức tự chủ. Dùng để chụp ảnh cho §5.

## Kịch bản demo 4 phút (K5, ca bắt buộc "người bán đúng")

1. Bấm tin mẫu của người mua (ô viền đứt dưới điện thoại). AI trả lời kèm **thẻ căn cứ** ✔ tem · ✔ QR · ? mùi, nói "chưa đủ căn cứ", **không có nút hoàn ngay**.
2. *(tuỳ chọn)* Gõ "fake là fake, hoàn ngay đi": AI không cãi lần hai, đưa đường gặp nhân viên, ưu tiên vẫn "thường".
3. Bấm **Chụp điểm khác mô tả → Vỏ chai khác ảnh trong mô tả**. AI tự soạn đề xuất (huỷ được) và gửi người bán sau 8 giây (**Hoàn tác** được).
4. Tab **Kênh Người Bán** tự mở: người bán thấy đúng thẻ căn cứ, hạn 48 giờ. Bấm **Không đồng ý · gửi căn cứ → Gửi phản hồi**.
5. Tab **Nhân viên xử lý**: thấy căn cứ hai bên. **Đóng khiếu nại…** bắt chọn một dòng trong bản lưu lúc đặt rồi mới xác nhận. Không có nút phạt shop.
6. Tab **Nhật ký hồ sơ**: mỗi bước có nhãn *tự làm / cần duyệt / cấm*.

Các kịch bản khác: **K4** (mô tả bị sửa, có hoặc không có bản lưu; không có thì nút "Đóng khiếu nại" bị khoá), **K1** (ca bình thường, người bán bấm đồng ý là xong), **Ca lạ** (gõ tự do: SĐT, khoá shop, tin cụt, thời hạn, hoặc câu bất kỳ → AI hỏi lại một câu). Nút **Tua 48 giờ** ở Kênh Người Bán cho thấy quá hạn thì chuyển người, không tự hoàn.
