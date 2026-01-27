# ClawdBot + MiniMax Coding Plan Setup

Deploy ClawdBot on Ubuntu VPS with MiniMax M2.1 as the AI backend.

## Prerequisites

- Ubuntu VPS (tested on 22.04/24.04)
- Node.js v22+
- SSH access to VPS

## Quick Start

### 1. Install ClawdBot

```bash
# SSH into your VPS
ssh user@your-vps-ip

# Install via npm (recommended)
npm install -g clawdbot@latest

# Or use the official installer
curl -fsSL https://clawd.bot/install.sh | bash

# Add npm global bin to PATH (if needed)
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 2. Configure MiniMax Coding Plan

Edit `~/.clawdbot/clawdbot.json`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "minimax": {
        "baseUrl": "https://api.minimax.io/anthropic",
        "apiKey": "YOUR_MINIMAX_API_KEY",
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

### 3. Run Onboard Wizard

```bash
clawdbot onboard --non-interactive --accept-risk --mode local --install-daemon
```

### 4. Set MiniMax as Default Model

```bash
clawdbot models set minimax/MiniMax-M2.1
systemctl --user restart clawdbot-gateway
```

### 5. Verify Installation

```bash
clawdbot models status
clawdbot doctor
```

---

## Connect Messaging Platforms

### WhatsApp

**Setup:**

1. Add WhatsApp config to `~/.clawdbot/clawdbot.json`:

```json
{
  "channels": {
    "whatsapp": {
      "dmPolicy": "allowlist",
      "allowFrom": ["+84123456789"]
    }
  }
}
```

2. Link your WhatsApp account:

```bash
clawdbot channels login
```

3. Scan the QR code with WhatsApp > Linked Devices > Link a Device

**Configuration Options:**

| Option | Values | Description |
|--------|--------|-------------|
| `dmPolicy` | `pairing`, `allowlist`, `open`, `disabled` | DM access control |
| `allowFrom` | `["+1234567890"]` | Allowed phone numbers (E.164 format) |
| `selfChatMode` | `true/false` | Enable for personal WhatsApp number |
| `ackReaction` | emoji | Auto-react on message receipt |

**Pairing Mode (for unknown senders):**

```bash
clawdbot pairing list whatsapp
clawdbot pairing approve whatsapp <CODE>
```

---

### Telegram

**Setup:**

1. Create bot via [@BotFather](https://t.me/BotFather):
   - Send `/newbot`
   - Follow prompts to get your bot token

2. Add Telegram config to `~/.clawdbot/clawdbot.json`:

```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "botToken": "123456789:ABCdefGHIjklMNOpqrsTUVwxyz",
      "dmPolicy": "pairing"
    }
  }
}
```

3. Restart gateway:

```bash
systemctl --user restart clawdbot-gateway
```

**Configuration Options:**

| Option | Values | Description |
|--------|--------|-------------|
| `dmPolicy` | `pairing`, `allowlist`, `open`, `disabled` | DM access control |
| `groupPolicy` | `allowlist`, `open`, `disabled` | Group access control |
| `customCommands` | `[{command, description}]` | Add custom bot commands |

**Group Settings:**

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

**Tips:**
- Disable Privacy Mode via BotFather `/setprivacy` for group access
- Get group ID: forward message to [@userinfobot](https://t.me/userinfobot)

---

### Discord

**Setup:**

1. Create Discord Application:
   - Go to [Discord Developer Portal](https://discord.com/developers/applications)
   - Create New Application > Bot > Generate Token
   - Enable "Message Content Intent" and "Server Members Intent"

2. Generate Invite URL:
   - OAuth2 > URL Generator
   - Scopes: `bot`, `applications.commands`
   - Permissions: View Channels, Send Messages, Read Message History, Embed Links, Attach Files, Add Reactions

3. Add Discord config to `~/.clawdbot/clawdbot.json`:

```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "YOUR_DISCORD_BOT_TOKEN",
      "dmPolicy": "pairing"
    }
  }
}
```

4. Restart gateway:

```bash
systemctl --user restart clawdbot-gateway
```

**Configuration Options:**

| Option | Values | Description |
|--------|--------|-------------|
| `dmPolicy` | `pairing`, `allowlist`, `open`, `disabled` | DM access control |
| `guilds.<id>.allowFrom` | `["user_id"]` | Per-guild allowlist |

---

## Useful Commands

```bash
# Status & Health
clawdbot status
clawdbot doctor
clawdbot models status

# Logs
clawdbot logs -f

# Channel Management
clawdbot channels status
clawdbot channels login

# Pairing (for new users)
clawdbot pairing list <platform>
clawdbot pairing approve <platform> <code>

# Gateway Control
systemctl --user start clawdbot-gateway
systemctl --user stop clawdbot-gateway
systemctl --user restart clawdbot-gateway
systemctl --user status clawdbot-gateway

# Model Management
clawdbot models list --all
clawdbot models set <model>
```

---

## Configuration Reference

Full config file location: `~/.clawdbot/clawdbot.json`

```json
{
  "agents": {
    "defaults": {
      "maxConcurrent": 4,
      "workspace": "/home/user/clawd"
    }
  },
  "gateway": {
    "mode": "local",
    "port": 18789,
    "bind": "loopback"
  },
  "models": {
    "mode": "merge",
    "providers": {
      "minimax": {
        "baseUrl": "https://api.minimax.io/anthropic",
        "apiKey": "YOUR_API_KEY",
        "api": "anthropic-messages",
        "models": [...]
      }
    }
  },
  "channels": {
    "whatsapp": { ... },
    "telegram": { ... },
    "discord": { ... }
  }
}
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `command not found: clawdbot` | Add npm bin to PATH: `export PATH="$HOME/.npm-global/bin:$PATH"` |
| WhatsApp disconnected | Run `clawdbot channels login` and re-scan QR |
| Telegram bot not responding | Check `/setprivacy` is disabled in BotFather |
| Discord bot silent | Enable "Message Content Intent" in Developer Portal |
| Gateway won't start | Check logs: `journalctl --user -u clawdbot-gateway -f` |
| Model auth failed | Verify API key in config and restart gateway |

---

## Resources

- [ClawdBot Documentation](https://docs.clawd.bot)
- [ClawdBot GitHub](https://github.com/clawdbot/clawdbot)
- [MiniMax Coding Plan](https://platform.minimax.io/docs/coding-plan/claude-code)

---

## License

MIT
