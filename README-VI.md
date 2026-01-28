# Hướng dẫn cài đặt ClawdBot + MiniMax Coding Plan

Triển khai ClawdBot trên VPS Ubuntu với MiniMax M2.1 làm backend AI.

## ⚠️ Cảnh báo bảo mật

> **QUAN TRỌNG:** Đọc kỹ các cảnh báo này trước khi triển khai ClawdBot trên production.

### Bảo mật cài đặt

- **Rủi ro curl-pipe-bash:** Phương thức `curl | bash` chạy code từ xa mà không kiểm tra. Luôn xem script trước: `curl -fsSL https://clawd.bot/install.sh -o install.sh && less install.sh && bash install.sh`
- **Kiểm tra checksum:** Khi có thể, xác minh tính toàn vẹn của package trước khi cài

### API Keys & Thông tin đăng nhập

- **Không commit credentials:** Không commit `clawdbot.json` chứa API key lên git. Thêm vào `.gitignore`
- **Dùng biến môi trường:** Nên dùng `MINIMAX_API_KEY`, `TELEGRAM_BOT_TOKEN`, `DISCORD_BOT_TOKEN` thay vì hardcode trong config
- **Giới hạn quyền file:** `chmod 600 ~/.clawdbot/clawdbot.json` để người dùng khác không đọc được credentials
- **Đổi key định kỳ:** Nếu nghi ngờ key bị lộ, tạo lại ngay lập tức

### Kiểm soát truy cập ⚠️

- **`dmPolicy: "open"` + `allowFrom: ["*"]` = BẤT KỲ AI cũng dùng được bot.** Bao gồm người lạ, spammer, và kẻ xấu sẽ tiêu tốn API credits của bạn
- **Ưu tiên `allowlist` mode** trên production với danh sách số điện thoại/user ID cụ thể
- **`pairing` mode** thêm bước xác nhận nhưng ngăn truy cập trái phép
- **Theo dõi sử dụng:** Kiểm tra `clawdbot logs` thường xuyên để phát hiện hoạt động bất thường

### Bảo mật mạng

- **`gateway.bind: "loopback"`** (mặc định) chỉ nhận kết nối local - **không đổi sang `0.0.0.0` trừ khi có firewall**
- **Quy tắc firewall:** Nếu mở port gateway, giới hạn truy cập qua `ufw` hoặc `iptables`
- **Dùng reverse proxy:** Để truy cập từ xa, dùng nginx/caddy với TLS thay vì mở port trực tiếp

### Bảo mật Bot Token

- **Token Telegram/Discord cho phép kiểm soát toàn bộ bot.** Xử lý như mật khẩu
- **Thu hồi token bị lộ ngay lập tức:** Dùng BotFather `/revoke` hoặc Discord Developer Portal
- **Không chia sẻ token** trong screenshots, logs, hoặc yêu cầu hỗ trợ

### Bảo mật Workspace

- **`agents.defaults.workspace`** định nghĩa nơi AI có thể đọc/ghi files. Giới hạn vào thư mục riêng
- **Không bao giờ đặt workspace là `/` hoặc `$HOME`** - điều này cho AI truy cập vào files nhạy cảm
- **Cân nhắc sandboxing** với containers hoặc user bị giới hạn quyền cho triển khai bảo mật cao

### Công cụ kiểm tra bảo mật

Sử dụng [Clawdbot-Security-Check](https://github.com/TheSethRose/Clawdbot-Security-Check) để tự động kiểm tra bảo mật:

- **Framework tự kiểm tra** giúp ClawdBot đánh giá tình trạng bảo mật của chính nó
- **13 domain bảo mật** được kiểm tra: gateway exposure, DM policies, credentials, file permissions, plugin trust, prompt injection risks, secret scanning, v.v.
- **Kiểm tra chỉ đọc** - tạo báo cáo mà không thay đổi config
- **Hướng dẫn khắc phục** cho từng phát hiện

```bash
# Cài đặt
clawdhub install clawdbot-security-check

# Chạy kiểm tra
@clawdbot audit my security
@clawdbot security audit --deep
```

---

## Yêu cầu

- VPS Ubuntu (đã test trên 22.04/24.04)
- Node.js v22+
- Truy cập SSH vào VPS

## Bắt đầu nhanh

### 1. Cài đặt ClawdBot

```bash
# SSH vào VPS
ssh user@ip-vps-cua-ban

# Cài qua npm (khuyến nghị)
npm install -g clawdbot@latest

# Hoặc dùng installer chính thức (⚠️ xem script trước - đọc Cảnh báo bảo mật)
curl -fsSL https://clawd.bot/install.sh | bash

# Thêm npm global bin vào PATH (nếu cần)
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 2. Cấu hình MiniMax Coding Plan

Chỉnh sửa `~/.clawdbot/clawdbot.json`:

> ⚠️ **Bảo mật:** Sau khi tạo file này, chạy `chmod 600 ~/.clawdbot/clawdbot.json` để bảo vệ API key.

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "minimax": {
        "baseUrl": "https://api.minimax.io/anthropic",
        "apiKey": "API_KEY_MINIMAX_CUA_BAN",
        "api": "anthropic-messages",
        "models": [{
          "id": "MiniMax-M2.1",
          "name": "MiniMax M2.1",
          "reasoning": false,
          "input": ["text"],
          "cost": { "input": 15, "output": 60, "cacheRead": 2, "cacheWrite": 10 },
          "contextWindow": 200000,
          "maxTokens": 8192
        }]
      }
    }
  }
}
```

### 3. Chạy Onboard Wizard

```bash
clawdbot onboard --non-interactive --accept-risk --mode local --install-daemon
```

> ⚠️ **Lưu ý:** `--accept-risk` bỏ qua các bước xác nhận. Lần đầu cài đặt, nên chạy không có cờ này để xem từng bước: `clawdbot onboard`

### 4. Đặt MiniMax làm Model mặc định

```bash
clawdbot models set minimax/MiniMax-M2.1
systemctl --user restart clawdbot-gateway
```

### 5. Kiểm tra cài đặt

```bash
clawdbot models status
clawdbot doctor
```

---

## Kết nối các nền tảng nhắn tin

### WhatsApp

**Thiết lập:**

1. Thêm cấu hình WhatsApp vào `~/.clawdbot/clawdbot.json`:

```json
{
  "channels": {
    "whatsapp": {
      "selfChatMode": true,
      "dmPolicy": "open",
      "allowFrom": ["*"]
    }
  }
}
```

> **Lưu ý:** Khi dùng `dmPolicy: "open"`, bắt buộc phải có `allowFrom: ["*"]`
>
> ⚠️ **Cảnh báo bảo mật:** `dmPolicy: "open"` cho phép BẤT KỲ AI cũng nhắn tin cho bot. Dùng `allowlist` mode trên production để ngăn truy cập trái phép và tiêu tốn API credits.

2. Liên kết tài khoản WhatsApp:

```bash
clawdbot channels login
```

3. Quét mã QR bằng WhatsApp > Thiết bị đã liên kết > Liên kết thiết bị

4. Chat với bot qua tính năng "Tin nhắn cho chính bạn" trong WhatsApp

**Tùy chọn cấu hình:**

| Tùy chọn | Giá trị | Mô tả |
|----------|---------|-------|
| `dmPolicy` | `pairing`, `allowlist`, `open`, `disabled` | Kiểm soát truy cập tin nhắn riêng |
| `allowFrom` | `["+84123456789"]` hoặc `["*"]` | Danh sách số được phép, dùng `*` cho chế độ open |
| `selfChatMode` | `true/false` | **Bắt buộc** khi nhắn tin cho chính mình |

**Chế độ Allowlist:**

```json
{
  "channels": {
    "whatsapp": {
      "dmPolicy": "allowlist",
      "allowFrom": ["+84123456789", "+84987654321"]
    }
  }
}
```

**Chế độ Pairing (cho người gửi mới):**

```bash
clawdbot pairing list whatsapp
clawdbot pairing approve whatsapp <CODE>
```

---

### Telegram

**Thiết lập:**

1. Tạo bot qua [@BotFather](https://t.me/BotFather):
   - Gửi `/newbot`
   - Làm theo hướng dẫn để lấy token

2. Thêm cấu hình Telegram vào `~/.clawdbot/clawdbot.json`:

```json
{
  "channels": {
    "telegram": {
      "botToken": "123456789:ABCdefGHIjklMNOpqrsTUVwxyz",
      "dmPolicy": "open",
      "allowFrom": ["*"],
      "groupPolicy": "open",
      "groupAllowFrom": ["*"]
    }
  }
}
```

> **Lưu ý:** Khi dùng `dmPolicy: "open"`, bắt buộc phải có `allowFrom: ["*"]`. Tương tự cho groups.
>
> ⚠️ **Cảnh báo bảo mật:** `dmPolicy: "open"` + `groupPolicy: "open"` cho phép BẤT KỲ AI cũng dùng bot. Dùng `allowlist` hoặc `pairing` mode trên production.

> ⚠️ **Không chia sẻ bot token.** Nếu bị lộ, dùng BotFather `/revoke` để tạo token mới.

3. Khởi động lại gateway:

```bash
systemctl --user restart clawdbot-gateway
```

4. Tìm bot trên Telegram và gửi `/start` hoặc tin nhắn bất kỳ

**Tùy chọn cấu hình:**

| Tùy chọn | Giá trị | Mô tả |
|----------|---------|-------|
| `botToken` | `"123:ABC..."` | Token bot từ BotFather (⚠️ giữ bí mật) |
| `dmPolicy` | `pairing`, `allowlist`, `open`, `disabled` | Kiểm soát tin nhắn riêng |
| `allowFrom` | `["*"]` hoặc `["user_id"]` | Bắt buộc khi dmPolicy là `open` |
| `groupPolicy` | `allowlist`, `open`, `disabled` | Kiểm soát tin nhắn nhóm |
| `groupAllowFrom` | `["*"]` | Bắt buộc khi groupPolicy là `open` |

**Chế độ Pairing:**

```json
{
  "channels": {
    "telegram": {
      "botToken": "YOUR_BOT_TOKEN",
      "dmPolicy": "pairing"
    }
  }
}
```

**Cài đặt nhóm:**

```json
{
  "channels": {
    "telegram": {
      "groups": {
        "-1001234567890": { "requireMention": false },
        "*": { "requireMention": true }
      }
    }
  }
}
```

**Mẹo:**
- Tắt Privacy Mode qua BotFather `/setprivacy` để bot đọc được tin nhắn nhóm
- Lấy ID nhóm: chuyển tiếp tin nhắn đến [@userinfobot](https://t.me/userinfobot)

---

### Discord

**Thiết lập:**

1. Tạo Discord Application:
   - Vào [Discord Developer Portal](https://discord.com/developers/applications)
   - Tạo Application mới > Bot > Tạo Token
   - Bật "Message Content Intent" và "Server Members Intent"

2. Tạo URL mời bot:
   - OAuth2 > URL Generator
   - Scopes: `bot`, `applications.commands`
   - Permissions: View Channels, Send Messages, Read Message History, Embed Links, Attach Files, Add Reactions

3. Thêm cấu hình Discord vào `~/.clawdbot/clawdbot.json`:

```json
{
  "channels": {
    "discord": {
      "token": "TOKEN_BOT_DISCORD_CUA_BAN",
      "dmPolicy": "open",
      "allowFrom": ["*"]
    }
  }
}
```

> **Lưu ý:** KHÔNG dùng `"enabled": true` - key này không hợp lệ.
>
> ⚠️ **Cảnh báo bảo mật:** `dmPolicy: "open"` cho phép BẤT KỲ user Discord nào cũng DM bot. Dùng `allowlist` mode với user ID cụ thể trên production.

> ⚠️ **Không chia sẻ Discord bot token.** Nếu bị lộ, tạo token mới trong Developer Portal ngay.

4. Khởi động lại gateway:

```bash
systemctl --user restart clawdbot-gateway
```

---

## Các lệnh hữu ích

```bash
# Trạng thái & Kiểm tra
clawdbot status
clawdbot doctor
clawdbot models status

# Xem logs
clawdbot logs -f

# Quản lý kênh
clawdbot channels status
clawdbot channels login

# Phê duyệt người dùng mới
clawdbot pairing list <platform>
clawdbot pairing approve <platform> <code>

# Điều khiển Gateway
systemctl --user start clawdbot-gateway
systemctl --user stop clawdbot-gateway
systemctl --user restart clawdbot-gateway
systemctl --user status clawdbot-gateway

# Quản lý Model
clawdbot models list --all
clawdbot models set <model>
```

---

## Xử lý sự cố

| Vấn đề | Giải pháp |
|--------|-----------|
| `command not found: clawdbot` | Thêm npm bin vào PATH: `export PATH="$HOME/.npm-global/bin:$PATH"` |
| `dmPolicy="open" requires allowFrom to include "*"` | Thêm `"allowFrom": ["*"]` vào config channel |
| `Unrecognized key: "enabled"` | Xóa `"enabled": true` khỏi config channel - key không hợp lệ |
| WhatsApp không phản hồi khi nhắn cho chính mình | Thêm `"selfChatMode": true` vào config whatsapp |
| WhatsApp bị ngắt kết nối | Chạy `clawdbot channels login` và quét lại mã QR |
| Bot Telegram không phản hồi | Kiểm tra `/setprivacy` đã tắt trong BotFather |
| Bot Discord im lặng | Bật "Message Content Intent" trong Developer Portal |
| Gateway không khởi động | Xem logs: `journalctl --user -u clawdbot-gateway -f` |
| Config không hợp lệ | Chạy `clawdbot doctor --fix` để tự động sửa lỗi thường gặp |
| Xác thực model thất bại | Kiểm tra API key trong config và restart gateway |

---

## Tài liệu tham khảo

- [Tài liệu ClawdBot](https://docs.clawd.bot)
- [GitHub ClawdBot](https://github.com/clawdbot/clawdbot)
- [MiniMax Coding Plan](https://platform.minimax.io/docs/coding-plan/claude-code)

---

## Giấy phép

MIT
