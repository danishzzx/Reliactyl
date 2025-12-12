# Configuration Guide (settings.json)

This guide explains every section of the `settings.json` file. This file is the heart of your Reliactyl installation, controlling everything from the website port to the Discord integration and resource pricing.

> **Note**: Always restart your application after making changes to `settings.json` for them to take effect.

---

## 🌐 Website Settings

Basic configuration for the dashboard's web server.

```json
"website": {
  "port": 3000,
  "secret": "Your Secret Here"
}
```
- **port**: The port the dashboard will run on (default: `3000`).
- **secret**: A secure random string used for session encryption. Change this to something unique!

---

## 🦖 Pterodactyl Integration

Connect Reliactyl to your Pterodactyl panel.

```json
"pterodactyl": {
  "domain": "https://panel.yourdomain.com",
  "key": "ptla_..."
}
```
- **domain**: The full URL to your Pterodactyl panel.
- **key**: Your **Application API Key** from Pterodactyl (Admin -> Application API).

---

## 🗄️ Database

```json
"database": "sqlite://database.sqlite"
```
- **database**: The connection string for your database.
  - SQLite: `"sqlite://database.sqlite"` (Recommended for small setups)
  - MongoDB: `"mongodb://user:pass@host:port/db"`
  - Others supported by Keyv (Postgres, MySQL, Redis).

---

## 🔐 Discord OAuth2

Required for user login. Create an application at the [Discord Developer Portal](https://discord.com/developers/applications).

```json
"oauth2": {
  "id": "CLIENT_ID",
  "secret": "CLIENT_SECRET",
  "link": "http://your-domain.com",
  "callbackpath": "/callback",
  "prompt": true
}
```
- **id**: Your Discord Application Client ID.
- **secret**: Your Discord Application Client Secret.
- **link**: The base URL of your Reliactyl dashboard (e.g., `https://dashboard.example.com`).
- **callbackpath**: The path for the OAuth2 callback (default: `/callback`).
- **prompt**: If `true`, users will always be prompted to authorize, even if they've done so before.

---

## 💰 Coins & Store

Configure the economy and resource purchasing.

### Coins System
```json
"coins": {
  "enabled": true,
  "store": {
    "enabled": true,
    "ram": { "cost": 100, "per": 1024 },
    "disk": { "cost": 100, "per": 5120 },
    "cpu": { "cost": 100, "per": 75 },
    "servers": { "cost": 100, "per": 2 }
  }
}
```
- **ram/disk/cpu/servers**:
  - `cost`: How many coins it costs.
  - `per`: How much resource you get for that cost (e.g., 1024MB RAM for 100 coins).

### Earning Coins
- **Linkvertise**: Set `userid` and `coins` reward in the `linkvertise` object.
- **AFK Page**: Configured under `api.arcio.afk page`.
  - `every`: Seconds between rewards.
  - `coins`: Amount of coins given per interval.

---

## 💳 Stripe (Payments)

Enable real-money purchases for coins.

```json
"stripe": {
  "enabled": true,
  "key": "sk_test_...",
  "coins": 100
}
```
- **enabled**: Set to `true` to enable.
- **key**: Your Stripe Secret Key.
- **coins**: How many coins a user gets for **£1.00** (GBP).

---

## 📦 Packages & Resources

Define the default resources new users receive.

```json
"packages": {
  "default": "default",
  "list": {
    "default": {
      "ram": 1024,
      "disk": 1024,
      "cpu": 100,
      "servers": 1
    }
  }
}
```
- **default**: The name of the package assigned to new users.
- **list**: Define multiple packages here.

### Role Packages (Boost Rewards)
Give extra resources to users with specific Discord roles (e.g., Server Boosters).
```json
"rolePackages": {
  "roleServer": "Guild_ID",
  "roles": {
    "Role_ID": "Package_Name"
  }
}
```

---

## 🥚 Eggs & Locations

Configure which server types and locations are available.

### Locations
```json
"locations": {
  "1": {
    "name": "US | New York",
    "package": null
  }
}
```
- **"1"**: The Location ID from Pterodactyl.
- **name**: Display name for the dashboard.

### Eggs
Define server types (Minecraft, Python, Node.js, etc.).
```json
"eggs": {
  "paper": {
    "display": "Minecraft Java",
    "info": {
      "egg": 5,
      "docker_image": "...",
      "startup": "..."
    }
  }
}
```
- **egg**: The Egg ID from Pterodactyl.
- You must configure the `docker_image`, `startup` command, and environment variables exactly as they appear in Pterodactyl.

---

## 🤖 Discord Bot & J4R

### Bot Settings
```json
"bot": {
  "token": "BOT_TOKEN",
  "joinguild": { ... },
  "giverole": { ... }
}
```
- **token**: Your Discord Bot Token.
- **joinguild**: Automatically add the bot to user's servers (requires permissions).
- **giverole**: Give a role to users when they join your Discord server.

### Join for Rewards (J4R)
Reward users for joining specific Discord servers.
```json
"j4r": {
  "ads": [
    {
      "name": "My Server",
      "invite": "https://discord.gg/...",
      "id": "Guild_ID",
      "coins": 100
    }
  ]
}
```

---

## 🛡️ Security & Anti-Abuse

- **Whitelist**: Restrict dashboard access to specific User IDs.
- **AntiVPN**: Block VPNs using [ProxyCheck.io](https://proxycheck.io).
  - Set `enabled` to `true` and provide your `APIKey`.
- **Logging**: Log user actions (create server, buy coins) to a Discord Webhook.
- **Rate Limits**: Prevent API spam by adjusting limits in `api.client.ratelimits`.

---

## 🔄 Renewals

Require users to pay coins to keep their servers running.

```json
"renewals": {
  "status": false,
  "cost": 50,
  "delay": 14
}
```
- **cost**: Coins required to renew.
- **delay**: Number of days before renewal is required.

