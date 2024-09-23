# Wazuh Server

## Packages
### Step1 : 
Install necessary packages like docker, docker Compose and git

```
sudo apt install git -y
```
```
sudo apt-get install -y docker.i
```
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.21.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
### Step2 :
Clone this repository for running wazuh server as container 

```
git clone https://github.com/wazuh/wazuh-docker.git -b v4.8.0 /home/ubuntu/wazuh-docker
```
### Step3 :
Go to the wazuh-docker directory and open the single-node and run the following command 
```
sudo docker-compose -f generate-indexer-certs.yml run --rm generator
```

### Step4 :
After Installing the certificates run the below command, 
```
sudo docker-compose -f docker-compose.yml up -d
```
### Step5:
Open the Wazuh dashboard by copy paste the public ip in the browser, The password for the wazuh agent
admin - kibanaserver
password - kibanaserver

### Step6 :
Click to add a new agent in the wazuh dashboard

### Step7 :
Got to the instance which we have created, Copy the following command and paste it in the client machine.

### Step8 :
After installing the agent, run the below command 
```
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

### Step9 :
Navigate to the below folder and edit the file
```
sudo nano /var/ossec/etc/ossec.conf
```

### Step10 :
In the address column, enter the  wazuh manager IP(Where the wazuh is installed)
```
<address>MANAGER_IP</address>
```

### Step11 :
Restart the wazuh agent by using the below command, 
```
sudo systemctl restart wazuh-agent
```
