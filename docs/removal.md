   Wordpress removal (Disater simulation) 


  1. Stop servicess 

sudo systemctl stop apache2
sudo systemctl stop mariadb 

  2. Delete website 

sudo rm -rf /var/www/html/* 

  3. Delete database  

DROP DATABASE wordpressdb;
DROP USER 'subin'@'localhost'; 

  4. Remove packages  

sudo apt purge apache2 -y
sudo apt purge php* -y
sudo apt purge mariadb-server -y
sudo apt autoremove -y  
   
  5.  Verify removal 

systemctl status apache2    
systemctl status mariadb   
ls -la /var/www/html/      # verify website files deleted
sudo mysql -e "SHOW DATABASES; SELECT User, Host FROM mysql.user;"   ###Login mysql verify user and database deleted  
dpkg -l | grep -E "apache2|php|mariadb"                               # Checking binaries in package manager
