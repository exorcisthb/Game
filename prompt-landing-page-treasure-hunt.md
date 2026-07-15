# Bổ sung: Đổi tên giữa chừng + Mật khẩu tài khoản

*Bổ sung vào luồng Landing Page (ranking realtime) → nhập tên → chơi → Result Screen.*

---

## 1. Nút "🔄 Đổi tên khác" trong lúc chơi

**Vị trí:** Trong `player-screen`, cạnh tên/rank ở thanh trên cùng — icon `🔄` hoặc chữ "Đổi tên".

**Khi bấm:**
- Nếu ván hiện tại **chưa** nhận được cúp nào (chưa tới ô Finish): hủy bàn cờ đang chơi, vị trí, `moveCount`, câu hỏi đang mở, không lưu gì.
- Tên + số cúp tích lũy trên Firebase vẫn giữ nguyên.
- Sau đó quay về màn hình nhập tên (Landing Page / modal nhập tên) để chọn hoặc tạo tên khác.

**Xác nhận:** Confirm dialog trước khi thoát.
> "Bạn có chắc muốn đổi tên? Ván đang chơi sẽ mất, nhưng cúp đã có vẫn được giữ."

---

## 2. Mật khẩu riêng cho từng tên (tên = tài khoản)

**Form nhập tên** bổ sung trường `input type="password"` ngay dưới ô tên.

**Xử lý khi bấm "Chơi ngay":**

| Trường hợp | Hành động |
|---|---|
| Tên chưa tồn tại | Tạo tài khoản mới: lưu `name` + `password` (hash) vào record, `cups=0`, `fiftyFiftyCount=0` |
| Tên đã tồn tại + đúng mật khẩu | Nạp lại `playerId`, `cups`, `fiftyFiftyCount` cũ, vào game |
| Tên đã tồn tại + sai mật khẩu | Báo lỗi: `⚠️ Sai mật khẩu cho tên này`, không vào game, không lộ thông tin |

**Cách kiểm tra:** Query `treasure_hunt_v2/players`, so khớp `name` (lowercase + trim). Nếu có → so hash mật khẩu trước khi đăng nhập.

**Auto‑login:** Nếu localStorage còn `playerId` hợp lệ, vẫn tự động vào game không cần nhập tên/mật khẩu. Mật khẩu chỉ bắt buộc khi chọn một tên đã tồn tại từ trạng thái chưa đăng nhập.

### Hash
Dùng `crypto.subtle.digest('SHA-256', ...)` hoặc thư viện nhẹ — không lưu plaintext lên Firebase. Vì không có backend thật, mức bảo mật chỉ chống nhầm lẫn, chống phá điểm giữa người chơi, không dùng cho thông tin nhạy cảm.
