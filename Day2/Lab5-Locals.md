## Understanding locals 

```
cd ~ && mkdir locals-lab && cd locals-lab
```
```
vi local.tf
```
Add the given lines, by pressing "INSERT" and replace "ENTER THE AMI VALUE" with actual values
```
provider "aws" {
  region     = "us-east-1"
}

locals {
  custom_tags = {
    Name = "Mehar"
    Team = "DevOps"
    Company = "CloudThat"
  }
}
resource "aws_instance" "instance1" {
   ami = "ami-0866a3c8686eaeeba"
   instance_type = "t2.micro"
   tags = local.custom_tags
}

resource "aws_ebs_volume" "db_ebs" {
  availability_zone = "us-east-1a"
  size              = 8
  tags = local.custom_tags
}
```

Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform plan
```
```
terraform apply -auto-approve
```
```
terraform destroy
```
