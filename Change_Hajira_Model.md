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
6. After opening the file, make sure the `self.model_path` is referencing the path to new model path. 
Example: 
``` python3
 self.model_path = config['models_path']['face_recognition']['new_model_name']['path']+ '/' + config['models_path']['face_recognition']['new_model_name']['weight']` '
```
7. Make sure new model is loaded before inference; you may need to import a script that contains backbone of the new model. 



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
Make changes in config file just like in local machine part(Step:1 to Step:4) & add new line which points to new model weights folder(Step:5 to Step:6).
*Note: Azure Virtual Machine path will be different than local machine. It should look like: `/Hajira_ML_Cloud/backend/`

    
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
We have to update images of every single employee using the web HRM system of hajira (hajira.cpsd.com) and manually update employee images.
