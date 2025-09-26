# 🚀 FastAPI + Gunicorn + Nginx + HTTPS (with nip.io)

This guide shows how to expose a FastAPI app securely over HTTPS on a VPS with only a static IP (no custom domain).  
We’ll use:

- **Gunicorn** → runs FastAPI
- **Nginx** → reverse proxy
- **Certbot (Let’s Encrypt)** → free SSL certificates
- **nip.io** → wildcard DNS that maps `IP.nip.io` → `IP`

---

## 1. Install dependencies

```bash
sudo apt update
sudo apt install nginx certbot python3-certbot-nginx -y
```

---

## 2. Run FastAPI with Gunicorn

Example command (adjust to your app):

```bash
gunicorn app:app -w 4 -k uvicorn.workers.UvicornWorker -b 127.0.0.1:8000
```

- Runs Gunicorn on `127.0.0.1:8000`
- Replace `app:app` with `your_module:app`

---

## 3. Create Nginx site config

```bash
sudo cat > /etc/nginx/sites-available/fastapi << 'EOF'
server {
    listen 80;
    server_name 34.159.10.25.nip.io;

    location / {
        proxy_pass http://127.0.0.1:8000;  # Gunicorn running on port 8000
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
EOF
```

👉 Replace `34.159.10.25` with **your server’s static IP**.

Enable the config:

```bash
sudo ln -s /etc/nginx/sites-available/fastapi /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 4. Get a Let’s Encrypt certificate

Use `nip.io` hostname:

```bash
sudo certbot --nginx -d 34.159.10.25.nip.io
```

Test renewal:

```bash
sudo certbot renew --dry-run
```

---

## 5. Access your app

Now visit:

👉 `https://34.159.10.25.nip.io`

You’ll see a valid HTTPS lock 🔒

---

## 6. Notes

- `nip.io` auto-resolves `IP.nip.io` → `IP`. Example:
  - `34.159.10.25.nip.io` → `34.159.10.25`
- Certificates from Let’s Encrypt are valid for **90 days**, but Certbot auto-renews them.
- To disable the site later:
  ```bash
  sudo rm /etc/nginx/sites-enabled/fastapi
  sudo systemctl reload nginx
  ```

---
