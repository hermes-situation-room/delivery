# ============================================================
# 🧭 HERMES SITUATION ROOM — LOCAL RELEASE STAGE SETUP GUIDE
# ============================================================

Welcome to your **local release-stage environment**! This guide walks you through **setting up and running the full Hermes system locally with HTTPS**, using either **self-signed certificates** or **mkcert**.

---

## 📁 Project Structure

Your folder layout should look like this:

```
release-stage/
├── compose.yaml
├── .env
├── nginx/
│   └── conf.d/
│       └── default.conf
└── README.md   ← (this file)
```

## 📁 .env File Structure

```env
DB_CONN_STRING=Server=mssql-release;User=sa;Password=YOUR_DB_PASSWORD;TrustServerCertificate=true;Initial Catalog=HERMESSituationRoom;
MSSQL_SA_PASSWORD=YOUR_DB_PASSWORD
```

> **Note:** Replace `YOUR_DB_PASSWORD` with the actual secret password. Never commit real passwords to version control.

---

## ⚙️ STEP 1 — Add Local Domain Mapping

Since the system uses the hostname `hermes-situation-room.release`, map it to `127.0.0.1`.

### macOS / Linux

```bash
sudo nano /etc/hosts
```

Add:
```
127.0.0.1 hermes-situation-room.release
```

### Windows

Edit as Administrator:
```
C:\Windows\System32\drivers\etc\hosts
```

Add:
```
127.0.0.1 hermes-situation-room.release
```

Access the site at:
👉 [https://hermes-situation-room.release](https://hermes-situation-room.release)

---

## 🔐 STEP 2 — Generate Local HTTPS Certificates

Let’s Encrypt cannot issue certificates for `.release`. Use **self-signed** or **mkcert**.

### ✅ OPTION 1 — Self-Signed Certificate

```bash
mkdir -p certbot/conf/live/hermes-situation-room.release
openssl req -x509 -nodes -newkey rsa:2048 \
  -keyout certbot/conf/live/hermes-situation-room.release/privkey.pem \
  -out certbot/conf/live/hermes-situation-room.release/fullchain.pem \
  -days 365 \
  -subj "/CN=hermes-situation-room.release"
```

**Trust locally:**

*macOS:* Open `fullchain.pem` → add to "System" keychain → set “Always Trust”.

*Windows:* Double-click `.pem` → Install Certificate → Local Machine → Trusted Root Certification Authorities.

---

## 🚀 STEP 3 — Launch the Full System

```bash
docker compose --env-file .env up -d
```

Containers started:
* 🧱 SQL Server (`hermes-sql-release`)
* ⚙️ Backend API (`hermes-backend-release`)
* 🖥️ Frontend (`hermes-frontend-release`)
* 🌐 Nginx Proxy (`nginx-release-proxy`)
* 🔁 Certbot (`hermes-certbot-release`)

---

## ✅ STEP 4 — Test Everything

Check containers:
```bash
docker ps
```
Expected:
```
nginx-release-proxy
hermes-frontend-release
hermes-backend-release
hermes-sql-release
hermes-certbot-release
```

Visit:
[https://hermes-situation-room.release](https://hermes-situation-room.release)

---

## 🔄 Maintenance

Stop the system:
```bash
docker compose -f docker-compose.release.yml down
```

View logs:
```bash
docker logs hermes-backend-release
docker logs nginx-release-proxy
```

Clean old images:
```bash
docker system prune -af
```

---

## 🧩 Summary

| Step | Action                                         |
| ---- | ---------------------------------------------- |
| 1    | Add local domain to `/etc/hosts`               |
| 2    | Create SSL cert (self-signed or mkcert)       |
| 3    | Run `docker compose --env-file .env up -d`    |
| 4    | Visit `https://hermes-situation-room.release` |

---

### 🧠 Notes

* For production, replace `.release` with a real domain and use Let’s Encrypt.
* Certbot container auto-renews valid certificates.

---

🪶 *Hermes Situation Room — Release Stage Deployment (Local Testing Edition)*

