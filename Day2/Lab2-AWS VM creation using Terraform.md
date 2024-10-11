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

The same can be verified on the management console as well</br>


![image](https://github.com/user-attachments/assets/d6c8c60e-4d19-45cf-bdb9-9b34f2c51e6e)


Clean up your resources
```
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/df5032dc-82c5-4dae-8815-fefed21758d2)
