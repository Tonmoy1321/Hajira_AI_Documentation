# Hajira Face Recognition Attendance System
## _How to change model in local machine_

## Steps 
1. Go to location: `Local_Hajira_lite_Dev/backend/checkpoints/facerecognition` by opening the terminal and running command:
```bash
cd /home/username/Local_Hajira_lite_Dev/backend/checkpoints/facerecognition
```
2. Then add a new folder using command:
```bash
mkdir new_model_folder 
``` 
Note: Here, new_model_folder is just a placeholder for your folder name.

3. Go to location: `Local_Hajira_lite_Dev/backend/src/config` and open `config.py`.
4. Inside the config file under `config['models_path']['face_recognition']` add a new line with preferably new model name as key, which will consist of a dictionary with paths to new model weights and model file name.
Example:
```
config = {
        "models_path": {
            "face_recognition": {
                "arcface_ncnn": {"weight": "r100", "path": "checkpoints/facerecognition/arcface_ncnn" },
                "ir50_torch":{"weight": "backbone_ir50_ms1m_epoch120.pth", "path": "checkpoints/facerecognition/ir50_torch"}, 
                "new_model_name":{"weight": "new_model.pth", "path": "checkpoints/facerecognition/new_model_folder"}
                },........

```

5. Then go to location: `Local_Hajira_lite_Dev/backend/src/libs/facerecognition` and open the `Learner.py` file.
6. After opening the file, make sure the `self.model_path` is written in a way that uses config file to read the path to new model weight folder. 
Example: 
``` python3
 self.model_path = config['models_path']['face_recognition']['new_model_name']['path']+ '/' + config['models_path']['face_recognition']['new_model_name']['weight']` '
```
7. Make sure new model is loaded before inference; you may need to import a script that contains backbone of the new model. 
8. Finally, DO NOT FORGET to replace the existing Facebank to a new fresh Facebank by going to the location: `Local_Hajira_lite_Dev/backend/checkpoints/facebank/fresh facebank`, copy `company_name.npy` and `company_name_embed.npy` file; then paste them in the following location: `Local_Hajira_lite_Dev/backend/checkpoints/facebank`. 
9. After pasting the fresh facebank in the correct directory, go to location: `var/www/html/face-attendance-local/storage/app/public/users` 
```bash
cd var/www/html/face-attendance-local/storage/app/public/users
``` 
Stop all the supversior workers by running the command:
```bash
sudo supervisorctl stop all
```
All registered employee images for that company is saved here, delete all of them.
10. Then open terminal and run:
```bash
cd /var/www/html/face-attendance-local
``` 
After current working location is changed, then cache and migrate fresh seed using(this will clean the existing local database):
```bash
php artisan config:cache 
```
```bash
php artisan migrate:fresh --seed 
``` 
11. Restart the supervisor workers `when model is also changed on cloud` instance. Restart supervisor workers by running the following command: 
```bash
sudo supervisorctl restart all
```


## How to change model in Cloud:

## Steps 
Step 1: Collect the private key file from the admin. Save it somewhere secured. Let's assume the file is saved at: 
```
home/username
```

Step 2: Connect to the virtual machine where the machine learning model is deployed. In order to connect to the virtual machine at first open a terminal. change directory to where the file is located. 
For example: 
```
cd home/username
```
step 3: change the file permission to READ.
In order to do that, RUN:
 ```
sudo chmod 400 file_name.pem
```
Step 4: To connect your pc to the azure server, run:
```
ssh -i file_name.pem azureuser@ip_address
```
* This makes azure accessible through your local terminal
* please note the public IP address provided when VM was created
* If you dont have the IP address ask the admin.

Step 4.1: Install miniconda and other system dependencies 
Run the following commands one by one in your azure vm:
```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
``` 
`Note:` Install other pyhton modules required by checking requirements.txt file on t.ly/Ysh_  
* `If pytorch cpu version doesn't install with pip then:` 
```bash
conda install pytorch==1.10.0 torchvision==0.11.0 torchaudio==0.10.0 cpuonly -c pytorch 
```
* `If there is a error while running a python file, error is somewhat like: "ImportError: libGL.so.1: cannot open shared object file: No such file or directory"`; then run: 
```bash 
sudo apt-get install ffmpeg libsm6 libxext6  -y
```
* Then we have to install onnx: 
```
pip install onnx && pip install onnxruntime 
```

Step 5: Upload the pre-trained model file on the cloud by running the following command:
``` 
scp -i file_name.pem home/path_of_project.zip azureuser@ip_address:/home/azureuser/green_revolution/backend/checkpoints/facerecognition/model_folder
```
Step: 6 Unzip model folder
```bash 
cd /home/azureuser/green_revolution/backend/checkpoints/facerecognition/model_folder
``` 
Then run the command:
```bash 
unzip path_of_project.zip && sudo rm -rf path_of_project.zip
```
Note: Make sure unzip is installed in azure virtual machine. If not please install unzip.

Step 7: Make changes in config file 
Make changes in config file just like in local machine part. Add new line which points to new model weights folder.
    
Step 8: Make changes in face recognition script
Go to location where `Learner.py` script is located in the azure virtual machine.
Example: `/home/azureuser/Hajira_ML_Cloud/backend/src/libs/facerecognition`
To open and make changes to `Learner.py` use nano: 
```bash
sudo nano Learner.py
```

Step: 9 Restart the supervisor by running the following command:
```
sudo supervisorctl restart all
```

# Making changes in user information 
Right now the facebank is empty and the attendance system will not work. To re-populate the new facebank with new model embeds we need to go to web HRM system of hajira (hajira.cpsd.com) and manually chnage image to update embeddings of the employees. 
