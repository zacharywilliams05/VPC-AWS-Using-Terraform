<img width="442" height="24" alt="image" src="https://github.com/user-attachments/assets/c85afbc3-9bc5-4f81-9fd6-fb04f3a5d017" /><img width="442" height="43" alt="image" src="https://github.com/user-attachments/assets/a86de912-605c-46e2-8ce8-0f86eb8063f3" /><img width="870" height="149" alt="image" src="https://github.com/user-attachments/assets/7eead5e8-b15e-445c-8439-ea13855f8d33" />


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
(If you are getting an error stating [Terraform initialized in an empty directory!] do an ls (that is "el" "es") command and make sure your file ends with .tf and not .tf.txt. If you need to rename the command is mv <filename>.tf.txt <filename>.tf)
<img width="425" height="209" alt="image" src="https://github.com/user-attachments/assets/d0670748-644d-4be8-a518-d460d55d416b" />

2. Next use the command below. This command will verify our code is good and let us know of any issues.
   ```
   terraform validate
   ```
<img width="425" height="82" alt="image" src="https://github.com/user-attachments/assets/5edf7428-386a-489b-a096-33dddbd267bc" />

In my case I have an undeclared resource error. I will correct this and run the terraform validate command again.

<img width="230" height="25" alt="image" src="https://github.com/user-attachments/assets/2135e5b0-32a1-4d4b-98ce-7588951aadd0" />

3. Enter the command below. This will show us all of the resources terraform will create.

   ```
   terraform plan
   ```
<img width="425" height="191" alt="image" src="https://github.com/user-attachments/assets/a6ab28e5-4c3c-496c-b061-b26fab97f8f0" />

4. Enter the command below to create the VPC.
   ```
   terraform apply -auto-approve
   ```
<img width="425" height="20" alt="image" src="https://github.com/user-attachments/assets/0f4700d6-4ed1-4505-b0f3-5fdaa16fe7f8" />

Looking in the AWS console we see our new VPC

<img width="425" height="92" alt="image" src="https://github.com/user-attachments/assets/a071cee4-877a-460b-bef5-eba934017caa" />

We can see our two subnets, two routing tables, and internet gateway.

<img width="425" height="106" alt="image" src="https://github.com/user-attachments/assets/2c96b2d3-a862-4b40-9311-f85a6de6fe37" />

5. Clean up. Enter the command below to delete the resources we created. Enter "yes" when prompted.
   ```
   terraform destroy
   ```
<img width="425" height="89" alt="image" src="https://github.com/user-attachments/assets/a8e9d816-1afa-419e-af8c-9329b6cc3d5e" />










