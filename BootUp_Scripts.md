# How to run this

## Requirments
Checked this configuaration on PC
- Python>=3.8
- opencv-python >= 4.5.4.60

## Install in ubuntu

### Configuartion Details

```python
"image_data":   {
            "save_data" : True, #set it to True to save data in local device
            "save_unknown_faces" : True, #set it to True to save unknown faces
            "save_data_label" : True,  #
            "save_path": os.getcwd() + '/data/', #set save path location    
            "in_rec_dur": [[16,13],[16,50]], #set in camera record duration 
            "out_rec_dur": [[16,13],[16,50]], # set out camera record duration
        },
 "camera_setting" : {
            "in": rtsp://admin:admin@x.x.x.xx:554/cam/realmonitor?channel=1&subtype=1, #set entrance camera
            "out": rtsp://admin:admin@x.x.x.xx:554/cam/realmonitor?channel=1&subtype=1,#set exit camera
            "camera_resolution": (640, 480), #(1280, 720) #set resolution
        }
```

## Auto Start Python Script on Boot
Steps is given below,
- Make a Launcher Sript
- Make It Executable
- Add Logs Directory


#### Step 1: Make a Launcher Sript
Let's create the shell script!
This bash file needs for startup running so that it will run automatically if it restarted.

create bash file in terminal
```bash
touch RUN.sh
```


Then, edit with following commands in RUN.sh
```bash
#!/bin/bash
source /home/cpsd/miniconda3/bin/activate cpsd
cd /
cd /home/cpsd/Local_Hajira_lite_Dev/backend
# path where the code exist
sudo python3 cam1.py &
sudo python3 cam2.py &
cd /
```

Note: in base file, opencv and easydict may not be found while the python file running in bash. So, we need to append 
opencv and easydict path to program.

Find out the opencv path,
```bash
python3
>>> import cv2
>>> cv2.data
```

Add this path upto ```site-packages``` on top of "cam1.py and cam2.py" files 
```python
import sys
sys.path.append('/home/cpsd/miniconda3/envs/cpsd/lib/python3.8/site-packages')
```
#### Step 2: Make It Executable
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

#### Step 3: Add Logs Directory
we need to make a directory for the any errors in crontab to go.

Create a logs directory:
```
mkdir logs
```
for example,
```
/home/cpsd/Local_Hajira_lite_Dev1/backend/logs
```

#### Step 4: Add to Your Crontab
crontab is a background (daemon) process that lets you execute scripts at specific times. It's essential to Python and Raspberry Pi/Jetson Nano. The details are confusing, as is often the case with Linux. Once I got the hang of the format, I've found it to be incredibly easy to use.
Type in:
```bash
sudo crontab -e
```
This will brings up a crontab window.

Now, enter the line:
```bash
@reboot bash /home/cpsd/Local_Hajira_lite_Dev/RUN.sh >/home/cpsd/Local_Hajira_lite_Dev/logs/cronlog 2>&1
```
Note: there is two paths. First one idicates to the RUN.sh file and second one to the logs folder


#### ADD monitor screen while reboot the PC/Device

Steps:
- Goto Menu (9 dot left-bottom in Ubuntu)
- Search Startup and open SatartUp Application
- Click on "Add"
- Fill the box using following data. 
- Name: fire-fox
- Command: firefox -kiosk http://20.212.196.191/monitors?email=monitor-6200e958b13a3@email.com
- Comment: open firefox
- DONE!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Check the monitor email of the company that you want to deploy
