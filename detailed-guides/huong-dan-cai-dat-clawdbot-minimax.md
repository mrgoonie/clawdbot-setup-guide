# Hướng Dẫn Cài Đặt ClawdBot với MiniMax M2.1: Xây Dựng AI Agent Chạy 24/7 Trên Máy Tính Của Bạn

Bạn đã bao giờ mơ về một AI assistant sống ngay trên máy tính, luôn sẵn sàng trả lời qua Telegram hay WhatsApp bất kỳ lúc nào?

Không phải chatbot web mà mỗi lần dùng phải mở trình duyệt. Không phải ứng dụng mà sau vài giờ sẽ quên hết ngữ cảnh. Mà là một **AI agent thực sự** - có bộ nhớ dài hạn, chạy 24/7 trên máy tính của bạn, và bạn có thể nhắn tin cho nó như nhắn tin cho bạn bè.

**Đó chính xác là những gì ClawdBot + MiniMax M2.1 mang lại.**

Bài viết này hướng dẫn chi tiết từng bước cài đặt ClawdBot với mô hình MiniMax M2.1 - kết hợp được khuyến nghị bởi chính tác giả Peter Steinberger. Sau 10 phút, bạn sẽ có một AI agent riêng, sẵn sàng phục vụ qua Telegram.

---

## ClawdBot Là Gì? Tại Sao Chọn MiniMax M2.1?

### ClawdBot: AI Agent Sống Trên Máy Tính

ClawdBot không đơn thuần là chatbot. Đây là một **AI agent framework** cho phép bạn:

- **Chạy AI 24/7** trên máy tính cá nhân
- **Bộ nhớ dài hạn** - nhớ các cuộc hội thoại trước đó
- **Giao tiếp đa kênh** - Telegram, WhatsApp, Discord, Slack, Signal...
- **Mở rộng bằng skills** - tích hợp 1Password, Apple Notes, Obsidian, và nhiều hơn nữa
- **Tự tạo skills mới** - yêu cầu ClawdBot viết skill cho chính nó

### Tại Sao MiniMax M2.1?

Peter Steinberger - tác giả ClawdBot - [khuyến nghị sử dụng MiniMax M2.1](https://x.com/steipete/status/170107016598849759) vì:

- **Chi phí thấp** - Gói Starter chỉ $10/tháng với 100 prompts/5 giờ
- **Chất lượng cao** - Mô hình M2.1 cạnh tranh với GPT-4 và Claude
- **Text-to-speech hiện đại** - Có thể mở rộng để bot gọi điện hoặc gửi tin nhắn thoại

---

## Yêu Cầu Trước Khi Bắt Đầu

Trước khi cài đặt, đảm bảo bạn có:

| Yêu cầu | Mô tả |
|---------|-------|
| **macOS/Linux** | ClawdBot chạy trên Unix-based systems |
| **Terminal** | Truy cập command line |
| **Tài khoản MiniMax** | Đăng ký tại [minimax.io](https://minimax.io) |
| **Tài khoản Telegram** | Để tạo bot và nhận tin nhắn |
| **Kết nối internet** | Cho quá trình cài đặt và API calls |

---

## Bước 1: Cài Đặt ClawdBot

### 1.1 Chạy Lệnh Cài Đặt

Mở Terminal và chạy lệnh sau:

```bash
curl -fSsl https://clawd.bot/install.sh | bash
```

Installer sẽ tự động tải và khởi động wizard cài đặt.

### 1.2 Xác Nhận Bảo Mật

ClawdBot là một hệ thống mạnh mẽ với quyền truy cập vào máy tính của bạn. Installer sẽ hỏi:

```
Security
I understand this is powerful and inherently risky. Continue?
> Yes / No
```

Chọn **Yes** để tiếp tục.

### 1.3 Chọn Chế Độ Cài Đặt

```
Onboarding mode
> Quickstart
  Advanced
```

Chọn **Quickstart** - đủ cho hầu hết người dùng và nhanh hơn nhiều.

---

## Bước 2: Cấu Hình MiniMax M2.1

### 2.1 Chọn Model Provider

Khi installer hiển thị danh sách providers:

```
Model/auth provider
  OpenAI
  Anthropic
> MiniMax (M2.1 recommended)
  Cohere
  Google
  ...
```

Chọn **MiniMax (M2.1 recommended)**.

### 2.2 Chọn Phương Thức Xác Thực

```
MiniMax auth method
> MiniMax M2.1
  MiniMax M2.1 Lightning (faster, higher output cost)
```

Chọn **MiniMax M2.1** - cân bằng giữa tốc độ và chi phí.

### 2.3 Lấy API Key Từ MiniMax

**Bước 2.3.1:** Truy cập [platform.minimaxi.io/subscribe/coding-plan](https://platform.minimaxi.io/subscribe/coding-plan)

**Bước 2.3.2:** Chọn gói phù hợp:

| Gói | Giá | Prompts/5 giờ |
|-----|-----|---------------|
| **Starter** | $10/tháng | 100 |
| Plus | $25/tháng | 300 |
| Max | $50/tháng | Unlimited |

**Gói Starter đủ dùng** cho hầu hết người dùng cá nhân.

**Bước 2.3.3:** Cuộn xuống phần **API Key**, nhấn **Reset & Copy**

**Bước 2.3.4:** Popup hiển thị secret key (dạng `sk-cp-Mupz6h3JJ130I78QNJ-vRTDXXpbwc0FvIVkM5Gh_eC64I`)

**Bước 2.3.5:** Nhấn **Confirm** và dán key vào Terminal:

```
Enter MiniMax API Key
> [dán key và nhấn Enter]
```

### 2.4 Xác Nhận Model Mặc Định

```
Default model
> Keep current (minimax/MiniMax M2.1)
```

Chọn **Keep current** để sử dụng M2.1.

---

## Bước 3: Thiết Lập Telegram Bot

### 3.1 Chọn Kênh Giao Tiếp

```
Select channel (Quickstart)
> Telegram (Bot API)
  WhatsApp
  Discord
  Slack
  Signal
  ...
```

Chọn **Telegram (Bot API)** - dễ thiết lập nhất.

### 3.2 Tạo Bot Trên Telegram

**Bước 3.2.1:** Truy cập [web.telegram.org](https://web.telegram.org/k/)

**Bước 3.2.2:** Tìm kiếm và mở chat với **@BotFather**

**Bước 3.2.3:** Gửi lệnh:

```
/newbot
```

**Bước 3.2.4:** BotFather hỏi tên bot - nhập tên bạn muốn:

```
MIST
```

**Bước 3.2.5:** BotFather yêu cầu username (phải kết thúc bằng `bot` hoặc `_bot`):

```
my_clawd_bot
```

**Bước 3.2.6:** BotFather gửi token dạng:

```
6510928366:AAEHj9dMUFLMRA-ZSSMjYHmCwYICy
```

### 3.3 Nhập Bot Token

Sao chép token và dán vào Terminal:

```
Enter Telegram bot token
> [dán token và nhấn Enter]
```

### 3.4 (Tùy Chọn) Nhập Telegram User ID

Nếu installer yêu cầu Telegram User ID:

1. Mở Telegram và tìm **@userinfobot**
2. Gửi bất kỳ tin nhắn nào
3. Bot sẽ trả về User ID của bạn
4. Nhập ID vào Terminal

---

## Bước 4: Cấu Hình Skills

### 4.1 Chọn Skills Cần Thiết

```
Configure skills now? (recommended)
> Yes
```

Chọn **Yes** và đánh dấu các skills bạn muốn:

| Skill | Mô tả |
|-------|-------|
| `1password` | Tích hợp quản lý mật khẩu |
| `apple-notes` | Đọc/ghi Apple Notes |
| `apple-reminders` | Quản lý Reminders |
| `obsidian` | Tích hợp Obsidian vault |
| `camsnap` | Chụp ảnh từ webcam |
| `summarize` | Tóm tắt văn bản dài |

**Mẹo:** Bạn có thể thêm skills sau này, hoặc yêu cầu ClawdBot tự tạo skills mới!

### 4.2 Bỏ Qua API Keys Bổ Sung

Installer sẽ hỏi về các API keys khác (Google Maps, OpenAI, ElevenLabs...).

Chọn **No** cho tất cả nếu bạn chưa cần - có thể thêm sau.

---

## Bước 5: Kích Hoạt Hooks và Khởi Động

### 5.1 Bật Hooks

```
Enable hooks?
> command-logger
> session-memory
```

Chọn cả hai:
- **command-logger**: Ghi log các lệnh thực thi
- **session-memory**: Bộ nhớ phiên làm việc

### 5.2 Khởi Động Giao Diện

```
Start TUI (Best option!)
> Yes
```

Chọn **Yes** để khởi động bot.

---

## Bước 6: Kiểm Tra ClawdBot

### 6.1 Kiểm Tra Qua Web UI

Trình duyệt tự động mở Control UI tại:

```
http://127.0.0.1:18709
```

Trong tab **Chat**, gõ:

```
Hello
```

Bot sẽ trả lời:

```
Hey! Good to see you! What can I help you with today?
```

### 6.2 Kiểm Tra Qua Telegram

1. Mở Telegram
2. Tìm bot bạn vừa tạo (username bạn đặt ở Bước 3.2.5)
3. Nhấn **START**
4. Gửi tin nhắn bất kỳ

Bot sẽ phản hồi - xác nhận mọi thứ hoạt động!

---

## Mở Rộng: Tính Năng Nâng Cao

ClawdBot hỗ trợ nhiều tính năng nâng cao:

### Text-to-Speech

MiniMax có các mô hình text-to-speech hiện đại. Bạn có thể:
- Thiết lập bot gửi tin nhắn thoại
- Cho phép gọi điện trực tiếp đến bot
- Nhận voice responses thay vì text

### Tích Hợp Đa Kênh

Sau khi cài đặt Telegram, bạn có thể thêm:
- WhatsApp
- Discord
- Slack
- Signal
- Matrix

### Tự Tạo Skills

ClawdBot có khả năng tự tạo skills. Yêu cầu đơn giản như:

```
Create a skill to check my Gmail inbox
```

Bot sẽ tự viết và cài đặt skill mới.

---

## Troubleshooting

### Bot Không Phản Hồi Trên Telegram

1. Kiểm tra Control UI tại `http://127.0.0.1:18709` xem bot có chạy không
2. Xác nhận bot token đúng
3. Đảm bảo đã nhấn START trong Telegram

### Lỗi API Key

1. Truy cập [MiniMax Platform](https://platform.minimaxi.io)
2. Kiểm tra gói subscription còn hiệu lực
3. Reset và copy lại API key

### Terminal Đóng - Bot Dừng

ClawdBot cần Terminal mở để chạy. Để chạy nền:

```bash
# Sử dụng screen hoặc tmux
screen -S clawdbot
# Khởi động ClawdBot, sau đó Ctrl+A, D để detach
```

---

## Tổng Kết

Bạn vừa thiết lập thành công một AI agent chạy 24/7 trên máy tính với:

- **MiniMax M2.1** - mô hình AI chất lượng cao, chi phí thấp
- **Telegram integration** - nhắn tin cho bot như bạn bè
- **Bộ nhớ dài hạn** - bot nhớ các cuộc hội thoại trước
- **Mở rộng với skills** - tích hợp với apps và services khác

Khác biệt cốt lõi so với các chatbot thông thường: **ClawdBot sống trên máy tính của bạn, không phải trên cloud của ai đó**. Dữ liệu của bạn, quyền kiểm soát của bạn.

---

## Tài Nguyên

- **ClawdBot Official**: [clawd.bot](https://clawd.bot)
- **MiniMax Platform**: [minimax.io](https://minimax.io)
- **BotFather Telegram**: [@BotFather](https://t.me/BotFather)
- **Peter Steinberger (tác giả)**: [@steipete](https://x.com/steipete)

---

*Bài viết này được tạo bởi ClaudeKit Marketing, dựa trên video hướng dẫn chính thức của ClawdBot.*
