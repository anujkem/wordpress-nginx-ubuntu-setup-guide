# wordpress-nginx-ubuntu-setup-guide
Step-by-step guide for setting up WordPress with Nginx on Ubuntu. This repository provides detailed instructions on configuring Nginx, PHP, and MySQL, as well as enabling necessary extensions like cURL and XMLReader for a fully functional WordPress environment.


================================================================
# WP Full Setup With Nginx, Ubuntu
================================================================

1. Update System and Install Nginx
------------------------------------
sudo apt update -y
sudo apt install nginx

2. Start and Enable Nginx
---------------------------
sudo systemctl start nginx
sudo systemctl enable nginx

3. Install PHP and PHP Extensions
------------------------------------
sudo apt install php-fpm php-mysql

4. Configure PHP for WordPress
-------------------------------
1. Open the PHP configuration file:
   sudo nano /etc/php/7.4/fpm/php.ini       # Adjust version if needed

2. Uncomment and set the following directive:
   cgi.fix_pathinfo=0

3. Restart PHP-FPM service:
   sudo systemctl restart php7.4-fpm

5. Download and Set Up WordPress
------------------------------------
1. Navigate to the web root directory:
   cd /var/www/html

2. Create a directory for WordPress:
   sudo mkdir wordpress
   cd wordpress

3. Download and extract WordPress:
   sudo wget https://wordpress.org/latest.tar.gz
   sudo tar -xvzf latest.tar.gz
   sudo rm latest.tar.gz                    # Remove the downloaded tar file

6. Set Proper Permissions
---------------------------
sudo chown -R www-data:www-data /var/www/html/wordpress
sudo chmod -R 755 /var/www/html/wordpress

7. Configure WordPress
------------------------
1. Move and edit the sample configuration file:
   sudo mv wp-config-sample.php wp-config.php
   sudo nano wp-config.php

2. Update the following lines with your database details:
   define('DB_NAME', 'wordpress');
   define('DB_USER', 'wp_user');
   define('DB_PASSWORD', 'strong_password');
   define('DB_HOST', 'localhost');

8. Configure Nginx for WordPress
-----------------------------------
1. Open Nginx configuration file for your WordPress site:
   sudo nano /etc/nginx/sites-available/wordpress

2. Add the following Nginx server block configuration (adjust PHP version as necessary):
   server {
       listen 80;
       server_name your-domain.com;
       root /var/www/html/wordpress;

       index index.php index.html index.htm;

       location / {
           try_files $uri $uri/ /index.php?$args;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;   # Adjust version if needed
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       location ~ /\.ht {
           deny all;
       }
   }

3. Save and close the file.

9. Enable Nginx Site Configuration
------------------------------------
1. Create a symbolic link to enable the configuration:
   sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/

2. Test the Nginx configuration:
   sudo nginx -t

3. Reload Nginx to apply the changes:
   sudo systemctl reload nginx

(Optional) 10. Install SSL with Certbot
-----------------------------------------
1. Install Certbot for SSL:
   sudo apt install certbot python3-certbot-nginx -y

2. Obtain and install an SSL certificate:
   sudo certbot --nginx -d your-domain.com   # Replace with your domain
