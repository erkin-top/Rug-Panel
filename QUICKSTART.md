# 🚀 Rug-Panel Quick Start

> 🌍 **English Quick Start** | **[Русское руководство](QUICKSTART_RU.md)**

> Minimal guide to get up and running in 5 minutes

## Step 1: Server Preparation

You need a Linux server with:
- Ubuntu 20.04+ / Debian 11+ (or any modern Linux)
- Docker installed
- Public IP address

```bash
# Check kernel version (should be >= 5.6)
uname -r

# Install Docker (if not present)
curl -fsSL https://get.docker.com | sh
```

## Step 2: Run Rug-Panel

### Option A: From Source

```bash
git clone https://github.com/erkin-top/rug-panel.git
cd rug-panel
cp .env.example .env

# Generate SECRET_KEY
python3 -c "import secrets; print(secrets.token_urlsafe(32))"

# Insert the generated key into .env file
nano .env  # Find SECRET_KEY= and paste the key

# Start
docker compose up -d
```

### Option B: Pre-built Image

```bash
mkdir rug-panel && cd rug-panel
mkdir data

# Create docker-compose.yml
cat > docker-compose.yml << 'EOF'
services:
  rug-panel:
    image: erkintop/rug-panel:latest
    container_name: rug-panel
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    ports:
      - "8000:8000/tcp"
      - "51820:51820/udp"
    environment:
      - SECRET_KEY=REPLACE_ME_WITH_RANDOM_KEY
      - LANGUAGE=en
    volumes:
      - ./data:/app/data
      - /lib/modules:/lib/modules:ro
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv4.conf.all.src_valid_mark=1
EOF

# Generate key and update docker-compose.yml
python3 -c "import secrets; print('Replace SECRET_KEY with:', secrets.token_urlsafe(32))"
nano docker-compose.yml  # Replace SECRET_KEY

# Start
docker compose up -d
```

## Step 3: First Login

1. Open browser: `http://YOUR_IP:8000`
2. Login: `admin` / `admin`
3. **Change password immediately!** → Profile → Change Password

## Step 4: Configure WireGuard

1. Go to "Server" → "Settings"
2. Ensure **Server Endpoint** = your public IP
3. If not - enter and save

## Step 5: Create VPN Client

1. Dashboard → **"Add Client"**
2. Fill in:
   - Name: e.g., `iPhone-John`
   - Leave the rest as default
3. **Create**
4. Choose connection method:
   - **QR code** → scan in WireGuard app
   - **Download config** → import file

## Step 6: Connect Device

### Mobile Phone (iOS/Android)

1. Install [WireGuard](https://www.wireguard.com/install/)
2. Open app → "Add Tunnel"
3. Scan QR code from panel
4. Enable tunnel

### Computer (Windows/macOS/Linux)

1. Install [WireGuard](https://www.wireguard.com/install/)
2. Download config from panel
3. Import file to app
4. Activate tunnel

## ✅ Done!

You now have a working personal VPN server!

---

## 🆘 Something Not Working?

### WireGuard Not Starting

```bash
# Check logs
docker compose logs -f

# Check kernel module
sudo modprobe wireguard
```

### Client Cannot Connect

- Check that port 51820/UDP is open in firewall
- Verify Server Endpoint is correct
- Check logs: `docker compose logs -f`

### Panel Unavailable

```bash
# Check container status
docker compose ps

# Restart
docker compose restart
```

---

## 📚 Detailed Documentation

- [README.md](README.md) - Full documentation
- [GitHub Issues](https://github.com/erkin-top/rug-panel/issues) - Support
- [🇷🇺 Русское руководство](QUICKSTART_RU.md) - Russian Quick Start

---

**Congratulations on your launch! 🎉**
