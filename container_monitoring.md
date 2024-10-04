# Container Monitoring and Alerting Using Prometheous and Garafana

## Step 1:
Set the Ports for Instance 
PORTS - 9393,3000,9090

## Step 2:
Update the Packages
```
sudo apt update -y
```
## Step 3:
Install docker and Docker compose 
```
sudo apt-get install -y docker.io
sudo curl -L "https://github.com/docker/compose/releases/download/v2.21.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Step 4:
Install Prometheous 
```
wget https://github.com/prometheus/prometheus/releases/download/v2.30.0/prometheus-2.30.0.linux-amd64.tar.gz
```
```
tar xvfz prometheus-2.30.0.linux-amd64.tar.gz
```
```
cd /home/ubuntu/prometheus-2.30.0.linux-amd64/ && ./prometheus --config.file=prometheus.yml &
```

## Step 5:
go to browser and give the public ip of the server and put the port 9090
```
http://<public-IP>:9090
```

## Step 6:
Install Grafana
```
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -a
```
```
sudo add-apt-repository -y "deb https://packages.grafana.com/oss/deb stable main"
```
```
sudo apt update
```
```
sudo apt install grafana -y
```
```
sudo systemctl start grafana-server
```
```
sudo systemctl enable grafana-server
```
## Step 7:
Go to browser and give the public ip of the server and put the port 300
```
http://<public-IP>:3000
```
## Step 8:
Go to the docker directory in the server
```
cd /etc/docker
```
```
sudo nano daemon.json
```
Insert the command in the daemon.json file 
```
#insert the file
{
  "experimental": true,
  "metrics-addr": "0.0.0.0:9393"
}
```
```
sudo systemctl restart docker
```
## Step 8:
Then Edit the promethous.yml file and copy paste the below texts in the file and put our Public IP address in the  

```
sudo nano /home/ubuntu/prometheus-2.30.0.linux-amd64/prometheus.yml
```
```
# my global config
global:
  scrape_interval: 15s
  evaluation_interval: 15s

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape: Prometheus itself.
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "Docker Job"
    static_configs:
      - targets: ["Public IP:9393"] #put our public ip address
```

## Step 9:

Now go again in browser check Prometheus whether target resource is up or not, 
Prometheus > Status > targets

if you dont see any targets then put the following command in the server

## Step 10:
```
ps aux | grep prometheus
```
```
kill -SIGTERM <PID>
```
## Step 11:
Restart the prometheous once again after killing it

```
cd /home/ubuntu/prometheus-2.30.0.linux-amd64/ && ./prometheus --config.file=prometheus.yml &
```
## Step 12:

Then go to grafana login with credentials 

```
http://<public-IP>:3000
```
Username and Password for grafana is admin, admin

## Step 13:

Then click on “Administration => Data Source => Prometheus => and add Prometheus url”

## Step 14:
Then click on “Data Source”

Click on Add Data Source and select “Prometheus”

In setting paste Prometheus URL and keep other setting as by default and click “Save and Test”

## Step 15:

After click on “Save and Test” you will see below output,
it shows that the data source is working

## Step 16:

Now go to dashboard and in right hand corner click on “+” icon =>New Dashboard => Add visualisation

In the Metrics browser section add below query. The below query will fetch the number of running containers.

engine_daemon_container_states_containers{state="running"}

## Step 17:

Then click on apply and save and again click on “+” icon

## Step 18:
Then go to the gmail account ->security -> enable two steps authentication of the gmail -> then check there will be the app password available on the UI or enter the link

```
https://myaccount.google.com/apppasswords 
```

## STep 19:

create a new Password for username Grafana, copy and paste it in the ssmtp file for email verification

then go to the server
```
sudo apt update
```

Install smtp in our server
```
sudo apt install ssmtp mailutils -y
```

Edit the Grafana.ini file for mail alerts
```
sudo nano /etc/grafana/grafana.ini
```
```
#################################### SMTP / Emailing ##########################
[smtp]
enabled = true
host = smtp.gmail.com:587
user = muthulakshmananj007@gmail.com #user email
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""
password = xqod yids eodc vpin #user app password form gmail account
;cert_file =
;key_file =
skip_verify = true
from_address = muthulakshmananj007@gmail.com
from_name = Grafana
# EHLO identity in SMTP dialog (defaults to instance_name)
;ehlo_identity = dashboard.example.com
# SMTP startTLS policy (defaults to 'OpportunisticStartTLS')
;startTLS_policy = NoStartTLS
# Enable trace propagation in e-mail headers, using the 'traceparent', 'tracestate' and (optionally) 'baggage' fields (defaults to false)
;enable_tracing = false

```

Then restart the grafana-server
```
sudo systemctl restart grafana-server
```

## Step 20: 
Go to Grafana -> home -> dashboard -> create dashboard -> add visualization -> prometheous -> configure new data source -> copy paste the url 
http://<Public-ip>:9090

## Step 21:
Create a contact point and enter the mail and test the notification then save the contact point

## Step 22:
Create a contact point and choose the slack 

## Step 23:
go to the link:
https://api.slack.com/apps

## Step 24:
1. Create a new app in the slack api

2. Select from sratch 

3. Enter app name 

4. Enter the workspace 

5. After the app is created go to the incoming webooks in the left navigation panel

6. Then, Activate the incoming webhooks

7. Then click add new webhook to the workspace 

8. copy paste the webhook url in Grafana contact point in the grafana server


## Step 25:
1. Create a new alert rule in the grafana server

2. select the query 

3. engine_daemon_container_states_containers = state = stopped

4. select evaluation behaviour

5. Create a new folder 

6. create a new evaluation group

7. set timer to 1 minutes

8. Select the contact point which we have created 

9. Then save and exit

## Step 27:
Manually stop the container in the server check whether it is working or not in the server

## Email Alert testing
![Screenshot (34)](https://github.com/user-attachments/assets/f92afdbc-de23-4229-85c9-7ffe8af7515d)

## Slack Alert Testing 
![Screenshot (37)](https://github.com/user-attachments/assets/6eb8ca65-ccda-445f-9034-102eac71fe95)

## Firing an alert if an container is stopped 
![Screenshot (36)](https://github.com/user-attachments/assets/3c2c149b-c6e2-4af0-89e9-3dd5866af92e)

