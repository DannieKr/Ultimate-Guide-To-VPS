![Wordpress Logo](./images/WordPress-logotype-standard-white.png)
You always wanted to run your own website or blog? Then WordPress is the perfect choice for you!
WordPress is a free Content Management System (CMS) with which you can create your own websites, blogs and much more. It is based on PHP and MySQL and is one of the most popular CMS in the world. It is easy to use and has a large community that provides a lot of plugins and themes.
This tutorial will guide you through the installation and configuration of WordPress on your own server.

# Requirements
- A Linux VPS (I use Debian 12)
- Sudo or root access to the server
- A domain (optional)

# Installing and configuring PHP
WordPress is based on PHP, so we need to install PHP first.

At first we update the package list and upgrade the installed packages.
```bash
sudo apt update
```

```bash
sudo apt upgrade
```

Now we can install PHP and some required extensions:
```bash
sudo apt install php php-mysql
```

Now we have to adjust some settings in the PHP configuration file:
```bash
sudo nano /etc/php/8.2/apache2/php.ini
```

Change the following settings:
```bash
memory_limit = 512M
upload_max_filesize = 512M
post_max_size = 512M
data.timezone = Europe/Berlin
```
You can adjust the variables to your needs. You should also adjust the timezone to your location. You can find a list of supported timezones [here](https://www.php.net/manual/en/timezones.php).

# Installing other required packages
Now we need to install some other packages including a web server and a database server. We will use Apache as web server and MariaDB as database server.

```bash
sudo apt install apache2 unzip wget curl mariadb-client mariadb-server
```

# Configuring the database
At first we have to secure the MariaDB installation:
```bash
sudo mysql_secure_installation
```
Follow the instructions to secure the installation.

# Setting up the database
WordPress stores its data in a database. We have to create a new database and a new user for WordPress.

First we login to the MariaDB console:
```bash
sudo mysql -u root -p
```

Now we create a new database:
```sql
CREATE DATABASE wordpress;
```

Now we create a new user and grant him the necessary permissions:
```sql
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'your_password_here';
```
```sql
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'localhost';
```
```sql
FLUSH PRIVILEGES;
```
```sql
exit;
```

# Installing WordPress
Now we can download and install WordPress.

At first remove the default website of Apache:
```bash
sudo rm /var/www/html/index.html
```

Now we download and extract WordPress:
```bash
cd
```
```bash
wget https://wordpress.org/latest.zip
```
```bash
unzip latest.zip
```
```bash
sudo cp -R /wordpress/* /var/www/html
```
```bash
sudo rm latest.zip && sudo rm -r wordpress
```

# Adjust folder permissions
To allow WordPress to write to its own files and folders we have to adjust the permissions of the WordPress folder:
```bash
sudo chown -R www-data:www-data /var/www/html
```
```bash
sudo find /var/www/html -type d -exec chmod 755 {} \;
```
```bash
sudo find /var/www/html -type f -exec chmod 644 {} \;
```

# Install an SSL certificate (optional if you have a domain)
If you have a domain you should install an SSL certificate to encrypt the data transfer between the server and the client. You can use certbot to install a free SSL certificate from Let's Encrypt.

At first we have to install certbot:
```bash
sudo apt install certbot python3-certbot-apache
```

Now we can install SSL certificates:
```bash
sudo certbot --apache
```
Follow the instructions to install the SSL certificate.

# Finish the installation
All the necessary steps are done. Now you can open your website in your browser and finish the installation of WordPress.

# Change the default permalink structure (optional)
WordPress default permalinks contain /index.php in the URL. You can remove it by changing the permalink structure.

Login to your WordPress admin panel and navigate to Settings -> Permalinks. Choose the "Post name" option and save the changes.

If you should encounter a 404 error after changing the permalink structure, you have to adjust the Apache configuration. Open the Apache configuration file:
```bash
sudo nano /etc/apache2/apache2.conf
```
Add the following lines at the end of the file:
```bash
<Directory /var/www/html>
    AllowOverride All
</Directory>
```

Restart apache2 service:
```bash
sudo service apache2 restart
```
