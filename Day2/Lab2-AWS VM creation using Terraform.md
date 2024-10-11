## Launching your first AWS VM instance using Terraform 
```
cd ~ && mkdir aws-vm-lab && cd aws-vm-lab
```
Create the provider file
```
vi provider.tf
```
```
provider "aws" {
  region  = "us-east-1"
}
```
Create the main config file
```
vi main.tf
```
```
resource "aws_instance" "example" {
  ami           = "ami-0866a3c8686eaeeba"
  instance_type = "t2.micro"
  tags = {
    Name = "Mehar-EC2"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform plan
```
![image](https://github.com/user-attachments/assets/e91c6126-4359-447a-8e54-65c2c8b79280)

```
terraform apply -auto-approve
```
![image](https://github.com/user-attachments/assets/e51b404c-d33e-448b-89a5-5884827069b4)



Clean up your resources
```
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/5c45c2f9-3fc5-483a-8588-b97b07e7ac2b)



