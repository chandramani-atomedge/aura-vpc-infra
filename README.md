# Aura-47


## VPC


  Created VPC named as Aura-Cloud with two public subnets and three private subnets, Created and Attached Route table for both Public and Private subnets and then created Internet Gateway attached to public subnet and created NAT gateway attached to private subnet, when we want create NAT gateway we need NAT ip(elastic ip).

### Create a Vpc with the dynamic cidr_block, give the variables in terraform.tfvars file 
### vpc.tf
```
resource "aws_vpc" "main" {
  cidr_block = "${var.vpc_cidr_block}.0.0/16"

  tags = {
    Name = var.vpc_name
  }
}
```
### terraform.tfvars
```
vpc_name = "Aura-Cloud"

vpc_cidr_block = "10.0"
```

### Subnets 





# Aura-48

### Research Spike: Find a suitable instance family

| Category | Instance Family    | Size               | VCPUs | Memory(GB) | Network Performance |  Description | Cost |
| :-------- | :------- | :------------------------- | :-----| :---| :------| :------------ | :----|
| FrontEnd | M6i| M6i.xlarge | 4 | 16 | upto 12.5 Gbps | General purpose with more memory and compute capacity, Suitable for high-performance front-end applications.| 0.202 USD per hour |
|  | M6i | M6i.large | 2 | 8 | upto 12.5 Gbps | Provides a good balance of compute, memory, and network performance. Ideal for moderate to high-performance web servers hosting React applications.| 0.101 USD per hour | 
|   | C6i | C6i.large | 2 | 4 | upto 12.5 Gbps | High compute and network performance.| 0.085 USD per hour |
|  | C5  | C5.large  | 2 | 4 | upto 10.5 Gbps | Suitable for tasks requiring significant compute power, like high concurrency user management.| 0.085 USD per hour |
| Backend | T3 | t3.medium | 2 | 4 | upto 5 Gpbs | Suitable for burstable workloads, good for development. | 0.0448 USD per hour |
| | M5/M6i | m5.large/m6i.large | 2 | 8  | m5-upto 10 Gbps/m6i- upto 12.5 Gbps | Balanced performance for general-purpose backend services.| 0.101 USD per hour |
| | C5/C6i | c5.large/c6i.large | 2 | 4 | c5-upto 10 Gbps/c6i-upto 12.5 Gbps | High compute performance, good for CPU-intensive applications. | 0.085 USD per hour |

Reference link: https://aws.amazon.com/ec2/instance-types/

https://aws.amazon.com/blogs/aws/choosing-the-right-ec2-instance-type-for-your-application/


# Aura-49

### Web-App Cluster creation

  Created Security group(WEB-CLUSTER-SG), when we need to ssh in to the bastion host we need to attach our static ip in the security group(Bastion) in ssh cidr block to access from our local machine. Added Bastion host's Security group in the inbound rule , Created Launch Configuration for Auto Scaling Group in that EBS volume is attached, Created Autoscaling group with Load balancer and Target group with two instances placed into public subnets so that it can be directly connected with the internet and then attached auto scaling policy(Target Tracking policy) for Scale up and for scale down we used (Step scaling).We need to ssh through Bastion so created Bastion host. 



# Aura-50

### Research Spike: Find a suitable instance family to deploy Postgres DB clusters.
| Category | Instance Family    | Size               | VCPUs | Memory(GB) | Network Performance |  Description | Cost |
| :-------- | :------- | :------------------------- | :-----| :---| :------| :------------ | :----|
| Postgres | R6i| R6i.xlarge | 4 | 32 | upto 12.5 Gbps | Ideal fit for memory-intensive workloads, and balanced compute power.| 0.26 USD per hour |
|  |  | R6i.2xlarge | 8 | 64 | upto 12.5 Gbps | Increased memory and compute power.| 0.52 USD per hour | 
|   | R5 | R5.2xlarge | 8 | 64 | upto 10 Gbps | Well suited for running memory intensive workloads and good compute resources..| 0.52 USD per hour |
|  | M6i  | M6i.2xlarge  | 8 | 32 | upto 12.5 Gbps | Provides a balance of compute, memory, and network resources.| 0.404 USD per hour |

Reference link: https://aws.amazon.com/ec2/instance-types/

# Aura-51

### Postgres Cluster creation

Created Security Group(db_sg), when we need to ssh in to the bastion host we need to attach our static ip in the security group(Bastion) in ssh cidr block to access from our local machine. Added Bastion host's Security group in the inbound rule , Created Launch Configuration for Auto Scaling Group in that EBS volume is attached, Created Autoscaling group with Load balancer and Target group with two instances placed into private subnets and then attached auto scaling policy(Target Tracking policy) for Scale up and for scale down we used (Step scaling).We need to ssh through Bastion so created Bastion host.

# AURA-106

### Parameterise Cluster creation based on the environment

  Created cluster based on the environment, Created variable for Web_instance_type, db_instance_type.

  ```
variable "web_instance_type" {
  type = map(string)
  default = {
    "dev"   = "t3.micro"
    "stage" = "t3.medium"
    "prod"  = "t3.large"
  }
}

variable "db_instance_type"{
  type = map(string)
  default = {
    "dev"   = "t3.micro"
    "stage" = "t3.medium"
    "prod"  = "t3.large"
  }
}
  ```
we want to create a variable for No of instance, for development we used single instance and for staging and production we used two.

```
variable "no_of_instance"{
    type=map(string)
    default = {
        "dev"  = "1"
        "stage"= "2"
        "prod" = "2"
    }
}
```

Created variable for root volume size for both web and db, here we used 10GB for all environment.

```
variable "web_root_volume_size"{
  type = map(string)
  default = {
    "dev"   = "10"
    "stage" = "10"
    "prod"  = "10"
  }
}
variable "db_root_volume_size"{
  type = map(string)
  default = {
    "dev"   = "10"
    "stage" = "10"
    "prod"  = "10"
  }
}
```
In this we used lookup function in Terraform that allows to define a map of values and then retrieve specific values based on the environment. It is used to centralize environment-specific configurations like instance type, instance count, and EBS volume size.
```
instance_type   = lookup(var.web_instance_type,terraform.workspace)
```
```
root_block_device {
    volume_type = "gp3"
    volume_size = lookup(var.web_root_volume_size,terraform.workspace)
  }
```

First to Create Environment for dev, stage and prod.
```
terraform workspace new dev 
```

Select the environment,
```
terraform workspace select dev
```
In Terraform apply command,
  ```
terraform apply -var="aws_region=ap-south-1"  
  ```

  # AURA-107

  ### Attach external volume to all web & DB instances

  The requirement is to attach external ebs volume to both web and dp instances,

  ```
  variable "db_ebs_volume_size" {
  type = map(string)
  default = {
    "dev"   = "10"
    "stage" = "10"
    "prod"  = "25"
  }
}
  ```

  the below Script is for mount the external attached volume,

  ```
  #!/bin/bash

# Variables (Replace these with appropriate values)
DEVICE_NAME="/dev/nvme1n1"  # The device name of the attached EBS volume
MOUNT_POINT="/home/backup"  # The directory where the volume will be mounted

# Step 1: Check if the device exists
if [ ! -e $DEVICE_NAME ]; then
  echo "Device $DEVICE_NAME not found, exiting."
  exit 1
fi

# Step 2: Create a filesystem on the EBS volume (Assuming ext4, adjust if necessary)
sudo mkfs -t ext4 $DEVICE_NAME

# Step 3: Create a mount point
if [ ! -d $MOUNT_POINT ]; then
  sudo mkdir -p $MOUNT_POINT
fi

# Step 4: Mount the EBS volume
sudo mount $DEVICE_NAME $MOUNT_POINT

# Step 5: Change ownership (optional)
# Adjust this if you want a specific user to own the mounted directory
sudo chown -R ubuntu:ubuntu $MOUNT_POINT

# Step 6: Add the mount to /etc/fstab for automatic remount on reboot
UUID=$(sudo blkid -s UUID -o value $DEVICE_NAME)
sudo bash -c "echo 'UUID=$UUID $MOUNT_POINT ext4 defaults,nofail 0 2' >> /etc/fstab"

echo "EBS volume $DEVICE_NAME mounted at $MOUNT_POINT and added to /etc/fstab."

  ```

  In Launch configuration we need to add the user data script to mount the external ebs volume in our instances,we need to add this script in both webapp cluster and db cluster.

  ```
    resource "aws_launch_configuration" "db_config" {
  name_prefix     = "db-config-sg"
  image_id        = var.ami_map
  instance_type   = lookup(var.db_instance_type,terraform.workspace)
  security_groups = [aws_security_group.db_sg.id]
  key_name        = var.key_name
  root_block_device {
    volume_type = "gp3"
    volume_size = lookup(var.db_root_volume_size,terraform.workspace)
  }
  ebs_block_device {
    device_name = "/dev/xvdbc"
    volume_type = "gp3"
    volume_size = lookup(var.db_ebs_volume_size,terraform.workspace)
  }  
  user_data = file("./modules/db/script.sh")

  lifecycle {
    create_before_destroy = true
  }
}
```
For db cluster use the above launch configurations but change the user data script in db with full path.
```
    user_data = file("./modules/db/script.sh")  #db

  ```

  # AURA-113

  ### Parameterize Region

It is dynamic in such as a way that the entire resources is created for the given region which user is specified. When we want the specific region we need to pass the variable while applying the terraform script example,     terraform apply -var="region=<specific-region>"


```
    terraform apply -var="region=<us-east-1>"
```
  when we create resource we used specific region, That region automatically fetch the ami id.
```
variable "ami_map" {
  type = map(string)
  default = {
    us-east-1      = "ami-04a81a99f5ec58529" # N.Virginia
    us-east-2      = "ami-0862be96e41dcbf74" # Ohio
    us-west-1      = "ami-0ff591da048329e00" # N.California
    us-west-2      = "ami-0aff18ec83b712f05" # Oregon
    ap-south-1     = "ami-0ad21ae1d0696ad58" # Mumbai
    ap-northeast-3 = "ami-0db6b6e701fbc0603" # Osaka
    ap-northeast-2 = "ami-062cf18d655c0b1e8" # Seoul
    ap-northeast-1 = "ami-0a0b7b240264a48d7" # Tokyo
    ap-southeast-1 = "ami-060e277c0d4cce553" # Singapore
    ap-southeast-2 = "ami-03f0544597f43a91d" # Sydney
    eu-central-1   = "ami-0e872aee57663ae2d" # Frankfurt
    eu-west-1      = "ami-0c38b837cd80f13bb" # Ireland 
    eu-west-2      = "ami-07c1b39b7b3d2525d" # London
    eu-west-3      = "ami-09d83d8d719da9808" # Paris
    eu-north-1     = "ami-07c8c1b18ca66bb07" # Stockholm
  }
}

```
