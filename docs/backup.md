                Backup Procedure 


 1. Backup Website Files 
    sudo tar -czvf wordpress-files.tar.gz /var/www/html 
   tar -tvf wordpress-files.tar.gz | head                     # verify 

2. Backup Apache Configuration  
    sudo tar -czvf apache-config.tar.gz /etc/apache2  
    tar -tvf apache-config.tar.gz | head                   # verify 

3. Backup Database   

  mysqldump -u root -p --all-databases > all_databases_backup.sql   #All data backup including user and password details  
  ls -lh all_databases_backup.sql                                    #verify 


