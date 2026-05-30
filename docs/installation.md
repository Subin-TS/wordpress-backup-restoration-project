  WordPress Installation Guide 

       "This project is hosted on a self-hosted Linux environment using a classic LAMP (Linux, Apache, MariaDB, PHP) stack. Follow these step-by-step instructions to set up the environment and deploy the Wordpress site".  

1. Update the System (Linux best practice) 
   ##Ensure all system packages are up to date before installing new software.
   
   sudo apt update && sudo apt upgrade -y 
 
2. Install and Verify Apache 
  
   sudo apt install apache2 -y 
   systemctl status apache2   

3. Install PHP and Extensions  
   ## Install the core PHP package along with the required extensions for database connectivity, image processing, and zip handling. 
   
    sudo apt install php php-mysql php-cli php-curl php-gd php-xml php-mbstring php-zip -y
    php -v   # Verify the installation 

4. Install and Configure MariaDB 
    
   sudo apt install mariadb-server -y   # database server to store site content and user data  
   mysql --version                       # Verify 
5. Create the WordPress Database  

   sudo mysql                                                                     # login to databse server 
   CREATE DATABASE wordpressdb;                                                   #  create a secure database and a dedicated user for WordPress
   CREATE USER 'subin'@'localhost' IDENTIFIED BY 'Password@123';
   GRANT ALL PRIVILEGES ON wordpressdb.* TO 'subin'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;  

6. Download and Extract WordPress 
   ## Download the latest official WordPress release to a temporary directory, extract it, and move the core files to the web server's root directory 

   cd /tmp
   wget https://wordpress.org/latest.tar.gz
   tar -xvf latest.tar.gz
   sudo cp -r wordpress/* /var/www/html/  

7. Configure WordPress Database Links 

   ## Create the configuration file from the sample template 

      cd /var/www/html
      sudo cp wp-config-sample.php wp-config.php 
      sudo vim  wp-config.php  
            define( 'DB_NAME', 'wordpressdb' );
            define( 'DB_USER', 'subin' );
            define( 'DB_PASSWORD', 'Password' ); 

8. Set File Permissions 

    ## Assign ownership of the project directory to the Apache web user (www-data) and restrict directory permissions to ensure security  


     sudo chown -R www-data:www-data /var/www/html
     sudo chmod -R 755 /var/www/html  
9. Restart Apache  
     sudo systemctl restart apache2

10. Complete WordPress Setup via web browser  
    # access the installer by typing server Ip in browser, set following Language, username, password, email and seach engine visibility.
     http://x.x.x.x

11. Install WP-CLI
    
    cd /tmp                                     ## Download the Phar file, make it executable, and move it to system path
    curl -O https://githubusercontent.com
    chmod +x wp-cli.phar
    sudo mv wp-cli.phar /usr/local/bin/wp 
    wp --info                                          # verify installation 

12.  Change the Theme  
     
     cd /var/www/html 
     wp theme install astra --activate --allow-root  

13. Change the Admin Password (optional for practice) 

     wp user list --allow-root       ## list all users 
     wp user update admin_username --user_pass="NewPassword" --allow-root

    
   
