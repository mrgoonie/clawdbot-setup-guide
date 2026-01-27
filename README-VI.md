# Hướng dẫn cài đặt ClawdBot + MiniMax Coding Plan

Triển khai ClawdBot trên VPS Ubuntu với MiniMax M2.1 làm backend AI.

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

# Hoặc dùng installer chính thức
curl -fsSL https://clawd.bot/install.sh | bash

# Thêm npm global bin vào PATH (nếu cần)
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 2. Cấu hình MiniMax Coding Plan

Chỉnh sửa `~/.clawdbot/clawdbot.json`:

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

3. Khởi động lại gateway:

```bash
systemctl --user restart clawdbot-gateway
```

4. Tìm bot trên Telegram và gửi `/start` hoặc tin nhắn bất kỳ

**Tùy chọn cấu hình:**

| Tùy chọn | Giá trị | Mô tả |
|----------|---------|-------|
| `botToken` | `"123:ABC..."` | Token bot từ BotFather |
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
