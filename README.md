# AWS-CLI-LAB
AWS CLI

# Install and coinfigure AWS CLI

## 1. Run from powershell
```powershell
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi![image](https://user-images.githubusercontent.com/83871251/160388647-e2e24582-8ea9-4485-8e23-33f22d734892.png)
```

## 2. Check version
```aws
aws --version
```

# Credentials Profiles & Config Profiles

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
  
  
  
  
  
  
  # 1. Create a VPC
  ```
  aws ec2 create-vpc \
	--cidr-block 10.69.0.0/16 \
  --tag-specifications ResourceType=vpc,Tags='[{Key=Name,Value="VPC from CLI"},{Key=Owner,Value="MIKA"}]'
  ```
  
  
  # 2. Create Internet Gateway (IGW)
  
  ```
  aws ec2 create-internet-gateway --tag-specifications ResourceType=internet-gateway,Tags='[{Key=Name, Value=myIGW}]'
  ```
  
  
  
