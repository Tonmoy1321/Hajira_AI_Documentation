# Hajira

Hajira is a ML based attendance system, which enables user to setup a seamless attendance system using only 2 IP camera.

## Step-1: Install packages

#### Step-1.1: Download Anaconda/Miniconda and Install it with python 3.8

Firstly, Install Anaconda/Miniconda: open terminal and download miniconda

```
  wget https://repo.anaconda.com/miniconda/Miniconda3-py38_4.10.3-Linux-x86_64.sh
```

#### Step-1.2:

After download Miniconda, install it by typing

```
bash Miniconda3-py38_4.<span class="hljs-number">10.3</span>-Linux-x86_64.sh
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

* Download requirements.txt from >= [File Link](https://drive.google.com/file/d/1cX3p9hB-SIHsg7dMWFu-_DCxoVRIxO9a/view?usp=sharing)
* Then run the command:

```
pip install requirements.txt
```

#### Step-2.2(Optional):

* If there is an error to import opencv, simply run following command:

```
sudo apt-get update
sudo apt-get install ffmpeg libsm6 libxext6  -y
```

#### Step-2.3:

* Install Supervisor globally:

```
sudo apt install supervisor
sudo apt update
```

#### Step-2.4:

* Install Cron globally:

```
sudo apt install cron
```

## Step-3: Automating Hajira using CronJob
The cron command-line utility is a job scheduler on Unix-like operating systems. Users who set up and maintain software environments use cron to schedule jobs, also known as cron jobs, to run periodically at fixed times, dates, or intervals.

Steps is given below,

* Make a Launcher Sript
* Make It Executable
* Add Logs Directory

#### Step-3.1: Make a Launcher Sript

Let's create the shell script!

This bash file needs for startup running so that it will run automatically if it restarted.

create bash file in terminal

```
touch RUN.sh
```

Then, edit with following commands in `RUN.sh`

```
#!/bin/bash</span>
source /home/gr/miniconda3/bin/activate gr
cd /
cd /home/gr/Local_Hajira_lite_Dev/backend
# path where the code exist
sudo python3 cam1.py &
sudo python3 cam2.py &
cd /
```

Note: in base file, opencv and easydict may not be found while the python file running in bash. So, we need to append opencv and easydict path to program.

Add this path upto `site-packages` on top of `cam1.py1` and `cam2.py` files

``` pyhton
import sys
sys.path.append('/home/gr/miniconda3/envs/gr/lib/python3.8/site-packages')
```
#### Step 3.2: Make It Executable

We need to make the launcher script an executable, which we do with this command,

```
chmod 755 RUN.sh
```

Now test it, by typing in:

```
bash RUN.sh
```

For exit, type and enter:

```
Ctrl+C
```

#### Step 3.3: Add Logs Directory

We need to make a directory for the any errors in crontab to be logged.<br>

Go to Path:

```
/home/gr/Local_Hajira_lite_Dev/backend
```

Create a logs directory by opening terminal in directory.<br>

Then use the following command:

```
mkdir logs
```

the directory should have a PATH:

```
/home/cpsd/Local_Hajira_lite_Dev1/backend/logs
```

#### Step 4: Add task to Your Crontab

Type in termianl:

```
sudo crontab -e
```

This will bring up a crontab window.

Now, enter the line:

```
@reboot bash /home/gr/Local_Hajira_lite_Dev/RUN.sh >/home/gr/Local_Hajira_lite_Dev/logs/cronlog 2&1
```

Note: there is two paths. First one idicates to the `RUN.sh` file and second one to the logs folder.

## Step-5: Setting UP Supervisor

Supervisor allows to monitor and control a number of processes on UNIX-like operating systems.

#### Step-5.1: Configured supervisor file

* Create a supervisor file in `/etc/supervisor/conf.d/` and configure it according to your requirements.
* Type following command to create

```
sudo nano /etc/supervisor/conf.d/gr.conf
```

The file should lool like:

```
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

```
sudo supervisorctl reread
sudo service supervisor restart
```

This should start a new process. To check the status of all monitored apps, use the following command:

```
sudo supervisorctl status
```

Important: if you make any changes in the code and want to get updated results, you need to restart the supervisor by following commands:

```
sudo supervisorctl restart all
```

## Step-6: Setup nginx

Nginx is an HTTP and reverse proxy server.

Before proceed, make sure that ports like 80 or 8080 are opened and generated by admin of the vm.

Let's define a server block for our flask app.

```
sudo nano /etc/nginx/conf.d/virtual.conf
```

Modify the file as below:
```bash
server {
    listen       80;
    server_name  52.163.71.151;

    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}
```

Note: `server_name` is basically the public ip of the virtual machine and proxy_pass is the refer to the flask app with ports running.

Proxy pass directive must be the same port on which the gunicorn process is listening.

Restart the nginx web server.

```
sudo nginx -t
sudo service nginx restart
```

Congratulations! Now the Flask app is successfully deployed using a configured nginx, gunicorn and supervisor and ready to serve.
