<img width="442" height="43" alt="image" src="https://github.com/user-attachments/assets/a86de912-605c-46e2-8ce8-0f86eb8063f3" /><img width="870" height="149" alt="image" src="https://github.com/user-attachments/assets/7eead5e8-b15e-445c-8439-ea13855f8d33" />
<img width="442" height="24" alt="image" src="https://github.com/user-attachments/assets/10d1a359-973d-4efe-93b0-adc51053c3e6" /><img width="442" height="24" alt="image" src="https://github.com/user-attachments/assets/197aa308-8c9b-48f2-96e2-c9042b99cc25" />

<img width="870" height="149" alt="image" src="https://github.com/user-attachments/assets/57b4b62d-0ed2-4f6c-94be-fbccb069a929" />

## How to setup a Virtual Private Cloud in AWS using Terraform

Our goal is to create a VPC in AWS using Terraform.

## Step 0 : Give user full access permission
1. The user will need permissions to create a VPCs. Normally you will want to give granular access but in this case we will grant full access. Access the user in the IAM console and click add permission and then add permission.
<img width="425" height="114" alt="image" src="https://github.com/user-attachments/assets/4ec952da-4d72-4661-8200-f808d1361687" />

2. Click attach policy and search for “AmazonVPCFullAccess.” Click the radio box and click next.
<img width="425" height="273" alt="image" src="https://github.com/user-attachments/assets/0978f808-0a53-4729-a557-39b70cb73f6d" />

3. Click add permission. You should see the permission was added to the user.
<img width="425" height="113" alt="image" src="https://github.com/user-attachments/assets/b597747d-79ca-425a-9f72-382e77314304" />

## Step 1 : Write Terraform code for your VPC
1. If you are a total beginner, I recommend watching this video to help you write your code https://www.youtube.com/watch?v=4IFW5dXMW1Y Once you are done you should have something that looks like this
```
#Step 1 : Create a VPC
resource "aws_vpc" "myvpc" {
    cidr_block = "10.0.0.0/16"
    tags = {
        Name = "MyTerraformVPC"
	   }
}

#Step 2 : Create a public subnet
resource "aws_subnet" "PublicSubnet" {
    vpc_id = aws_vpc.myvpc.id
    cidr_block = "10.0.1.0/24"
}

#Step 3 : Create a private subnet
resource "aws_subnet" "PrivateSubnet" {
    vpc_id = aws_vpc.myvpc.id
    cidr_block = "10.0.2.0/24"
}

#Step 4 : Create internal gatway
resource "aws_internet_gateway" "igw"{
	 vpc_id = aws_vpc.myvpc.id
}

#Step 5 : Route tables for plublic subnet
resource "aws_route_table" "PublicRT"{
	 vpc_id = aws_vpc.myvpc.id
    route {
		  cidr_block = "0.0.0.0/0"
		  gateway_id = aws_internet_gateway.igw.id
		}
}

#Step 6 : Route table association for public subnet
resource "aws_route_table_association" "PublicRTassociation"{
	 subnet_id = aws_subnet.PublicSubnet.id
	 route_table_id = aws_route_table.PublicRT.id
}
```
2. Save this code in a file and name it something like <my-terraform-code.tf> in a directory and then move to that directory in the terminal.

## Step 2 : Execute your code using Terraform
1. In the terminal enter
   ```
   terraform init
   ```
(If you are getting an error stating [Terraform initialized in an empty directory!] do an ls command and make sure your file ends with .tf and not .tf.txt. If you need to rename the command is mv <filename>.tf.txt <filename>.tf)
If this is the first time running Terraform it may take a few minutes to install.
