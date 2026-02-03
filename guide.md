# WordPress on AWS: Step-by-Step Deployment Guide
> A beginner-friendly guide to deploy WordPress using AWS EC2 and RDS

## 🚀 Before You Start

### Required AWS Resources
1. AWS Account (free tier eligible)
2. EC2 Instance:
   - Ubuntu 24.04 LTS
   - t2.micro (free tier)
   - At least 8GB storage
3. Security Group for EC2 with:
   - SSH (Port 22) open to your IP
   - HTTP (Port 80) open to all
   - HTTPS (Port 443) open to all

### Required Skills
- Basic command line usage
- Basic understanding of AWS console
- Ability to follow technical instructions

## 1️⃣ Launch EC2 Instance

1. Go to AWS Console → EC2 → Launch Instance
2. Configure as follows:
   ```
   Name: wordpress-server
   OS: Ubuntu 24.04 LTS
   Instance type: t2.micro
   Key pair: Create new
   Storage: 8GB gp3
   ```
3. Save your key pair (.pem file) securely
4. Note your instance's public IP address

## 2️⃣ Connect to Your Server

### For Windows Users
1. Download PuTTY or MobaXterm
2. Convert .pem to .ppk if using PuTTY
3. Connect using your EC2's public IP

### For Mac/Linux Users
```bash
# Set correct permissions for key file
chmod 400 your-key.pem

# Connect to server
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

## 3️⃣ Install Required Software

```bash
# Update system
sudo apt update
sudo apt upgrade -y

# Install Apache and PHP
sudo apt install -y apache2 \
    php \
    libapache2-mod-php \
    php-mysql \
    php-xml \
    php-gd \
    php-mbstring \
    php-curl \
    php-zip \
    php-intl \
    php-imagick

# Enable Apache modules
sudo a2enmod rewrite
sudo systemctl restart apache2

# Verify Apache is running
sudo systemctl status apache2
```

## 4️⃣ Create RDS Database

1. Go to AWS Console → RDS → Create database
2. Choose these settings:
   ```
   Engine: MySQL
   Version: 8.0.28 or later
   Template: Free tier
   DB instance identifier: wordpress-db
   Master username: admin
   Password: Create a strong password (save it!)
   Instance: t3.micro
   Storage: 20 GB
   Public access: No
   VPC security group: Create new
   ```

3. Create a security group rule:
   - Type: MySQL/Aurora
   - Port: 3306
   - Source: Your EC2 security group ID

4. Wait for RDS to be available (≈10 minutes)
5. Note the RDS endpoint URL

## 5️⃣ Configure Database

```bash
# Install MySQL client
sudo apt install -y mysql-client

# Connect to RDS (replace with your endpoint)
mysql -h your-rds-endpoint.amazonaws.com -u admin -p

# When prompted, enter your RDS password
```

Run these SQL commands:
```sql
CREATE DATABASE wordpress_db;
CREATE USER 'wp_user'@'%' IDENTIFIED BY 'YourStrongPassword123!';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_user'@'%';
FLUSH PRIVILEGES;
EXIT;
```

## 6️⃣ Install WordPress

```bash
# Go to web root
cd /var/www/html

# Remove default Apache page
sudo rm index.html

# Download WordPress
sudo curl -O https://wordpress.org/latest.tar.gz

# Extract files
sudo tar -xzf latest.tar.gz
sudo cp -a wordpress/. .
sudo rm -rf wordpress latest.tar.gz

# Set permissions
sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;
```

## 7️⃣ Configure WordPress

```bash
# Create config file
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```

Replace database settings with:
```php
define('DB_NAME', 'wordpress_db');
define('DB_USER', 'wp_user');
define('DB_PASSWORD', 'YourStrongPassword123!');
define('DB_HOST', 'your-rds-endpoint.amazonaws.com');
```

## 8️⃣ Complete Installation

1. Open your browser
2. Go to: `http://your-ec2-public-ip`
3. Follow the WordPress setup wizard:
   - Choose language
   - Set site title
   - Create admin account
   - Complete installation

## ❓ Troubleshooting

### Can't connect to EC2?
- Check if instance is running
- Verify security group allows SSH
- Confirm using correct key file
- Try: `ssh -v -i key.pem ubuntu@ip` for debug info

### Apache not working?
```bash
# Check status
sudo systemctl status apache2

# Check logs
sudo tail -f /var/log/apache2/error.log
```

### Database connection fails?
- Verify RDS endpoint is correct
- Check security group allows EC2 connection
- Confirm database credentials
- Try connecting manually:
```bash
mysql -h endpoint -u wp_user -p
```

## 📝 Common Commands

```bash
# Restart Apache
sudo systemctl restart apache2

# Check Apache status
sudo systemctl status apache2

# View Apache error logs
sudo tail -f /var/log/apache2/error.log

# Fix permissions
sudo chown -R www-data:www-data /var/www/html
```

## 🔒 Security Tips

1. Change default admin URL:
   - Install security plugin
   - Enable custom login URL

2. Set up SSL (HTTPS):
   ```bash
   sudo apt install certbot python3-certbot-apache
   sudo certbot --apache
   ```

3. Regular updates:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
