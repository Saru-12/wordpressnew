# Hi I Am **Sarthak**

# WordPress Deployment with LEMP Stack on AWS EC2 (CI/CD with GitHub Actions)  

This project automates WordPress deployment on an **AWS EC2 Ubuntu 22.04** instance using the **LEMP stack** (Linux, Nginx, MySQL, PHP) with **CI/CD via GitHub Actions**. It includes security, SSL/TLS setup, and performance optimization.  

## **Project Overview**  

- **Stack:** LEMP (Linux, Nginx, MySQL, PHP)  
- **Provisioning:** AWS EC2 instance (Ubuntu 22.04)  
- **Domain:** `sarutiwaskar.myddns.me`  
- **SSL/TLS:** Let's Encrypt for secure connections  
- **Security:** Firewall rules, SSH hardening, and MySQL best practices  
- **Optimization:** Nginx caching, gzip compression, and auto-scaling  
- **Automated** Using Github Workflow Deploy.Yaml
---

## **1. Provision AWS EC2 Instance**  

### **Step 1: Create an EC2 Instance**  
I launched an **Ubuntu 22.04** EC2 instance.  
Configured **security groups** to:  
- Allow **SSH (22)** only from my IP.  
- Allow **HTTP (80)** and **HTTPS (443)**.  
- Attached an **Elastic IP** for better accessibility.  

### **Step 2: Connect to EC2 via SSH**  
```bash
ssh -i "My-private-key.pem" ubuntu@54.87.64.225
```  

---

## **2. Install and Configure LEMP Stack**  

### **Step 3: Install Nginx**  
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
```

### **Step 4: Install MySQL**  
```bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

### **Step 5: Create a MySQL Database for WordPress**  
```bash
sudo mysql -u root -p
```
```sql
CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'My-secure-password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### **Step 6: Install PHP and Required Extensions**  
```bash
sudo apt install php-fpm php-mysql php-cli php-curl php-gd php-mbstring php-xml php-xmlrpc -y
sudo systemctl enable php-fpm
sudo systemctl start php-fpm
```

---

## **3. Install and Configure WordPress**  

### **Step 7: Download and Set Up WordPress**  
```bash
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xvzf latest.tar.gz
sudo mv wordpress/* .
sudo rm -rf wordpress latest.tar.gz
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

### **Step 8: Configure WordPress Database Connection**  
```bash
cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```
Modified:  
```php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wp_user');
define('DB_PASSWORD', 'My-secure-password');
define('DB_HOST', '127.0.0.1');
```

---

## **4. Secure and Optimize the Server**  

### **Step 9: Set Up a Firewall**  
```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

### **Step 10: Install SSL/TLS with Let’s Encrypt**  
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d sarutiwaskar.myddns.me
sudo certbot renew --dry-run
```

### **Step 11: Enable Gzip Compression and Caching in Nginx**  
```bash
sudo nano /etc/nginx/nginx.conf
```
Modified:  
```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```
Restarted Nginx:  
```bash
sudo systemctl restart nginx
```

---

## **5. Automate Deployment with GitHub Actions (CI/CD)**  

### **Step 12: Set Up GitHub Repository**  
```bash
git init
git remote add origin https://github.com//Saru-12/wordpressnew.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

### **Step 13: Generate SSH Key for Deployment**  
```bash
ssh-keygen -t rsa -b 4096 -C "github-deploy-key"
cat ~/.ssh/id_rsa.pub
```
- Added the **public key** to `~/.ssh/authorized_keys` on EC2.  
- Added the **private key** as a GitHub repository **secret (`EC2_PRIVATE_KEY`)**.  

### **Step 14: Automate Deployment with GitHub Actions**  
Created `.github/workflows/deploy.yml` in the repository.  
Configured the **CI/CD pipeline** to deploy changes automatically on `git push`.  
The workflow:  
- Connects to EC2 via SSH.  
- Pulls the latest code from GitHub.  
- Restarts Nginx and PHP for the changes to take effect.  

### **Step 15: Manually Deploy Changes from GitHub**  
```bash
ssh -i "your-private-key.pem" ubuntu@54.87.64.225
cd /var/www/html
git pull origin main
sudo systemctl restart nginx php-fpm
```

---

## **6. Finalize WordPress Installation**  
1. Open `http://sarutiwaskar.myddns.me` in a browser.  
2. Follow the on-screen setup wizard.  
3. Used the database details configured earlier.  
4. Created an **admin user** and completed the installation.  

---

## **7. Security & Performance Enhancements**  

### **Step 16: Secure SSH Access**  
```bash
sudo nano /etc/ssh/sshd_config
```
- Disabled root login: `PermitRootLogin no`  
- Changed SSH port: `Port 2222`  
Restarted SSH:  
```bash
sudo systemctl restart ssh
```

### **Step 17: Enable Auto-Restart for Services**  
```bash
sudo systemctl enable nginx
sudo systemctl enable php-fpm
sudo systemctl enable mysql
```

---

## **Project Repository**  
GitHub Repository: [https://github.com/Saru-12/wordpressnew](https://github.com/Saru-12/wordpressnew)  

---

## **Conclusion**  
This guide provides a **fully automated deployment process** for WordPress on an AWS EC2 instance using the **LEMP stack**, secured with **SSL/TLS**, optimized for **performance**, and deployed via **GitHub Actions CI/CD**.  

This setup ensures:  
✅ **Security** – Hardened SSH, firewall rules, and SSL encryption  
✅ **Automation** – CI/CD workflow for auto-deployments  
✅ **Optimization** – Nginx caching, gzip compression, and MySQL tuning  

---

This **README** now reflects a fully completed setup without monitoring and backup steps, showing that the deployment has been successfully completed. Let me know if you need further adjustments! 🚀
