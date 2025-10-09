# Local Domain Setup Guide

This guide explains how to configure a **local custom domain** (like `hermes-situation-room.stage`) on your computer so it points to your local development environment. This works great for testing your app locally with a clean, realistic domain name.

---

## 🧠 Why This Is Needed

Your computer needs to know where to find `hermes-situation-room.stage`. Since it's not a real internet domain, you must manually tell your system to resolve it to your local machine (127.0.0.1).

---

## 💻 macOS / Linux Setup

1. Open your Terminal.
2. Run this command to edit your hosts file:

   ```bash
   sudo nano /etc/hosts
   ```

3. At the bottom of the file, add the following line:

   ```
   127.0.0.1   hermes-situation-room.stage
   ```

4. Press **CTRL + O** to save, then **ENTER**, then **CTRL + X** to exit.
5. Flush DNS cache to apply changes:

   ```bash
   sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
   ```

6. You can now open `http://hermes-situation-room.stage` or `https://hermes-situation-room.stage` in your browser.

---

## 🪟 Windows Setup

1. Press **Start**, type **Notepad**.
2. Right-click on **Notepad** → choose **Run as administrator**.
3. Open this file inside Notepad:

   ```
   C:\Windows\System32\drivers\etc\hosts
   ```

4. Add this line to the bottom of the file:

   ```
   127.0.0.1   hermes-situation-room.stage
   ```

5. Save the file and close Notepad.
6. Open **Command Prompt (Admin)** and run:

   ```cmd
   ipconfig /flushdns
   ```

7. Now you can visit `http://hermes-situation-room.stage` in your browser.

---

## ✅ Verification

You can test that your system resolves the local domain correctly:

```bash
ping hermes-situation-room.stage
```

It should return:

```
PING hermes-situation-room.stage (127.0.0.1): 56 data bytes
```

If it does, you’re all set! 🎉

---

## 🧩 Optional: HTTPS Self-Signed Certificate

If you’re running a local HTTPS setup with Nginx or another reverse proxy, you can generate a **self-signed certificate** to avoid browser warnings.

Example command:

```bash
mkdir -p certbot/conf/live/hermes-situation-room.stage
openssl req -x509 -nodes -days 365 -newkey rsa:2048   -keyout certbot/conf/live/hermes-situation-room.stage/privkey.pem   -out certbot/conf/live/hermes-situation-room.stage/fullchain.pem   -subj "/CN=hermes-situation-room.stage"
```

This will create a local certificate you can use in your Nginx config.

---

## 🧹 Undoing the Change

To remove the local domain later, just delete the line you added from your hosts file and flush the DNS cache again.

---

Enjoy your clean local domain setup! 🚀
