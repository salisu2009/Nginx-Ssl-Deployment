# 🍫 Chocolate Website Deployment with Nginx, DNS & SSL

A hands-on DevOps project where I deployed a static chocolate website to an **AWS EC2 server** using **Nginx**, configured a custom **domain name**, and secured it with a **free SSL certificate** from Let's Encrypt.

---

## 🌐 Live Website

🔗 [https://salissweet.shop](https://salissweet.shop)

---

## 📋 Project Overview

| Item | Details |
|------|---------|
| Website | Static HTML/CSS/JS Chocolate Website |
| Server | AWS EC2 (Ubuntu 24.04) |
| Web Server | Nginx |
| Domain | salissweet.shop |
| SSL | Let's Encrypt (via Certbot) |

---

## 🛠️ Tools & Technologies

- **AWS EC2** — Cloud server (Ubuntu 24.04)
- **Nginx** — Web server to serve the static website
- **Hostinger** — Domain name registrar
- **Certbot** — Free SSL certificate from Let's Encrypt
- **Git** — Cloning the repository
- **SSH** — Remote server access

---

## 📁 Repository

🔗 [salisu2009/chocolate-website](https://github.com/salisu2009/chocolate-website)

---

## 🚀 Deployment Steps

### Step 1 — Connect to AWS EC2

```bash
ssh -i "your-key.pem" ubuntu@your-ec2-public-ip
```

---

### Step 2 — Install Nginx

```bash
sudo apt update
sudo apt install nginx -y
```

Verify Nginx is running:

```bash
sudo systemctl status nginx
```

---

### Step 3 — Clone the Repository

```bash
git clone https://github.com/salisu2009/chocolate-website.git
```

---

### Step 4 — Copy Website Files to Nginx Directory

```bash
sudo cp -r chocolate-website/* /var/www/html/
```

Verify files are in place:

```bash
ls /var/www/html/
```

---

### Step 5 — Configure Nginx for the Domain

Create a new Nginx config file:

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

---

### Step 6 — Enable the Site

```bash
sudo ln -s /etc/nginx/sites-available/salissweet.shop /etc/nginx/sites-enabled/
```

Test the configuration:

```bash
sudo nginx -t
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

---

### Step 7 — Configure DNS

In Hostinger DNS Zone, add this record:

| Type | Name | Points to | TTL |
|------|------|-----------|-----|
| A | @ | your-ec2-public-ip | 14400 |

---

### Step 8 — Install SSL Certificate

Install Certbot:

```bash
sudo apt install certbot python3-certbot-nginx -y
```

Get the SSL certificate:

```bash
sudo certbot --nginx -d salissweet.shop
```

Follow the prompts — enter your email and agree to the terms.

---

## 🔐 AWS Security Group — Inbound Rules

| Port | Protocol | Source | Purpose |
|------|----------|--------|---------|
| 22 | TCP | 0.0.0.0/0 | SSH Access |
| 80 | TCP | 0.0.0.0/0 | HTTP |
| 443 | TCP | 0.0.0.0/0 | HTTPS |

---

## 💡 Key Lessons Learned

- Nginx serves static websites by putting files in `/var/www/html/`
- Always create a separate Nginx config file for each domain in `sites-available`
- Use `ln -s` to enable the site by linking it to `sites-enabled`
- Always run `sudo nginx -t` before restarting Nginx to catch errors
- DNS propagation can take time — be patient!
- Certbot automatically configures Nginx for HTTPS — no manual SSL setup needed
- A domain name + SSL makes your site look professional and trustworthy 🔒

---

## 👤 Author

**Salisu** — DevOps Enthusiast  
GitHub: [@salisu2009](https://github.com/salisu2009)  
Website: [https://salissweet.shop](https://salissweet.shop)
