Troubleshooting 

Issue-1 Apache Default Page Displayed Instead of WordPres

Issue: Browsing to http://<SERVER-IP> loaded the default "Apache2 Ubuntu Default Page" instead of the WordPress setup wizard.
Root Cause: Both index.html (Apache default) and index.php (WordPress) existed in /var/www/html. By default, Apache prioritizes serving index.html over index.php.

Investigation Performed
Step 1: Verify Apache Service

sudo systemctl status apache2
This confirmed that Apache was functioning correctly.

Step 2: Verify WordPress Files

ls -la /var/www/html

Output showed both:

index.html
index.php
wp-admin
wp-content
wp-includes

step-3 Identify the Root Cause 

  Apache serves files from:

/var/www/html

Both of the following files were present:

/var/www/html/index.html
/var/www/html/index.php

Apache gives higher priority to:

index.html  

Resolution

Removed the Apache default page:

sudo rm -f /var/www/html/index.html

Verified:

ls -la /var/www/html | grep index

Output:

index.php

Restarted Apache:

sudo systemctl restart apache2 

Validation

Accessed the website again:

http://<SERVER-IP>   


Key Takeaway: Always delete the default index.html file in Apache's web root when deploying a PHP-based application like WordPress.

===========================================================================


Issue-2   WP-CLI Theme Installation Failure


Issue Encountered -- While installing the Astra theme using WP-CLI, the following error occurred:

wp theme install astra --activate --allow-root

Warning: Could not create directory. "/var/www/html/wp-content/upgrade"
Warning: The 'astra' theme could not be found.  
Warning: Failed to create directory '/var/www/.wp-cli/cache/': mkdir(): Permission denied. 


Investigation performed

1. Verify WordPress Directory Ownership

Checked ownership of the WordPress installation directory:

ls -ld /var/www/html
ls -ld /var/www/html/wp-content       ## www-data:www-data  -- correct

2. Verify Upgrade Directory  
 ## Checked whether the WordPress upgrade directory existed:

ls -ld /var/www/html/wp-content/upgrade     
The directory was missing and was created:

sudo mkdir -p /var/www/html/wp-content/upgrade
sudo chown -R www-data:www-data /var/www/html/wp-content

3. Verify Write Permissions 
   ## Tried  Apache user can write to the WordPress content directory 

sudo -u www-data touch /var/www/html/wp-content/testfile ls -l /var/www/html/wp-content/testfile 

4. Verify Available Disk Space
Checked server storage:

df -h


5. Identify WP-CLI Cache Error  

sudo -u www-data wp theme install astra --activate

Warning: Failed to create directory '/var/www/.wp-cli/cache/': mkdir(): Permission denied.


Root Cause

The WP-CLI cache directory did not exist:

/var/www/.wp-cli/cache/

WP-CLI could not create or access this directory, it was unable to download and extract the Astra theme package.

Resolution

Created the WP-CLI cache directory and assigned ownership to the Apache user:

sudo mkdir -p /var/www/.wp-cli/cache
sudo chown -R www-data:www-data /var/www/.wp-cli  
Verified:

ls -ld /var/www/.wp-cli/cache

Conclusion

Although WordPress file permissions, disk space, and directory ownership were configured correctly, WP-CLI failed because its cache directory was missing. Creating the cache directory and assigning the correct ownership resolved the issue and allowed the theme installation to complete successfully.

========================================================================================================================

Issue-3   WordPress Redirecting to Old Server IP After Restoration


Issue Encountered  

Site homepage was accessanble using the IP address but when try to access WordPress admin portal, it was not rechable and redirecting to another IP.

Initial Observation
•	Apache service was running.
•	MariaDB service was running.
•	WordPress files were restored.
•	Website content was visible.
•	Database import completed successfully.

Verified the administrator existed using below commands

cd /var/www/html
sudo -u www-data wp user list

Checked the current WordPress URL settings: 

sudo -u www-data wp option get siteurl
sudo -u www-data wp option get home   ##The database still contained the old server IP address from before the disaster recovery process. 

Root Cause

During backup, WordPress stored the following values inside the database and After restoration, the server received a new IP address:

Resolution
Updated the WordPress URL settings to the new server IP address:
sudo -u www-data wp option update siteurl "http://x.x.x.x" 
sudo -u www-data wp option update home "http://x.x.x.x"

Flushed WordPress rewrite rules:
sudo -u www-data wp rewrite flush
Restarted services:
sudo systemctl restart apache2
sudo systemctl restart mariadb

Key Learning
WordPress stores the website URL inside its database. During disaster recovery or server migration, if the restored server uses a different IP address or domain name, WordPress may continue redirecting users to the old address.
Always validate the following after a WordPress restoration:
•	Website homepage access
•	WordPress admin access
•	siteurl value
•	home value

