
# ✅ PrestaShop Docker to cPanel Migration Guide

This guide covers the exact steps taken to migrate a fully customized PrestaShop instance from a local Docker environment to a cPanel server.

---

## 🔧 Prerequisites

- Access to your Docker container files (`docker-compose.yml`, code, images, DB)
- cPanel/WHM access with root (`sudo`) permissions
- PHP 8.1 installed on the server (match your local version)
- Terminal/SSH access to the server

---

## 🔄 Step-by-Step Migration

### 1. 📦 Export Files from Docker

```bash
cd ~/Development/evitaflames/
zip -r prestashop.zip prestashop/
docker exec -it <mysql_container_name> mysqldump -u root -p prestashop > db.sql
```

### 2. 🆙 Upload to cPanel

- Use cPanel File Manager or SFTP to upload `prestashop.zip` and `db.sql` to `public_html/`
- Extract `prestashop.zip` into `public_html/prestashop/`

### 3. 🛠️ Import Database

- Create a new MySQL database and user in cPanel
- Use **phpMyAdmin** to import `db.sql` into your new DB

### 4. ⚙️ Update `app/config/parameters.php`

Set your new DB credentials:

```php
'database_host' => 'localhost',
'database_name' => 'your_db_name',
'database_user' => 'your_db_user',
'database_password' => 'your_db_password',
```

### 5. 🖼️ Fix Image Permissions

```bash
cd public_html/prestashop
chmod -R 755 img
chmod -R 775 img/p img/tmp
chown -R your_cpanel_user:your_cpanel_user img
```

### 6. 🌐 Regenerate `.htaccess`

- Go to PrestaShop Admin > Shop Parameters > Traffic & SEO
- Click “Save” at the bottom

### 7. 🔄 Fix Product Image Upload Error (500)

#### Problem: `mime_content_type()` is missing

#### Fix:

```bash
sudo yum install ea-php81-php-fileinfo
sudo systemctl restart httpd
```

Then confirm:

```bash
/usr/local/bin/php -m | grep fileinfo
```

Also clear PrestaShop cache:

```bash
rm -rf public_html/prestashop/var/cache/*
```

### 8. ✅ Test Everything

- Product images display? ✅
- Product image upload works? ✅
- Admin & frontend work without errors? ✅

---

## 🎉 Done

You’ve successfully moved PrestaShop from Docker to a live cPanel server. Keep this guide for future reference!

---

## 🙌 Support

Maintained by **ghafoori** 🧠 — DM anytime you need deep PrestaShop, PHP, or DevOps help.
