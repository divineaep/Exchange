# ⚡ FastExchanger

> Production-grade Discord Exchanger & Middleman Bot

Built with **Node.js**, **discord.js v14**, **better-sqlite3**, **Express.js** and **dotenv**.

---

## 📁 Project Structure

```
FastExchanger/
├── src/
│   ├── index.js                  # Entry point
│   ├── deploy.js                 # Slash command deployer
│   ├── config/
│   │   └── index.js              # Centralised config (reads .env)
│   ├── commands/                 # Slash commands
│   │   ├── panel.js              # /panel  — post the main panel
│   │   ├── rates.js              # /rates  — view rates
│   │   └── profile.js            # /profile — user profile
│   ├── events/
│   │   ├── ready.js
│   │   ├── messageCreate.js
│   │   └── interactionCreate.js
│   ├── handlers/
│   │   ├── prefixCommands.js     # .claim .done .unclaim .close .reopen .ui etc.
│   │   └── interactions.js       # Buttons, modals, slash routing
│   ├── systems/
│   │   ├── tickets/index.js      # Ticket creation & embed refresh
│   │   ├── exchange/rates.js     # Rate lookups & conversion math
│   │   ├── vouches/index.js      # Auto-vouch generation
│   │   ├── logging/index.js      # Log embeds to Discord channels
│   │   └── security/index.js     # Permission checks & security middleware
│   ├── database/
│   │   └── index.js              # SQLite setup, schema, prepared statements
│   ├── dashboard/
│   │   └── server.js             # Express REST API
│   └── utils/
│       ├── helpers.js            # ID gen, formatting, time utilities
│       └── embeds.js             # All embed builders
└── database/
    └── fastexchanger.db          # Auto-created SQLite database
```

---

## 🚀 Quick Start

### 1. Install dependencies

```bash
npm install
```

### 2. Configure environment

```bash
cp .env.example .env
# Edit .env with your values
```

### 3. Deploy slash commands

```bash
node src/deploy.js
```

### 4. Start the bot

```bash
npm start
# or for development with auto-reload:
npm run dev
```

---

## ⚙️ Environment Variables

| Variable | Required | Description |
|---|---|---|
| `BOT_TOKEN` | ✅ | Discord bot token |
| `CLIENT_ID` | ✅ | Your bot's Application ID |
| `GUILD_ID`  | ✅ | Your server's Guild ID |
| `BOT_OWNER` | ✅ | Your Discord user ID |
| `EXCHANGERS` | ✅ | Comma-separated exchanger IDs |
| `ADMINS` | ✅ | Comma-separated admin IDs |
| `PANEL_CHANNEL_ID` | ✅ | Channel where panel is posted |
| `TICKET_CATEGORY_ID` | ⚠️ | Category for ticket channels (recommended) |
| `LOG_EXCHANGE` | ⚠️ | Channel ID for exchange logs |
| `LOG_VOUCHES` | ⚠️ | Channel ID for vouch logs |
| `LOG_SCAM` | ⚠️ | Channel ID for scam report logs |
| `LOG_STAFF` | ⚠️ | Channel ID for staff action logs |
| `VOUCH_CHANNEL_ID` | ⚠️ | Public vouch channel |
| `MIN_ACCOUNT_AGE_DAYS` | — | Default: 7 |
| `MIN_JOIN_AGE_DAYS` | — | Default: 1 |
| `DASHBOARD_PORT` | — | Default: 3000 |
| `DASHBOARD_SECRET` | — | API key for dashboard |

---

## 🎫 Slash Commands

| Command | Permission | Description |
|---|---|---|
| `/panel` | Admin | Post the exchange panel embed |
| `/rates` | Everyone | View current exchange rates |
| `/profile [user]` | Everyone | View exchange profile |

---

## 📝 Prefix Commands

| Command | Permission | Description |
|---|---|---|
| `.claim` | Exchanger | Claim an open ticket |
| `.unclaim` | Exchanger | Unclaim a ticket |
| `.done` | Exchanger | Mark exchange complete + auto vouch |
| `.close` | Exchanger/Admin | Close ticket channel |
| `.reopen` | Admin | Reopen closed ticket |
| `.ui [user]` | Everyone | View user profile |
| `.setrate <key> <value>` | Admin | Update exchange rate |
| `.addex <user>` | Admin | Add exchanger (runtime) |
| `.removeex <user>` | Admin | Remove exchanger (runtime) |
| `.bl <user> [reason]` | Admin | Blacklist a user |
| `.unbl <user>` | Admin | Remove blacklist |
| `.stats` | Admin | View server statistics |

**Rate keys:** `c2i`, `i2c`, `c2b`, `b2c`

---

## 🌐 Dashboard API

The Express server exposes a REST API at `http://localhost:3000`.  
All routes (except `/`) require the header `x-api-key: <DASHBOARD_SECRET>`.

| Method | Route | Description |
|---|---|---|
| GET | `/` | Health check |
| GET | `/api/stats` | Global statistics |
| GET | `/api/tickets?status=open&limit=50` | List tickets |
| GET | `/api/user/:id` | User profile |
| GET | `/api/transactions` | Recent transactions |
| GET | `/api/rates` | Current rates |
| POST | `/api/rates` | Update a rate `{key, value}` |
| GET | `/api/vouches` | Recent vouches |
| GET | `/api/reports` | Scam reports |
| GET | `/api/logs` | Staff action logs |
| POST | `/api/blacklist` | Blacklist user `{user_id, reason}` |

---

## 🛡️ Security Features

- **Anti-self-claim** — exchangers cannot claim their own tickets
- **Anti-double-claim** — one exchanger per ticket, race-condition safe
- **Blacklist system** — persistent in SQLite
- **Account age check** — configurable minimum account age
- **Server join age check** — configurable minimum server membership
- **One open ticket per user** — prevents spam
- **Duplicate vouch prevention** — UNIQUE constraint on ticket_id
- **Exchanger permission middleware** — all sensitive commands are gated
- **Private ticket channels** — permission overwrites per ticket

---

## 💾 Database Tables

| Table | Purpose |
|---|---|
| `users` | Customer profiles and stats |
| `tickets` | All exchange tickets |
| `transactions` | Completed exchange records |
| `vouches` | Auto-generated vouches |
| `claims` | Audit trail of claim/unclaim events |
| `reports` | Scam reports |
| `rates` | Dynamic exchange rates |
| `staff_logs` | All staff actions |

---

## 📋 Required Bot Permissions

- `Send Messages`
- `Embed Links`
- `Read Message History`
- `Manage Channels`
- `Manage Roles` (for ticket permissions)
- `View Channel`
- `Use Application Commands`

Enable **Server Members Intent** and **Message Content Intent** in the Developer Portal.

---

## 🔧 Bot Intents Required

In the [Discord Developer Portal](https://discord.com/developers/applications):

- ✅ **Server Members Intent**
- ✅ **Message Content Intent**
- ✅ **Presence Intent** (optional)

---

*Made with ⚡ by FastExchanger*
