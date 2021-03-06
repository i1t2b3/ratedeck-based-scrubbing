# ratedeck-based-scrubbing
Scrubbing of phone numbers based on a given ratedeck and blacklist

Command line:
```
composer install
mkdir -p temp/csv/
mkdir -p temp/sessions/
chmod -R a+w temp
```

Find the PHP config location and edit the given file:
```
php --ini | grep Loaded
```

Edit this settings as a ROOT:
* short_open_tag = On
* memory_limit = 2Gb
* post_max_size = 2Gb
* upload_max_filesize = 2Gb

Create a web file "test.php", write this inside and open it in a browser:
```
<?php 
phpinfo();
```

Copy the `ini` file location there and make the same changes.

```
/etc/init.d/apache2 restart
```

MySQL:
* make sure this returns empty value:
```
SHOW VARIABLES LIKE "secure_file_priv"
```
If not, For Ubuntu, edit the file `/etc/mysql/mysql.conf.d/mysqld.cnf` and add the following line at the end:
```
secure_file_priv=""
```
Then make sure to restart the service

* create database `ratedeck`
* 
```
CREATE USER 'ratedeck'@'localhost'  IDENTIFIED BY 'pass';
GRANT ALL PRIVILEGES ON ratedeck.* TO 'ratedeck'@'localhost' IDENTIFIED BY 'pass';
GRANT FILE ON *.* TO 'ratedeck'@'localhost' IDENTIFIED BY 'pass';
FLUSH PRIVILEGES;
```
* edit `/etc/apparmor.d/usr.sbin.mysqld`
add this line somewhere below:
```
  /var/www/html/ratedeck/temp/csv/ rw,
```
and then restart the service:
```
sudo /etc/init.d/apparmor reload
```

Add this to Crontab (`crontab -e`):
```
* * * * * wget --spider http://199.168.97.106/ratedeck/index.php?page=process-queue
```

Troubleshooting:
* if emails from Gmail account fail to run, visit this page to unlock it — https://accounts.google.com/b/4/DisplayUnlockCaptcha
Number 4 means it's your 4th open Gmail account (you can get this number from your open Gmail page).
