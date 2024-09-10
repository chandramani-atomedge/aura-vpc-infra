# VPC Peering
### Step1: 
Create a VPC with subnet, route table and internet gateway in a specific Region eg:us-east-1(N.Virginia)
![image](https://github.com/user-attachments/assets/68b43e71-273d-4e05-92a2-d12c7eb1a232)

### Step2:
Create a VPC with subnet, route table and internet gateway in a specific Region eg:us-west-2(Oregeon)
![image](https://github.com/user-attachments/assets/111dff33-b8b8-4465-b7b0-f4fd576d1bc8)

### Step3:
Create a Peering Connection for vpc peering
![image](https://github.com/user-attachments/assets/7c21143d-08e0-4843-a10d-0b0458c64ed8)

### Step4:
Choose another region for peering in multiple region
![image](https://github.com/user-attachments/assets/81b016a7-7a7e-4324-9f27-fdc4ff76d509)

### Step5:
After creating the vpc peering, the status should be active
![image](https://github.com/user-attachments/assets/a7beaefb-9d0b-4680-819c-7a21f1952524)

### Step6:
If not the status is active accept the request in the vpc peering 

### Step7:
Copy the VPC1 IPV4 address and attach it in the routetable of VPC2
![image](https://github.com/user-attachments/assets/be24b764-fbfc-41e0-ac2b-897d729268d1)

### Step8:
Copy the VPC2 IPV4 address and attach it in the routetable of VPC1
![image](https://github.com/user-attachments/assets/f7e371e7-e073-4b93-82b3-be7219c14288)

### Step9:
We have Successfully achieved the VPC Peering Across Multiple Region, To check whether it is working or not
Create a instance in vpc1 region and create a another instance from vpc2

### Step10:
copy the private IP of the created instance from the vpc1 and ping it in the another region where the instance created in the VPC2 region
give curl command to test it,
```
curl <PrivateIP>
```
![image](https://github.com/user-attachments/assets/77db3c32-645c-46b3-8aa4-2d32c0076041)

### Step11:
copy the private IP of the created instance from the vpc2 and ping it in the another region where the instance created in the VPC1 region
give curl command to test it,
```
curl <PrivateIP>
```
![image](https://github.com/user-attachments/assets/6f4b2957-3ebc-4ec6-86c5-cf20163dc8f9)

