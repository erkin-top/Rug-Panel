# 🔐 Rug-Panel

**Lightweight WireGuard VPN Management Panel**

> 🌍 **English Documentation** | **[Русская документация](README_RU.md)**

[![GitHub Stars](https://img.shields.io/github/stars/erkin-top/rug-panel?style=social)](https://github.com/erkin-top/rug-panel)
[![Docker Pulls](https://img.shields.io/docker/pulls/erkintop/rug-panel)](https://hub.docker.com/r/erkintop/rug-panel)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-Ready-blue.svg)](https://www.docker.com/)
[![WireGuard](https://img.shields.io/badge/WireGuard-VPN-88171a.svg)](https://www.wireguard.com/)

---

## 📋 Features

- ✅ **Fully Dockerized** — Single container, all-inclusive
- 🚀 **Quick Start** — Deploy with one command
- 🔄 **Easy Migration** — Copy your config and run
- 📱 **HTMX Interface** — Fast and responsive UI
- 🌐 **QR Codes** — For mobile device connections
- 🔒 **JWT Authentication** — Secure access with httpOnly cookies
- 📊 **Monitoring** — Real-time client status
- 🌍 **Multilingual** — Russian and English interface
- ⚡ **Optimized** — Caching, connection pooling, GZip compression

---

## 🐳 Docker Deployment (Recommended)

### Requirements

- **Docker** and **Docker Compose v2+**
- **Linux** with kernel 5.6+ (WireGuard built-in)

> 💡 On modern Linux distributions (Ubuntu 20.04+, Debian 11+), WireGuard is already integrated into the kernel — no additional installation required.

### Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/erkin-top/rug-panel.git
cd rug-panel

# 2. Set up environment variables
cp .env.example .env

# 3. Generate SECRET_KEY
python -c "import secrets; print(secrets.token_urlsafe(32))"
# Or: openssl rand -base64 32

# 4. Set SECRET_KEY in .env
nano .env

# 5. Start the container
docker compose up -d
```

**Panel:** `http://YOUR_SERVER_IP:8000`  
**Login:** `admin` / `admin`

⚠️ **Change the password after first login!**

⚠️ **Network interface (WAN) for docker is eth0**


### Using Pre-built Image from Docker Hub

If you don't want to build the image locally, use the pre-built one:

```bash
# docker-compose.yml
services:
  rug-panel:
    image: erkintop/rug-panel:latest  # or :1.0.0
    # ... rest of the configuration as in the example
```

```bash
# Start
docker compose up -d
```


### Container Management

```bash
docker compose ps              # Status
docker compose logs -f         # Logs
docker compose restart         # Restart
docker compose down            # Stop
docker compose up -d --build   # Rebuild
docker compose exec rug-panel /bin/sh   # Shell
docker compose exec rug-panel wg show   # WireGuard status
```

---

## 💻 Manual Setup (Without Docker)

### Requirements

- **Python 3.11+**
- **Linux** with WireGuard installed
- `sudo` privileges for WireGuard management

### Installation

```bash
# 1. Install WireGuard
sudo apt install wireguard wireguard-tools   # Ubuntu/Debian
sudo dnf install wireguard-tools             # Fedora/RHEL

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate

# Or with conda:
conda create -n rugEnv python=3.11
conda activate rugEnv

# 3. Install dependencies
pip install -r requirements.txt

# 4. Configure environment variables
cp .env.example .env
nano .env  # Set SECRET_KEY

# 5. Run
python run.py
```

**Panel:** `http://localhost:8000`

> ⚠️ **Windows/macOS:** The management panel works, but WireGuard VPN requires Linux. For full functionality, use Docker on a Linux server.

---

## 🔄 Migrating Existing Configuration

```bash
mkdir -p ./data
cp /etc/wireguard/wg0.conf ./data/wg0.conf
docker compose up -d
```

All clients are automatically imported.

---

## ⚙️ Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `SECRET_KEY` | ⚠️ **REQUIRED** | JWT encryption key |
| `PANEL_PORT` | 8000 | Web panel port |
| `WG_PORT` | 51820 | WireGuard UDP port |
| `WG_INTERFACE` | wg0 | WireGuard interface name |
| `WG_SERVER_ENDPOINT` | *(auto-detect)* | External IP/domain of server (overrides auto-detection) |
| `DEFAULT_DNS` | 77.88.8.8, 8.8.8.8 | DNS servers for clients |
| `DEFAULT_ALLOWED_IPS` | 0.0.0.0/0, ::/0 | Routes for clients |
| `DEFAULT_PERSISTENT_KEEPALIVE` | 25 | Keepalive interval (seconds) |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | 1440 | JWT lifetime (24h) |
| `DEBUG` | false | Debug mode |
| `LOG_LEVEL` | INFO | Logging level |
| `CONFIG_CACHE_TTL` | 5 | Config cache TTL (seconds) |
| `STATUS_CACHE_TTL` | 2 | Status cache TTL (seconds) |
| `QR_CACHE_SIZE` | 100 | QR code LRU cache size |
| `LANGUAGE` | ru | Interface language (ru/en) |

> 💡 **WG_SERVER_ENDPOINT** — Use this to fix the server domain/IP. If set, auto-detected IP will NOT override this value when generating client configs.

---

## 📁 Project Structure

```
rug-panel/
├── app/                      # FastAPI application
│   ├── main.py               # Entry point
│   ├── config.py             # Configuration
│   ├── wireguard.py          # WireGuard manager
│   ├── routes/               # API routes
│   └── templates/            # Jinja2 templates
├── docker/
│   ├── Dockerfile            # Container image
│   └── entrypoint.sh         # Startup script
├── static/                   # CSS styles
├── data/                     # wg0.conf, panel.db
├── docker-compose.yml
├── run.py                    # Local startup
└── requirements.txt
```

---

## 📱 Connecting Clients

### Creating a Client

1. Open the panel → log in
2. Click **"Add Client"**
3. Fill in the form → **"Create"**
4. Download config or scan QR code

### Mobile Devices

1. Install WireGuard: [Android](https://play.google.com/store/apps/details?id=com.wireguard.android) / [iOS](https://apps.apple.com/app/wireguard/id1441195209)
2. Scan QR code from the panel

### Computers

```bash
# Linux
sudo mv client.conf /etc/wireguard/wg-client.conf
sudo wg-quick up wg-client

# Windows/macOS — Import .conf in WireGuard app
```

---

## 🐛 Troubleshooting

### WireGuard Not Starting in Docker

```bash
# Check kernel version (should be 5.6+)
uname -r

# Check WireGuard module
sudo modprobe wireguard
lsmod | grep wireguard

# If kernel < 5.6 — Update system
sudo apt update && sudo apt upgrade
```

### Clients Cannot Connect

```bash
# Open WireGuard port
sudo ufw allow 51820/udp

# Check IP forwarding
sysctl net.ipv4.ip_forward  # Should be 1

# Check WireGuard status
docker compose exec rug-panel wg show
```

---

## 🔐 Security

- Passwords hashed with **PBKDF2-HMAC-SHA256** (100,000 iterations)
- JWT token in **httpOnly cookie** (XSS protection)
- **SameSite=Lax** for CSRF protection
- Automatic configuration backups

**Recommendations:**
1. Change admin password after installation
2. Set unique `SECRET_KEY`
3. Use HTTPS via reverse proxy (nginx/Caddy)
4. Restrict access through firewall

---

## 🏗️ Technologies

| Component | Technology |
|-----------|------------|
| Backend | FastAPI + Python 3.11 |
| Frontend | Jinja2 + HTMX |
| Database | SQLite (WAL mode) |
| Auth | JWT + PBKDF2-HMAC-SHA256 |
| Container | Docker + dumb-init |
| VPN | WireGuard (kernel mode) |

---

## 📄 License and Attribution

This project is licensed under the **Apache License 2.0**.

**Copyright © 2026 [Erkin](https://erkin.top)**

When using or modifying this project, please:
- Preserve the link to the author ([erkin.top](https://erkin.top))
- Reference the original repository: [github.com/erkin-top/rug-panel](https://github.com/erkin-top/rug-panel)
- Comply with the Apache 2.0 license terms (see [LICENSE](LICENSE) and [NOTICE](NOTICE))

License details:
- [LICENSE](LICENSE) — Full text of Apache License 2.0
- [NOTICE](NOTICE) — Copyright and attribution information

**Contacts:**
- 🌐 Website: [erkin.top](https://erkin.top)
- 🐙 GitHub: [github.com/erkin-top/rug-panel](https://github.com/erkin-top/rug-panel)
- 🐳 Docker Hub: [hub.docker.com/r/erkintop/rug-panel](https://hub.docker.com/r/erkintop/rug-panel)

---

## 📚 Additional Documentation

- [🚀 QUICKSTART.md](QUICKSTART.md) - Quick Start in 5 Minutes
- [🌐 TRANSLATION_GUIDE.md](TRANSLATION_GUIDE.md) - Translation Guide
- [🇷🇺 Русская документация](README_RU.md) - Russian Documentation

---

**Developed with ❤️ for simple WireGuard management**
