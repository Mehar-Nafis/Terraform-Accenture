## Data sources

```
cd ~ && mkdir data-lab && cd data-lab
```
```
vi data.tf
```

Add the given lines, by pressing "INSERT" 
```
provider "aws" {
  region     = "us-east-1"
}

data "aws_ami" "ami" {
  most_recent = true
  owners = ["amazon"]


  filter {
    name   = "name"
    values = ["amzn2-ami-hvm*"]
  }
}

resource "aws_instance" "ec2instance" {
    ami = data.aws_ami.ami.id
   instance_type = "t2.micro"
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
![image](https://github.com/user-attachments/assets/a5cd03cd-dd4c-4669-81c3-cc8cee692d4b)

Clean up the resources.
```
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/c5fddff2-8bad-46aa-8e89-9fbe32692be8)

```
cd ~ && rm -rf data-lab
```
