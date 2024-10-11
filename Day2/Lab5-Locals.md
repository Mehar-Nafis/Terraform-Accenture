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
    Name = "Mehar-local"
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
![image](https://github.com/user-attachments/assets/09e789e0-3fc5-4df5-a43d-808be1dd12c3)

Cross verify from the Console.

![image](https://github.com/user-attachments/assets/3800ec92-514b-46f5-937e-260713bb19dc)

![image](https://github.com/user-attachments/assets/62e3e475-94db-4439-b4b2-4b7a6a5f2ce1)

![image](https://github.com/user-attachments/assets/295ba029-bb51-421d-9993-f790a713dc8e)


Clean up the resources
```
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/f2067e98-1665-420a-a759-043f048f8b5c)

```
cd ~ && rm -rf locals-lab
```
