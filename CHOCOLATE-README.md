# 🍫 Nginx + DNS + SSL Deployment — Chocolate Website

## Overview
This project documents how I deployed a **static chocolate website** to an **AWS EC2 server** from scratch — configuring Nginx as a web server, pointing a custom domain using DNS, and securing the site with a free SSL certificate from Let's Encrypt.

This is a real-world DevOps workflow used in production environments.

---

## 🌐 Live Website
🔗 [https://salissweet.shop](https://salissweet.shop)

---

## 🧰 Technologies Used

| Tool | Purpose |
|------|---------|
| AWS EC2 (Ubuntu 24.04) | Cloud server to host the website |
| Nginx | Web server to serve the static files |
| Hostinger | Domain name registrar |
| Let's Encrypt + Certbot | Free SSL certificate for HTTPS |
| Git | Cloning the source code |
| SSH | Securely connecting to the remote server |

---

## 🗂️ Project Structure

```
chocolate-website/
├── index.html       # Main HTML file
├── style.css        # Stylesheet
├── script.js        # JavaScript file
├── choco1.jpg       # Image asset
├── choco2.jpg       # Image asset
└── README.md        # Documentation
```

---

## 📖 What is Nginx?
Nginx (pronounced "Engine-X") is a powerful web server that serves website files to users who visit your domain. Unlike running `npm start` or `python app.py`, Nginx runs as a **system service** — meaning it starts automatically and keeps running even after you disconnect from the server.

---

## 📖 What is DNS?
DNS (Domain Name System) is like a phonebook for the internet. It maps your domain name (`salissweet.shop`) to your server's IP address (`44.222.105.130`). Without DNS, users would have to type your IP address directly into the browser.

---

## 📖 What is SSL?
SSL (Secure Sockets Layer) encrypts the connection between your server and the user's browser. It's what gives your website the 🔒 padlock and changes `http://` to `https://`. We used **Let's Encrypt** — a free, trusted certificate authority — to get our SSL certificate.

---

## 🚀 Step-by-Step Deployment

### Step 1 — Connect to AWS EC2 via SSH
```bash
ssh -i "your-key.pem" ubuntu@your-ec2-public-ip
```
SSH (Secure Shell) allows you to remotely control your server from your local machine securely.

---

### Step 2 — Install and Start Nginx
```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl status nginx
```
This installs Nginx and starts it as a system service. Once installed, Nginx automatically serves a default page on port 80.

---

### Step 3 — Clone the Website Repository
```bash
git clone https://github.com/salisu2009/chocolate-website.git
```
This downloads the chocolate website source code from GitHub to the server.

---

### Step 4 — Copy Files to Nginx Web Directory
```bash
sudo cp -r chocolate-website/* /var/www/html/
```
`/var/www/html/` is Nginx's default directory — whatever files you put here get served to visitors. We copy all our website files there so Nginx can serve them.

---

### Step 5 — Create Nginx Configuration for the Domain
```bash
sudo nano /etc/nginx/sites-available/salissweet.shop
```

Paste this configuration:
```nginx
server {
    listen 80;
    server_name salissweet.shop www.salissweet.shop;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

**What each line does:**
- `listen 80` — Listen for HTTP traffic on port 80
- `server_name` — Respond to requests for your domain
- `root` — Where the website files are located
- `index` — The first file to load when someone visits
- `try_files` — Serve the requested file or return a 404 error

---

### Step 6 — Enable the Site
```bash
sudo ln -s /etc/nginx/sites-available/salissweet.shop /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```
- `ln -s` creates a symbolic link (shortcut) from `sites-available` to `sites-enabled` to activate the config
- `nginx -t` tests the configuration for errors
- `systemctl restart nginx` applies the changes

---

### Step 7 — Configure DNS
In Hostinger DNS Zone, add this A record:

| Type | Name | Points to | TTL |
|------|------|-----------|-----|
| A | @ | 44.222.105.130 | 14400 |

This tells the internet that `salissweet.shop` lives at your AWS server's IP address.

---

### Step 8 — Install Free SSL Certificate
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d salissweet.shop
```
- `certbot` is a tool that automatically gets and installs SSL certificates from Let's Encrypt
- `--nginx` tells Certbot to configure Nginx automatically
- `-d salissweet.shop` specifies the domain to secure

After running this, your site is accessible via `https://` with a 🔒 padlock!

---

## 🔐 AWS Security Group — Inbound Rules

| Port | Protocol | Source | Purpose |
|------|----------|--------|---------|
| 22 | TCP | 0.0.0.0/0 | SSH Access |
| 80 | TCP | 0.0.0.0/0 | HTTP traffic |
| 443 | TCP | 0.0.0.0/0 | HTTPS traffic |

---

## 💡 Key Lessons Learned

- **Nginx as a system service** means it runs 24/7 without keeping a terminal open
- **DNS propagation** takes time — be patient after adding records
- **Certbot automates SSL** — no manual certificate configuration needed
- Always run `sudo nginx -t` before restarting Nginx to catch errors
- **Local testing before deployment** saves time and avoids mistakes on the server

---

## 👤 Author

**Salisu** — DevOps Enthusiast  
GitHub: [@salisu2009](https://github.com/salisu2009)  
Website: [https://salissweet.shop](https://salissweet.shop)
