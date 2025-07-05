# scripts-todo-things

## 1. 🔒 check_ssl_expiry (CSE)

A production-ready Bash script to monitor, report, and auto-renew SSL certificates managed via Let's Encrypt. It’s perfect for VPS/server admins who want to automate SSL expiry monitoring and receive timely email alerts before certs expire.

> 📁 This script is part of a Git-managed repo. Keep it up to date by pulling regularly.

## 📌 Features

* ✅ List all domains and their SSL expiry
* ✅ Email alert if any cert is expiring soon (customizable threshold)
* ✅ Optional auto-renewal of near-expiry certs
* ✅ Optional force-renew for one/all certs
* ✅ Sends renewal confirmation via email
* ✅ Clean CLI output with colored formatting
* ✅ Designed to run via `cron` or `systemd`
* ✅ Secure and minimal dependencies

## ⚙️ Prerequisites

1. **Bash Shell** (Linux system)
2. **Certbot installed**

   ```bash
   sudo apt install certbot
   ```

3. **Mailutils installed** for sending emails:

   ```bash
   sudo apt install mailutils
   ```

4. **You must be using Let's Encrypt**, and your certs should be under `/etc/letsencrypt/live`

5. ✉️ Mailutils Configuration (`~/.mailrc`)

   To enable SSL expiry alerts via email, you must configure your SMTP credentials in a `.mailrc` file. Here's how:

   * 📄 Create `~/.mailrc`

      ```bash
      nano ~/.mailrc
      ```

   Choose your configuration based on your email provider:
      * ✅ Gmail SMTP (Recommended with App Password)

         > ⚠️ Google requires [App Passwords](https://support.google.com/accounts/answer/185833) if 2FA is enabled.

      * ✅ Custom SMTP (e.g. `your@mail.com`)

      * ✅ No Authentication SMTP (Local or Whitelisted IP)

## 🧰 Setup

### 1. 📁 Clone the repo

```bash
git clone https://github.com/vinugawade/scripts-todo-things.git
```

Ensure the script file is saved at:

```bash
~/scripts-todo-things/check_ssl_expiry
```

Make it executable:

```bash
chmod +x ~/scripts-todo-things/check_ssl_expiry
```

### 2. 🔗 Symlink for Global Access

Create a symlink so it can be used from anywhere:

```bash
sudo ln -s ~/scripts-todo-things/check_ssl_expiry /usr/local/bin/check_ssl_expiry
```

Now you can run it using:

```bash
sudo check_ssl_expiry -e you@example.com
```

### 📁 Optional arguments

| Flag        | Description                              | Default                         |
|-------------|------------------------------------------|---------------------------------|
| `-e`        | Email address to receive alerts (required) | —                               |
| `-p`        | Path to certs                            | `/etc/letsencrypt/live`        |
| `-d`        | Days before expiry to warn               | `15`                            |

## 🚀 Usage

### 🔎 Just check and alert

```bash
sudo check_ssl_expiry -e you@example.com
```

### 🔁 Auto-renew expiring certs

```bash
sudo check_ssl_expiry -e you@example.com --auto
```

### ♻️ Force renew all certs

```bash
sudo check_ssl_expiry -e you@example.com --now
```

### 🎯 Force renew specific domain

```bash
sudo check_ssl_expiry -e you@example.com --now=yourdomain.com
```

## ⏱️ Systemd Timer Setup (Recommended)

### 1. Create service unit

```bash
sudo nano /etc/systemd/system/check_ssl_expiry.service
```

```ini
[Unit]
Description=Check and auto-renew SSL certificates
After=network.target

[Service]
ExecStart=/usr/local/bin/check_ssl_expiry -e you@example.com --auto
User=root
```

### 2. Create timer unit

```bash
sudo nano /etc/systemd/system/check_ssl_expiry.timer
```

```ini
[Unit]
Description=Daily SSL expiry check

[Timer]
OnCalendar=*-*-* 08:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

### 3. Enable and start

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable --now check_ssl_expiry.timer
```

To check status:

```bash
systemctl status check_ssl_expiry.timer
journalctl -u check_ssl_expiry.service
```

## 🧧 Setting Default From Address (Optional)

To send email from `your@mail.com`, edit this file:

```bash
sudo nano /etc/mailutils.conf
```

Add:

```txt
set from=your@mail.com
```

## 🧪 Testing

To simulate near-expiry and test email flow:

```bash
sudo check_ssl_expiry -e you@example.com -d 90
```

## 🧯 Troubleshooting

| Issue                            | Solution                                                                 |
|----------------------------------|--------------------------------------------------------------------------|
| `Permission denied`              | Ensure you're running as `sudo` or root                                  |
| `cert.pem not found`             | Make sure your certs are managed by Let’s Encrypt                       |
| No emails received               | Test mailutils: echo "test" \| mail -s "test subject" <your@mail.com>              |
| Webroot not configured properly | Ensure your certbot uses `--webroot` method with correct path            |

## 👨‍🔧 Author

Feel free to fork or contribute!

© [Vinay Gawade](https://github.com/vinugawade) · [GPL-3.0 license](LICENSE)
