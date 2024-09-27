## Launching your first AWS VM instance using Terraform 
```
cd ~ && mkdir aws-vm-lab && cd aws-vm-lab
```
```
vi example.tf
```
Add the given lines, by pressing "INSERT"

##### Note: 
1. Replace your allocated `Region` and `AMI ID` of the same Region.
2. `"Yourname-EC2-1"` with Your Name.

```
provider "aws" {
  profile = "default" # This line is not mandatory.
  region  = "us-east-2"
}

resource "aws_instance" "example" {
  ami           = "ami-07efac79022b86107"
  instance_type = "t2.micro"
  tags = {
    Name = "Yourname-EC2-1"
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
