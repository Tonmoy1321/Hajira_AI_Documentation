Step 1.1: Collect the private key file from the admin. Save it somewhere secured. Let's assume the file is saved at: 
```
home/username
```

Step 1.2: Connect to the virtual machine where the machine learning model is deployed. In order to connect to the virtual machine at first open a terminal. change directory to where the file is located. 
For example: 
```
cd home/username
```
step 1.3: change the file permission to READ.
In order to do that, RUN:
 ```
sudo chmod 400 file_name.pem
```
Step 1.4: To connect your pc to the azure server, run:
```
ssh -i file_name.pem azureuser@ip_address
```
* This makes azure accessible through your local terminal
* please note the public IP address provided when VM was created
* If you dont have the IP address ask the admin.

Step-2.1: Installing Apache and Updating the Firewall
Run the following commands on terminal:
```
sudo apt update
sudo apt install apache2
sudo systemctl start apache2
sudo systemctl enable apache2
sudo ufw allow “Apache Full”
sudo systemctl restart apache2
```
Step-2.2: Installing MySQL and creating a DB
Command for installing MySQl:
```
sudo apt install mysql-server
```
Step-2.3:Creating Database:
```
sudo  mysql -u root -p
CREATE DATABASE hajira_db;
CREATE USER 'hajira_user'@'localhost' IDENTIFIED BY 'secretpassword';
GRANT ALL ON hajira_db.* TO 'hajira_user'@'localhost';
FLUSH PRIVILEGES;
QUIT
```

Step-2.4:Install PHP 8.1:
```
sudo apt-get install php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-redis php8.1-intl -y
```

Step-2.5:Download the project folder:


Step-2.6:Install composer:
```
	sudo composer install 
```
Step-2.6:Then create a duplicate of env file by typing:
```
	sudo cp .env.example .env
```
Step-2.7:Open the .env file by typing:
```
	sudo nano .env
```

Step-2.8:Add IP of the virtual machine on APP_URL key.
```
	Example: APP_URL=http://4.xxx.xx.xxx/
```
Step-2.9:Then type:
```
php artisan key:generate
php artisan config:cache
php artisan storage:link
```
Step-2.10: Open sql terminal by typing:
```
sudo mysql -u root -p
```
Step-2.11: On the terminal run the following queries:

```
CREATE DATABASE laravel CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
DROP USER ‘root’@’localhost’;
CREATE USER	‘root’@’%’ IDENTIFIED BY ‘’;
GRANT ALL PRIVILEGES ON *.* TO ‘root’@’%’ WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```

Step-2.12:Then give write permission to storage folder. Make sure you are on /var/www/html/soft-ui-hrm/public folder:
```
	Sudo chmod 777 -R /storage
```
Step-2.13:Then run:
```
php artisan migrate:fresh --seed
```
Step-2.14:Change directory to:
```
cd /etc/apache2/sites-available/
```

Step-2.15:Then make the necessary changes in the conf file.
    - open the conf file by running the following command:
```
Sudo nano 000-default.conf
```
- Then make chages by as below: 
```
ServerAdmin webmaster@localhost
DocumentRoot /var/www/html/soft-ui-hrm/public
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
```
Step-2.16: After making changes run:
```
Sudo systemctl reload apache2
Sudo a2enmod rewrite
Sudo systemctl reload. 
```
Step-2.17: Then open apche config file by running the following command:
```
sudo nano /etc/apache2/apache2.conf
```
Step-2.18: On the file under <Directory /var/www/> change AllowOverride to ALL as stated below. 
```
<Directory /var/www/>
    	Options Indexes FollowSymLinks
    	AllowOverride All
    	Require all granted
</Directory>
```
Step-2.19: Then change directory to soft-ui-hrm folder. 
```
cd /var/www/html/soft-ui-hrm
```
Step-2.20:Give write permission to storage folder by running the following command. 
```
Sudo chmod 777 -R storage/
```
Step-2.21: Then change directory to Controllers folder by typing the following command.
```
cd app/Http/Controllers/
```
Then open the  EmployeeController.php file by typing :
```
sudo nano EmployeeController.php
```

- On **updateFace** and **registerFace** function change the ip on the **responsible** variable with the VM ip of the ML virtual machine .


Step-2.22: Now open .env file by typing the following command:
```
nano .env
```

Change  **FACE_API_SECRET** = token of the facebank where we will store our embedding.

Step-2.23: Then type
```
Sudo php artisan config:cache
```
Now check if the website loads perfectly by trying to load the url on the browser:
http://4.xxx.xx.xx/employees
