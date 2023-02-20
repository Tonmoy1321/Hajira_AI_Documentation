# Hajira
Hajira is a ML based attendance system, which enables user to setup a seamless attendance system using only 2 IP camera.
[![GR](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)
## Step-1: Creating a virtual environment
#
#### Step-1.1: Download Miniconda and Install it with python 3.8.13
Firstly, Install Anaconda/Miniconda: open terminal and download miniconda
``` 
  wget https://repo.anaconda.com/miniconda/Miniconda3-py38_4.10.3-Linux-x86_64.sh
``` 
#### Step-1.2:
After download Miniconda, install it by typing
```bash
bash Miniconda3-py38_4.10.3-Linux-x86_64.sh
```
#### Step-1.3:
Create an Environment (eg: gr)
```
conda create -n gr python=3.8.13
```
#### Step-1.4:
Activate Environment:
```
conda activate gr
```

## Step-2: Install required packages
#### Step-2.1:
- Download requirements.txt from >= [`Drive Link`](https://drive.google.com/file/d/1cX3p9hB-SIHsg7dMWFu-_DCxoVRIxO9a/view?usp=sharing)
- Then run the command: 
``` bash
pip install -r requirements.txt
```
#### Step-2.2(Optional):
- If there is an error to import opencv, simply run following command:
```bash
sudo apt-get update
sudo apt-get install ffmpeg libsm6 libxext6  -y
``` 
#### Step-2.3: 
- Install Supervisor globally: 
```bash
sudo apt install supervisor
sudo apt update
``` 
#### Step-2.4(Optional):
- Install Cron globally:
```bash
sudo apt install cron
```
Note: In most cases you won't have to do this cause cron is already bundled with Ubuntu 20.04 LTS.
## Step-3: Automating Hajira using CronJob
The cron command-line utility is a job scheduler on Unix-like operating systems. Users who set up and maintain software environments use cron to schedule jobs, also known as cron jobs, to run periodically at fixed times, dates, or intervals.
Steps is given below,
- Make a Launcher Sript
- Make It Executable
- Add Logs Directory

#### Step-3.1: Make a Launcher Sript
Let's create the shell script!
This bash file needs for startup running so that it will run automatically if it restarted.

create bash file in terminal
```bash
touch RUN.sh
```

Then, edit with following commands in `RUN.sh`
```bash
#!/bin/bash
source /home/gr/miniconda3/bin/activate gr
cd /
cd /home/gr/Local_Hajira_lite_Dev/backend
# path where the code exist
sudo python3 cam1.py --accessway "in" &
sudo python3 cam2.py --accessway "out" &
cd /
```

Note: in base file, opencv and easydict may not be found while the python file running in bash. So, we need to append opencv and easydict path to program.

Add this path upto ```site-packages``` on top of `cam1.py` and `cam2.py` files 
```python
import sys
sys.path.append('/home/gr/miniconda3/envs/gr/lib/python3.8/site-packages')
```
#### Step 3.2: Make It Executable
We need to make the launcher script an executable, which we do with this command,
```bash
chmod 755 RUN.sh
```
Now test it, by typing in:
```bash
bash RUN.sh
```
For exit, type and enter:
```bash
Ctrl+C
```

#### Step 3.3: Add Logs Directory
We need to make a directory for the any errors in crontab to be logged.
Go to Path:
```
/home/gr/Local_Hajira_lite_Dev/backend
```
Create a logs directory by opening terminal in directory. 
Then use the following command:
```
mkdir logs
```
the directory should have a PATH:
```
/home/gr/Local_Hajira_lite_Dev/backend/logs
```

#### Step 4: Add task to Your Crontab
Type in termianl:
```bash
sudo crontab -e
```
This will bring up a crontab window.

Now, enter the line:
```bash
@reboot bash /home/gr/Local_Hajira_lite_Dev/RUN.sh >/home/gr/Local_Hajira_lite_Dev/backend/logs/cronlog 2>&1
```
Note: there is two paths. First one idicates to the RUN.sh file and second one to the logs folder.

Now, RUN:
```bash
crontab -e
```
Then add the line:
```bash
 * * * * * cd /var/www/html/face-attendance-local && php artisan schedule:run >> /dev/null 2>&1
```

## Step-5: Setting UP Supervisor
Supervisor allows to monitor and control a number of processes on UNIX-like operating systems.
#### Step-5.1: Configured supervisor file
- Create a supervisor file in ``` /etc/supervisor/conf.d/``` and configure it according to your requirements.
- Type following command to create
```python 
sudo nano /etc/supervisor/conf.d/gr.conf
```

The file should lool like:
```python
[program:gr]
directory=/home/gr/Face-Recognition-API/backend
environment=PATH=/home/gr/miniconda3/envs/gr/bin
command=/home/gr/miniconda3/envs/gr/bin/gunicorn app:app -b localhost:8080
autostart=true
autorestart=true
stderr_logfile=/home/gr/Face-Recognition-API/backend/logs/err.err.log
stdout_logfile=/home/gr/Face-Recognition-API/backend/logs/err.out.log
```
Note: Edit the path as your requirments and make sure that you created logs folder.

#### Step-5.2: To enable the configuration, run the following commands:
```bash
sudo supervisorctl reread
sudo service supervisor restart
```
This should start a new process. To check the status of all monitored apps, use the following command:
```bash
sudo supervisorctl status
```
Important: if you make any changes in the code and want to get updated results, you need to restart the supervisor by following commands:
```bash
sudo supervisorctl restart all
``` 
#
## Step-6: Local Web Part 
#### Step-6.1: Installing Apache and Updating the Firewall 
Run the following commands on terminal:

```bash
sudo apt update
sudo apt install apache2
sudo systemctl start apache2
sudo systemctl enable apache2
sudo ufw allow “Apache Full”
sudo systemctl restart apache2
``` 
#### Step-6.2: Installing MySQL and creating a DB
Command for installing MySQl:
```bash
sudo apt install mysql-server
```
Creating Database: 
```bash
sudo  mysql -u root -p
CREATE DATABASE hajira_db;
CREATE USER 'hajira_user'@'localhost' IDENTIFIED BY 'secretpassword';
GRANT ALL ON hajira_db.* TO 'hajira_user'@'localhost';
FLUSH PRIVILEGES;
QUIT
```
#### Step-7.2: Installing PHP and additional PHP extensions
Run following command in terminal:
```bash
sudo apt-get install apache2 php7.4 libapache2-mod-php7.4 php7.4-curl php-pear php7.4-gd php7.4-dev php7.4-zip php7.4-mbstring php7.4-mysql php7.4-xml curl -y
``` 

#### Step-6.3: Installing Composer 
```bash
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
sudo chmod +x /usr/local/bin/composer
```

#### Step-6.4: Virtual host setup
Navigate to desired path using terminal:
```bash 
cd /etc/apache2/sites-available
````
After directory is opened then run: 
```bash
sudo touch local-face.test.conf
sudo gedit local-face.test.conf
``` 
Then write the following code: 
```
<VirtualHost *:80>
	ServerName local-face.test
	DocumentRoot /var/www/html/face-attendance-local/public
	<Directory "/var/www/html/face-attendance-local">
		Require all granted
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
``` 
After setting up virtual host, run:
```bash
sudo systemctl reload apache2
sudo a2ensite local-face.test
sudo gedit /etc/hosts 127.0.0.1 local-face.test
sudo systemctl reload apache2
sudo a2enmod rewrite
``` 
Changing directive in conf file:
```bash
sudo gedit /etc/apache2/apache2.conf
```
Changing the `AllowOverride` directive for the /var/www directory to `AllowOverride All`
```bash
sudo systemctl reload apache2
```` 
#### Step-6.5: .env setup for local server
First collect the required folder from admin and paste them inside the following directory: `/var/www/html/face-attendance-local`.

Then go to directory: `path: /var/www/html/face-attendance-local` using:
```bash
cd /var/www/html/face-attendance-local
```
Open .env using a editor and add the following lines:
```php
APP_DEBUG=true
APP_URL=http://local-face.test/
MONITOR_TOKEN=1a2b3c4d5e6f7g8h9i
LOCAL_ML_URL=http://127.0.0.1:8001/

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=hajira_db
DB_USERNAME=hajira_user
DB_PASSWORD='secretpassword'
```
Then open terminal and run commands:
```bash
php artisan config:cache
php artisan migrate:fresh --seed
``` 
#### Step-6.6: Cron setup 
Terminal Commands:
```bash
crontab -e
```
Add the following line:
```
cd /var/www/html/face-attendance-local && php artisan schedule:run >> /dev/null 2>&1
``` 
#### Step-6.7: Deploy Supervisor 
* Deploy the facesocket and faceworker in supervisor 

Copy facesocket.conf and faceworker.conf file from dev pc or provided link, then paste them in path:`/etc/supervisor/conf.d/`
```bash
cd /etc/supervisor/conf.d/
```
Then open the directory using nautilus:
```bash 
nautilus .
```
When the directory is opened, download/copy the `faceworker.conf` and `facesocket.conf` into the directory. Download link: [`Drive Link`](https://drive.google.com/drive/folders/1_JUYjubSQ_UffO0PG9wV_H9c7n-Fdk5h?usp=sharing)
Then Run:
```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl restart all
```

## Step-7: After setting up Hajira on machine
We have to generate a new access key for the company being added to Facebank.
Steps: 
* Create a facebank on the web ml for the company.
* Create a token for the company.
* Add the token and facebank to config file on cloud. Facebank name on facebank list and token on token section.  
* Clean local web database. 
* Create a facebank locally.

## Extra Tips
* If images do not appear after syncing or can't copy any files/move files to any `folder`,
Open terminal in that directory and Run: 
```bash
sudo chmod 777 -R `folder/`
``` 
* If you fail to see .env or any other files in `/var/www/html/face-attendance-local`; they are most likely hidden. To see all hidden files inside a directory press: `ctrl+H`. 
* Link to Fresh Facebank => [`Fresh Facebank`](https://drive.google.com/drive/folders/1y35Xj48E4BDEIrkf7XQ8bs-Ki7f2hdXV?usp=sharing)
