## 🖥️ 1) Update System & Install LAMP Basics

Connect to your Ubuntu 24 EC2 instance:

```bash
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

Update packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Install Apache:

```bash
sudo apt install -y apache2
```

Install PHP with WordPress required extensions:

```bash
sudo apt install -y php libapache2-mod-php php-mysql php-xml php-gd php-mbstring
```

Enable rewrite rules for WordPress pretty URLs:

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## 📂 2) Set Correct Permissions

WordPress files will go in the web root:

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

---

## 📥 3) Download & Extract WordPress

Go to a temp folder and fetch the latest WordPress:

```bash
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
```

Extract it:

```bash
tar -xzf latest.tar.gz
```

Copy the files to your web root:

```bash
sudo cp -a /tmp/wordpress/. /var/www/html
```

Remove the default Apache index page so WordPress takes over:

```bash
sudo rm /var/www/html/index.html
```

Fix ownership again:

```bash
sudo chown -R www-data:www-data /var/www/html
```

---

## 🗄️ 4) Prepare AWS RDS MySQL Database

In the AWS console, create an **RDS MySQL** instance. Make sure the **EC2 instance’s security group is allowed to access the RDS instance on port 3306**, and note the **RDS endpoint** (something like `xxx.us-east-1.rds.amazonaws.com`). ([disloops][1])

Once RDS is running, connect to it from anywhere you can (e.g., from EC2):

```bash
mysql -h your-rds-endpoint.amazonaws.com -u admin-user -p
```

Create a database and user for WordPress:

```sql
CREATE DATABASE wordpress_db;
CREATE USER 'wp_user'@'%' IDENTIFIED BY 'StrongPass123!';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_user'@'%';
FLUSH PRIVILEGES;
```

This gives WordPress the access it needs. ([disloops][1])

---

## 📝 5) Configure WordPress to Use RDS

Move into the WordPress folder:

```bash
cd /var/www/html
```

Copy the sample config file:

```bash
sudo cp wp-config-sample.php wp-config.php
```

Open it for editing:

```bash
sudo nano wp-config.php
```

Find the database section and update it:

```php
define( 'DB_NAME', 'wordpress_db' );
define( 'DB_USER', 'wp_user' );
define( 'DB_PASSWORD', 'StrongPass123!' );
define( 'DB_HOST', 'your-rds-endpoint.amazonaws.com' );
```

---

### 🔐 Add Unique Security Keys

WordPress needs secure authentication keys (salts). Go to this URL in your browser:

```
https://api.wordpress.org/secret-key/1.1/salt/
```

It will show you random values like:

```php
define('AUTH_KEY',         'random-phrase');
define('SECURE_AUTH_KEY',  'random-phrase');
...
```

Copy that block **and replace the placeholder lines** in your `wp-config.php`. ([Ubuntu][2])

Save and exit (Ctrl+O → Enter → Ctrl+X).

---

## 📡 6) Final Permissions & Restart Apache

Make sure the WordPress files are owned by the Apache user:

```bash
sudo chown -R www-data:www-data /var/www/html
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

## 🌐 7) Complete WordPress Setup in Browser

Open a browser and go to:

```
http://your-ec2-public-ip
```

You’ll see the WordPress setup page. Follow the on-screen steps to set the **site name, WordPress admin user, and password**. WordPress will use your RDS database info automatically from the config file. ([Ubuntu][2])

---

## 🧠 Tips

* Make sure your **EC2 and RDS security groups** allow traffic from the EC2 instance to RDS on **port 3306**.
* If you see “Error establishing database connection,” check that the RDS user, database name, and endpoint are correct and reachable.
* You do **not install MySQL on the EC2 instance** since the database is on RDS. ([Amazon Web Services, Inc.][3])
