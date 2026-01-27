# ClawdBot + MiniMax M2.1 Installation Guide: Build a 24/7 AI Agent on Your Computer

What if you had an AI assistant living on your computer, ready to respond via Telegram or WhatsApp anytime?

Not a web chatbot you need to open in a browser. Not an app that forgets context after a few hours. A **real AI agent** - with long-term memory, running 24/7 on your machine, and you can message it like texting a friend.

**That's exactly what ClawdBot + MiniMax M2.1 delivers.**

This guide walks you through setting up ClawdBot with MiniMax M2.1 - the combination recommended by creator Peter Steinberger himself. In 10 minutes, you'll have your own AI agent ready to serve you via Telegram.

---

## What Is ClawdBot? Why MiniMax M2.1?

### ClawdBot: An AI Agent Living on Your Computer

ClawdBot isn't just a chatbot. It's an **AI agent framework** that lets you:

- **Run AI 24/7** on your personal computer
- **Long-term memory** - remembers previous conversations
- **Multi-channel communication** - Telegram, WhatsApp, Discord, Slack, Signal...
- **Extend with skills** - integrate 1Password, Apple Notes, Obsidian, and more
- **Create new skills** - ask ClawdBot to write skills for itself

### Why MiniMax M2.1?

Peter Steinberger - ClawdBot's creator - [recommends MiniMax M2.1](https://x.com/steipete/status/170107016598849759) because:

- **Low cost** - Starter plan is just $10/month with 100 prompts/5 hours
- **High quality** - M2.1 model competes with GPT-4 and Claude
- **Modern text-to-speech** - Can expand to voice calls or voice messages

---

## Requirements Before Starting

Before installation, ensure you have:

| Requirement | Description |
|-------------|-------------|
| **macOS/Linux** | ClawdBot runs on Unix-based systems |
| **Terminal** | Command line access |
| **MiniMax account** | Sign up at [minimax.io](https://minimax.io) |
| **Telegram account** | To create bot and receive messages |
| **Internet connection** | For installation and API calls |

---

## Step 1: Install ClawdBot

### 1.1 Run Installation Command

Open Terminal and run:

```bash
curl -fSsl https://clawd.bot/install.sh | bash
```

The installer will automatically download and start the setup wizard.

### 1.2 Confirm Security

ClawdBot is a powerful system with access to your computer. The installer will ask:

```
Security
I understand this is powerful and inherently risky. Continue?
> Yes / No
```

Select **Yes** to continue.

### 1.3 Choose Installation Mode

```
Onboarding mode
> Quickstart
  Advanced
```

Select **Quickstart** - sufficient for most users and much faster.

---

## Step 2: Configure MiniMax M2.1

### 2.1 Select Model Provider

When the installer displays the provider list:

```
Model/auth provider
  OpenAI
  Anthropic
> MiniMax (M2.1 recommended)
  Cohere
  Google
  ...
```

Select **MiniMax (M2.1 recommended)**.

### 2.2 Choose Authentication Method

```
MiniMax auth method
> MiniMax M2.1
  MiniMax M2.1 Lightning (faster, higher output cost)
```

Select **MiniMax M2.1** - balanced between speed and cost.

### 2.3 Get API Key from MiniMax

**Step 2.3.1:** Visit [platform.minimaxi.io/subscribe/coding-plan](https://platform.minimaxi.io/subscribe/coding-plan)

**Step 2.3.2:** Choose an appropriate plan:

| Plan | Price | Prompts/5 hours |
|------|-------|-----------------|
| **Starter** | $10/month | 100 |
| Plus | $25/month | 300 |
| Max | $50/month | Unlimited |

**Starter plan is sufficient** for most personal users.

**Step 2.3.3:** Scroll to **API Key** section, click **Reset & Copy**

**Step 2.3.4:** Popup displays secret key (format: `sk-cp-Mupz6h3JJ130I78QNJ-vRTDXXpbwc0FvIVkM5Gh_eC64I`)

**Step 2.3.5:** Click **Confirm** and paste key into Terminal:

```
Enter MiniMax API Key
> [paste key and press Enter]
```

### 2.4 Confirm Default Model

```
Default model
> Keep current (minimax/MiniMax M2.1)
```

Select **Keep current** to use M2.1.

---

## Step 3: Set Up Telegram Bot

### 3.1 Select Communication Channel

```
Select channel (Quickstart)
> Telegram (Bot API)
  WhatsApp
  Discord
  Slack
  Signal
  ...
```

Select **Telegram (Bot API)** - easiest to set up.

### 3.2 Create Bot on Telegram

**Step 3.2.1:** Visit [web.telegram.org](https://web.telegram.org/k/)

**Step 3.2.2:** Search and open chat with **@BotFather**

**Step 3.2.3:** Send command:

```
/newbot
```

**Step 3.2.4:** BotFather asks for bot name - enter your desired name:

```
MIST
```

**Step 3.2.5:** BotFather requires username (must end with `bot` or `_bot`):

```
my_clawd_bot
```

**Step 3.2.6:** BotFather sends token in format:

```
6510928366:AAEHj9dMUFLMRA-ZSSMjYHmCwYICy
```

### 3.3 Enter Bot Token

Copy the token and paste into Terminal:

```
Enter Telegram bot token
> [paste token and press Enter]
```

### 3.4 (Optional) Enter Telegram User ID

If the installer requests Telegram User ID:

1. Open Telegram and find **@userinfobot**
2. Send any message
3. Bot will return your User ID
4. Enter ID into Terminal

---

## Step 4: Configure Skills

### 4.1 Select Required Skills

```
Configure skills now? (recommended)
> Yes
```

Select **Yes** and check the skills you want:

| Skill | Description |
|-------|-------------|
| `1password` | Password manager integration |
| `apple-notes` | Read/write Apple Notes |
| `apple-reminders` | Manage Reminders |
| `obsidian` | Obsidian vault integration |
| `camsnap` | Capture webcam photos |
| `summarize` | Summarize long text |

**Tip:** You can add skills later, or ask ClawdBot to create new skills!

### 4.2 Skip Additional API Keys

The installer will ask about other API keys (Google Maps, OpenAI, ElevenLabs...).

Select **No** for all if you don't need them yet - can add later.

---

## Step 5: Enable Hooks and Launch

### 5.1 Enable Hooks

```
Enable hooks?
> command-logger
> session-memory
```

Select both:
- **command-logger**: Log executed commands
- **session-memory**: Session memory storage

### 5.2 Launch Interface

```
Start TUI (Best option!)
> Yes
```

Select **Yes** to start the bot.

---

## Step 6: Test ClawdBot

### 6.1 Test via Web UI

Browser automatically opens Control UI at:

```
http://127.0.0.1:18709
```

In the **Chat** tab, type:

```
Hello
```

Bot will respond:

```
Hey! Good to see you! What can I help you with today?
```

### 6.2 Test via Telegram

1. Open Telegram
2. Find the bot you created (username from Step 3.2.5)
3. Click **START**
4. Send any message

Bot will respond - confirming everything works!

---

## Expansion: Advanced Features

ClawdBot supports many advanced features:

### Text-to-Speech

MiniMax has modern text-to-speech models. You can:
- Set up bot to send voice messages
- Allow direct voice calls to bot
- Receive voice responses instead of text

### Multi-Channel Integration

After Telegram setup, you can add:
- WhatsApp
- Discord
- Slack
- Signal
- Matrix

### Self-Creating Skills

ClawdBot can create its own skills. Simply request:

```
Create a skill to check my Gmail inbox
```

Bot will write and install the new skill itself.

---

## Troubleshooting

### Bot Not Responding on Telegram

1. Check Control UI at `http://127.0.0.1:18709` to see if bot is running
2. Confirm bot token is correct
3. Ensure you clicked START in Telegram

### API Key Error

1. Visit [MiniMax Platform](https://platform.minimaxi.io)
2. Check subscription plan is still active
3. Reset and copy API key again

### Terminal Closes - Bot Stops

ClawdBot needs Terminal open to run. To run in background:

```bash
# Use screen or tmux
screen -S clawdbot
# Start ClawdBot, then Ctrl+A, D to detach
```

---

## Summary

You've successfully set up a 24/7 AI agent on your computer with:

- **MiniMax M2.1** - high-quality AI model, low cost
- **Telegram integration** - message bot like a friend
- **Long-term memory** - bot remembers previous conversations
- **Extensible with skills** - integrate with apps and services

The core difference from regular chatbots: **ClawdBot lives on YOUR computer, not someone else's cloud**. Your data, your control.

---

## Resources

- **ClawdBot Official**: [clawd.bot](https://clawd.bot)
- **MiniMax Platform**: [minimax.io](https://minimax.io)
- **BotFather Telegram**: [@BotFather](https://t.me/BotFather)
- **Peter Steinberger (creator)**: [@steipete](https://x.com/steipete)

---

*This article was created by ClaudeKit Marketing, based on the official ClawdBot tutorial video.*
