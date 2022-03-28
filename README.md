# AWS-CLI-LAB
AWS CLI

# Install and coinfigure AWS CLI

## 1. Run from powershell
```powershell
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
```

## 2. Check version
```powershell
aws --version
```

## Credentials Profiles & Config Profiles

* Linux
  ```
  ~/.aws/credentials
  ```
  
* Windows
  ```
  %USERPROFILE%\.aws\credentials
  ```
  
* You can specify the profile name in a cmd by adding --profile <profileName>
  ```
  [default]
  aws_access_key_id=AKIAIOSFODNN7EXAMPLE
  aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

  [MIKA]
  aws_access_key_id=AKIAI44QH8DHBEXAMPLE
  aws_secret_access_key=je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
  ```
  
  
  
  
  # Create a full environment from scratch: VPC, IGW, 
  
  ## 1. Create a VPC
  ```powershell
  aws ec2 create-vpc \
	--cidr-block 10.69.0.0/16 \
  --tag-specifications ResourceType=vpc,Tags='[{Key=Name,Value="VPC from CLI"},{Key=Owner,Value="MIKA"}]'
  ```
  
  
  ## 2. Create Internet Gateway (IGW)
  ```bash
  aws ec2 create-internet-gateway --tag-specifications ResourceType=internet-gateway,Tags='[{Key=Name, Value=myIGW}]'
  ```
  
  ## 3. Attach IGW to VPC
  ```awscli
  aws ec2 attach-internet-gateway --internet-gateway-id igw-05ef0911baf4ed97d --vpc-id vpc-062f367e1f4179f33
  ```	

  ## 4. Create Route Table
  ```
  aws ec2 create-route-table --vpc-id vpc-062f367e1f4179f33 --tag-specifications ResourceType=route-table,Tags='[{Key=Name, Value=Public-RT}]'
  ```

  ## 5. Create a Public Route in Route Table 
  ```
  aws ec2 create-route --route-table-id rtb-08fabc600737d3608 --destination-cidr-block 0.0.0.0/0 --gateway-id igw-05ef0911baf4ed97d
  ```	

  ## 6. Create 2 Subnets in VPC
  ```
  aws ec2 describe-subnets
  aws ec2 describe-subnets --filters "Name=vpc-id, Values=vpc-062f367e1f4179f33"
  ```

  Subnet-1
  ```
  aws ec2 create-subnet --vpc-id  vpc-062f367e1f4179f33 --cidr-block 10.69.10.0/24 --availability-zone us-east-1a --tag-specifications     ResourceType=subnet,Tags='[{Key=Name,Value=Subnet-1}]'
  ```

  Subnet-2
  ```
  aws ec2 create-subnet --vpc-id  vpc-062f367e1f4179f33 --cidr-block 10.69.20.0/24 --availability-zone us-east-1b --tag-specifications ResourceType=subnet,Tags='[{Key=Name,Value=Subnet-2}]'
  ```

  ## 7. Associate subnets with Route Table
  ```
  aws ec2 associate-route-table --route-table-id rtb-08fabc600737d3608 --subnet-id subnet-0ac8ccb75468a425a
  aws ec2 associate-route-table --route-table-id rtb-08fabc600737d3608 --subnet-id subnet-071f789533599fd73
  ```

  ## 8. Create a KP
  ```
  aws ec2 create-key-pair --key-name myKeyPair --tag-specifications ResourceType=key-pair,Tags='[{Key=Owner,Value=Mika}]'
  ```

  ## 9. Create a SG
  ```
  aws ec2 create-security-group --group-name webRDC --description "My security group" --vpc-id vpc-062f367e1f4179f33 --tag-specifications ResourceType=security-group,Tags='[{Key=Name,Value=webRDC}]'
  ```

  Add HTTP and RDC rules
  ```
  aws ec2 authorize-security-group-ingress --group-id sg-09d878908d0fb1086 --ip-permissions IpProtocol=tcp,FromPort=3389,ToPort=3389,IpRanges='[{CidrIp=0.0.0.0/0}]' IpProtocol=tcp,FromPort=80,ToPort=80,IpRanges='[{CidrIp=0.0.0.0/0}]'
  ```

  ## 10. Launch 2 EC2 instances
  ```
  aws ec2 run-instances --image-id ami-0f9a92942448ac56f --count 1 --instance-type t3.small --key-name myKeyPair --security-group-ids sg-09d878908d0fb1086 --subnet-id subnet-0ac8ccb75468a425a --tag-specifications ResourceType=instance,Tags='[{Key=Name,Value=Instance-1}]'
  aws ec2 run-instances --image-id ami-0f9a92942448ac56f --count 1 --instance-type t3.small --key-name myKeyPair --security-group-ids sg-09d878908d0fb1086 --subnet-id subnet-071f789533599fd73 --tag-specifications ResourceType=instance,Tags='[{Key=Name,Value=Instance-2}]'
  ```

  ## 11. Allocate 2 EIPs
  ```
  aws ec2 allocate-address --tag-specifications ResourceType=elastic-ip,Tags='[{Key=Name,Value=EIP-1}]'
  aws ec2 allocate-address --tag-specifications ResourceType=elastic-ip,Tags='[{Key=Name,Value=EIP-2}]'
  ```

  ## 12. Associate EIPs with the instances
  ```
  aws ec2 associate-address --instance-id i-04372c921501c3e70 --allocation-id eipalloc-071254f57c14e20f9
  aws ec2 associate-address --instance-id i-0ed16a3c26a14b326 --allocation-id eipalloc-0e294989374e53a58

  
