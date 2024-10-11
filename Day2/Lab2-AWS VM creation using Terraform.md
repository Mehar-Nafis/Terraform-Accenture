## Launching your first AWS VM instance using Terraform 
```
cd ~ && mkdir aws-vm-lab && cd aws-vm-lab
```
```
vi provider.tf
```
Add the given lines, by pressing "INSERT"
```
provider "aws" {
  profile = "default" # This line is not mandatory.
  region  = "us-east-2"
}
```
Create the main config file
```
resource "aws_instance" "example" {
  ami           = "ami-07efac79022b86107"
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
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
Clean up your resources
```
terraform destroy
```
