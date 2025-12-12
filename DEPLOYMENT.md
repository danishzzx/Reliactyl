# Production Deployment Guide

This guide will walk you through setting up Reliactyl for a production environment on a Linux server (Ubuntu/Debian recommended).

## Prerequisites
- A VPS or Dedicated Server (Ubuntu 20.04/22.04 recommended).
- A domain name pointing to your server's IP.
- Node.js (v14 or v16) installed.
- Pterodactyl Panel installed and running.

---

## Step 1: Configuration
Before deploying, ensure your `settings.json` is configured for production.

1.  **Edit settings.json**:
    ```bash
    nano settings.json
    ```
2.  **Verify Key Settings**:
    - `website.secret`: Change this to a long, random string.
    - `website.port`: Keep it at `3000` (we will use a reverse proxy).
    - `pterodactyl.domain`: Ensure this matches your panel URL.
    - `api.client.oauth2.link`: Set this to your **new domain** (e.g., `https://dashboard.example.com`) **without** the trailing slash.
    - `api.client.oauth2.callbackpath`: Ensure this matches what you set in the Discord Developer Portal (e.g., `/callback`).

> 📖 **See [CONFIGURATION.md](CONFIGURATION.md) for full details on every setting.**

---

## Step 2: DNS Setup
Point your domain to your server's IP address.

1.  Go to your Domain Registrar (Namecheap, Cloudflare, etc.).
2.  Create an **A Record**:
    - **Name**: `dashboard` (or `@` for the root domain).
    - **Content/Value**: Your server's public IP address (e.g., `123.45.67.89`).
    - **TTL**: Auto or 5 min.

---

## Step 3: Set up Nginx & SSL (Certbot)
We will use Nginx as a reverse proxy to handle SSL (HTTPS) and forward traffic to Reliactyl.

1.  **Install Nginx**:
    ```bash
    sudo apt update
    sudo apt install -y nginx
    ```

2.  **Install Certbot**:
    ```bash
    sudo apt install -y certbot python3-certbot-nginx
    ```

3.  **Generate SSL Certificate**:
    Run Certbot to automatically obtain a certificate and configure Nginx. Replace `dashboard.example.com` with your actual domain.
    ```bash
    sudo certbot --nginx -d dashboard.example.com
    ```
    - Enter your email when asked.
    - Agree to the terms.
    - Choose option **2** (Redirect) if asked to redirect HTTP to HTTPS.

4.  **Configure Nginx Proxy**:
    Certbot will create a config file, but we need to ensure it forwards to port 3000.
    
    Edit the config file:
    ```bash
    sudo nano /etc/nginx/sites-available/default
    # OR if certbot created a specific file:
    # sudo nano /etc/nginx/sites-available/dashboard.example.com
    ```

    Ensure the `location /` block looks like this:
    ```nginx
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    ```

5.  **Restart Nginx**:
    ```bash
    sudo systemctl restart nginx
    ```

---

## Step 4: Process Management (PM2)
Use PM2 to keep Reliactyl running in the background and restart it automatically if it crashes or the server reboots.

1.  **Install PM2**:
    ```bash
    sudo npm install -g pm2
    ```

2.  **Start Reliactyl**:
    Make sure you are in the project folder.
    ```bash
    pm2 start index.js --name "reliactyl"
    ```

3.  **Save the Process List**:
    ```bash
    pm2 save
    ```

4.  **Setup Startup Hook**:
    This ensures the app starts on server reboot.
    ```bash
    pm2 startup
    ```
    *Copy and paste the command that PM2 outputs.*

---

## Step 5: Final Checks
1.  **Discord Developer Portal**: Go to your application -> OAuth2.
    - Add your Redirect URL: `https://dashboard.example.com/callback` (replace with your domain).
2.  **Firewall**: Ensure ports 80 (HTTP) and 443 (HTTPS) are open.
    ```bash
    sudo ufw allow 80
    sudo ufw allow 443
    ```

🎉 **Setup Complete!** Your dashboard should now be accessible at `https://dashboard.example.com`.

