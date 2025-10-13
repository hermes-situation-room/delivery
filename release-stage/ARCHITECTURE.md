# 🚀 HERMES SITUATION ROOM — RELEASE STAGE DEPLOYMENT

This diagram visualizes the architecture and flow of the release (production) environment.

All services are isolated on a private Docker network.  
Only the Nginx reverse proxy exposes ports 80/443.  

---

```
Internet / Users [PUBLIC]
     │ 80/443
     ▼
🌐 NGINX RELEASE — REVERSE PROXY [PUBLIC]
     │
     ├── 🖥️ FRONTEND [INTERNAL, PUBLIC VIA NGINX]
     │
     └── ⚙️ BACKEND API [INTERNAL ONLY]
           │
           └── 🧱 MSSQL SERVER [INTERNAL ONLY]
     │
🔒 CERTBOT [INTERNAL ONLY]
```

---

## 🔑 Key Concepts

- **Isolation:** All services run on `hermes-release-network`.
- **Frontend & API:** Internal only; Nginx proxies public requests.
- **Database:** MSSQL container; never exposed to host.
- **SSL/TLS:** Managed via Certbot and auto-reloaded in Nginx.
- **Secrets:** Stored securely as environment variables.
- **Volumes:** Persistent DB storage under `sql-server-data-release`.

---

## ⚡ Network & Ports

| Service       | Port(s)     | Exposure      |
|---------------|-------------|---------------|
| Nginx         | 80 / 443    | Public        |
| Frontend      | 80          | Internal only |
| Backend API   | 8080        | Internal only |
| MSSQL Server  | 1433        | Internal only |
| Certbot       | —           | Internal only |

---

## ✅ Deployment Notes

1. Use `.env` for secret values:
   - `DB_CONN_STRING`
   - `MSSQL_SA_PASSWORD`

2. Bring up services with:
   ```bash
   docker compose --env-file .env up -d
   ```

3. Nginx automatically proxies requests to frontend & API.

4. Certbot renews SSL certificates every 12 hours and reloads Nginx.

5. All services have resource limits defined under `deploy.resources`.