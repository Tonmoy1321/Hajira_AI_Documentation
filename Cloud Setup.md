# How to create a server on Azure
        NB: Always choose the nearest location while creating the server.
Please follow the instructions below to set up the ML server on an ubuntu virtual machine in Azure
cloud.
1.  Login to azure portal
2. Create a virtual Machine (VM)
    * choose a resource group
    * Set a virtual machine name
    - Click Next: **Disk**
    - Click on create and attach a new disk and select your preferred size.
    - Click next: **Management**
    - Click next: **Advanced**
    - Click next: **Tags**
    - Click next: **Review and Create**
    - Click **create**

3. After creating the server go to **Networking** tab from setting.
    - Click on **Add inbound port rule**
    - Change the Name to **Port_80**
    - Change the **Destination port ranges** to **80**
    - Then click **Add**

4. Download private key and create resources
    - Safely store the private key file. (important)
    - Extension should be **‘file_name.pem’**

5. In your computer, run the following command
```
sudo chmod 400 file_name.pem
```

6. To connect your pc to the azure server, run:
```
ssh -i file_name.pem azureuser@ip_address
```
* This makes azure accessible through your local terminal
* please note the public IP address provided when VM was created
7. To upload project, zip the folder and run the command:
``` 
scp -i file_name.pem home/path_of_project.zip azureuser@ip_address:
```
* NB: the colon after the command is important
* Once the command is run, the project folder is uploaded in the VM
