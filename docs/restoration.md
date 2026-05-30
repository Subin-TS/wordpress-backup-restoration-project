Restoration Procedure  

## Install dependencies again  
sudo apt install apache2 -y
sudo apt install mariadb-server -y
sudo apt apt install php php-mysql -y 


## Restore files 

sudo tar -xzvf wordpress-files.tar.gz -C /  

## Restore Apache configuration

sudo tar -xzvf apache-config.tar.gz -C /

## Restore database 

mysql -u root -p wordpressdb < wordpressdb.sql

## Restart services  

sudo systemctl restart apache2
sudo systemctl restart mariadb
