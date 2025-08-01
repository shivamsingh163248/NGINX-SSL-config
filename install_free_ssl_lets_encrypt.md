
# 🔒 INSTALL FREE SSL WITH LET’S ENCRYPT ON UBUNTU (NGINX)

This guide shows you how to set up a free SSL certificate using **Let’s Encrypt** and **Certbot**, with **automatic renewal** and **email notifications**.

---

## ✅ STEP 1: Install Certbot

Update and install Certbot with NGINX support:

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```

---

## ✅ STEP 2: Run Certbot for Your Domain

Run the interactive SSL installation:

```bash
sudo certbot --nginx
```

Certbot will:
- Auto-detect your NGINX config
- Prompt for your email and agree to terms
- Ask you to select domain(s) to secure
- Edit your NGINX config to enable SSL

---

## ✅ STEP 3: Verify SSL Is Working

Open your browser and visit:

```
https://yourdomain.com
```

You should see a **🔒 lock icon** in the address bar.

---

## ✅ STEP 4: Enable Automatic SSL Renewal (Cron)

Certificates expire every 90 days, so it’s important to set up auto-renewal.

### 📌 Check Auto-Renew Works:

```bash
sudo certbot renew --dry-run
```

It should show a success message like:

```
Congratulations, all simulated renewals succeeded:
  /etc/letsencrypt/live/yourdomain.com/fullchain.pem (success)
```

### 🛠️ Set Up Cron Job

Edit root's cron:

```bash
sudo crontab -e
```

If prompted, choose `1` (nano editor).

Add the following lines at the top:

```bash
MAILTO="your-email@example.com"
0 3 * * * /usr/bin/certbot renew --quiet
```

This:
- Runs Certbot every day at 3:00 AM
- Sends an email **only if there are errors or renewal events**

### ✅ Save and Exit
In nano:
- Press `Ctrl + O` → `Enter` to save
- Press `Ctrl + X` to exit

---

## ✅ STEP 5: Force HTTPS Redirect (Optional)

Edit your NGINX config file (e.g., `/etc/nginx/sites-available/default`):

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$host$request_uri;
}
```

Then test and reload NGINX:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## ✅ STEP 6: Test Cron and Email Notifications (Optional)

### ➕ Add a test cron job to verify email:

```bash
sudo crontab -e
```

Add:

```bash
MAILTO="your-email@example.com"
*/2 * * * * echo "This is a test cron email" && date
```

This runs every 2 minutes and sends a test email. Watch your inbox or spam folder.

---

## ✅ STEP 7: Force Renewal for Testing (Optional)

Force a renewal:

```bash
sudo certbot renew --force-renewal
```

Or send a test email manually:

```bash
echo "SSL Cron Test" | mail -s "Certbot Cron Test" your-email@example.com
```

If `mail` is not installed:

```bash
sudo apt install mailutils -y
```

---

## ✅ TROUBLESHOOTING

| Problem                            | Solution                                      |
|------------------------------------|-----------------------------------------------|
| Cert not renewing                  | Check `sudo certbot renew --dry-run`          |
| Cron not running                   | Check `grep CRON /var/log/syslog`             |
| No SSL for www                     | Use `-d yourdomain.com -d www.yourdomain.com` |
| Email not received                 | Check spam or test with a dummy cron task     |
| NGINX not detected                 | Use `--nginx -d yourdomain.com` manually      |

---

## 🎉 DONE!

You're now running on a **secure, auto-renewing HTTPS** setup with email alerts. You can sleep peacefully. 😴
