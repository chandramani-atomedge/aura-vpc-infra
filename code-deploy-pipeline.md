### Code Deploy and Code Pipeline

## Step 1:
Create a role for EC2 service, and that role should contain those policies like

![image](https://github.com/user-attachments/assets/c1a28872-dc64-4e4e-a66b-8cf2cfb3c7e0)

## Step 2:
Create a role for Code Deploy service, and that role should contain those policies like

![image](https://github.com/user-attachments/assets/0f0b6ba9-5f1c-417c-a32e-dfbd3f3e9abb)

## Step 3:
Create a Instance Manually and attach the IAM role for the EC2 instance,
Select an EC2 instance -> Actions -> Security -> Modify IAM Role

## Step 4:
Once the IAM Role attached Successfully, we need to install Code Deploy Agent in our instance, run those following commands
```
sudo apt update
sudo apt install ruby-full -y
sudo apt install wget
wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install
sudo chmod +x ./install
sudo ./install auto
```

## Step 5:
Check the code deploy agent logs, whether it is installed properly or not 
```
sudo tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log
```
It should Shows like the below image,

![image](https://github.com/user-attachments/assets/9affb0dc-d40a-4011-a6d1-ba082729a6c4)

## Step 6:
Go to Code Deploy and Create a Application

![image](https://github.com/user-attachments/assets/b5def454-4348-406d-9a0e-204d97517d90)

## Step 7:
Create a Deployment Group, Select the IAM role for which we created for code deploy

![image](https://github.com/user-attachments/assets/790a3f0a-995b-4375-819d-b6130b91253a)

## Step 8:
In Environment Configuration, Select EC2 instance

![image](https://github.com/user-attachments/assets/fbe3347a-10f8-4f7e-9fa0-ede1245337c4)
Then it will create a new deployment group for us

## Step 9:
Create a New Pipeline in Code pipeline, After that Select the Source Provider

![image](https://github.com/user-attachments/assets/491dd815-2505-43c1-a084-ffe34791a43c)

Select the repository of our source code to run 

![image](https://github.com/user-attachments/assets/78f72f32-658b-470a-9902-6a65c6c0d505)

## Step 10:
Skip the build stage as of now, we can build it when we want to build the application packages

## Step 11:
Add Deploy Stage and select the AWS code deploy provider

![image](https://github.com/user-attachments/assets/8b1f96cc-1aa7-42e2-864e-786e2635f3c5)

And then click Create Pipeline.


## Step 12:
add the deployement script in the github then commit the changes, and then it automatically triggers the pipeline and it will execute

![image](https://github.com/user-attachments/assets/10f4de77-8fbf-4561-92e5-4b3e6c0ffccf)



