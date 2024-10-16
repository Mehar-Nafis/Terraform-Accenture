## Functions

```
cd ~ && mkdir functions-lab  && cd functions-lab
```
```
vi functions.tf
```
Add the given lines, by pressing "INSERT" 
```
provider "aws" {
  region     = var.region
}

variable "region" {
  default = "us-east-1"
}

variable "tags" {
  type = list
  default = ["Mehar-ec2-1","Mehar-ec2-2"]
}

variable "ami" {
  type = map
  default = {
    "us-east-1" = "ami-0866a3c8686eaeeba"        
    "us-east-2" = "ami-0ea3c35c5c3284d82"       
    "us-west-1" = "ami-0da424eb883458071"      
  }
}

resource "aws_instance" "application-servers" {
   ami = lookup(var.ami,var.region)
   instance_type = "t2.micro"
   count = 2

   tags = {
     Name = element(var.tags,count.index)
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
```
terraform apply -auto-approve
```
![image](https://github.com/user-attachments/assets/132c0198-bb32-40ec-92b7-a7daef00aa94)


Now clean up the resources.
```
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/e17b058d-5a2c-4615-9299-7ff707e6169c)

```
cd ~ && rm -rf functions-lab
```

