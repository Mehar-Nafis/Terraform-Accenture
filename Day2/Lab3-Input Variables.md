## AWS EC2 instance creation using Terraform Variables


### Task-1: Create EC2 instance using variables 
```
cd ~ && mkdir input-variables-lab && cd input-variables-lab/
```
Now Create four Files ie. `provider.tf,` `vars.tf,` `terraform.tfvars,` `instance.tf.`
![image](https://github.com/user-attachments/assets/ee42d95e-0835-43e1-a1c1-cf6b3836d528)

```
vi provider.tf
```
Add the given lines, by pressing "INSERT" 
```
provider "aws"{
  region=var.AWS_REGION
}
```
Save the file using "ESCAPE + :wq!"
```
vi vars.tf
```
Add the given lines, by pressing "INSERT" also replace your `Region`
```
variable "AWS_REGION"{}
```
Save the file using "ESCAPE + :wq!"
```
vi terraform.tfvars
```
Add the given lines, by pressing "INSERT" Also replace them with your `AccessKey` and `Secret Access Keys` that you created and downloaded earlier.
```
AWS_REGION = "us-east-1"
```
Save the file using "ESCAPE + :wq!"
```
vi instance.tf
```
Add the given lines, by pressing "INSERT" Also replace your `region's AMI ID` and `YourName`
```
resource "aws_instance" "terraform_example"{
  ami = "ami-0866a3c8686eaeeba"
  instance_type="t2.micro"
  tags = {
    Name = "Mehar-Variables-EC2"
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

![image](https://github.com/user-attachments/assets/cb15c080-97bd-4826-bc5d-5522f0c05c4c)

Once applied, go to the Console and check that the new EC2 is created using Variables.

Use the `"terraform destroy"` command to clean the infrastructure used in this lab
```
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/2d662d06-3fee-4413-a3f9-57b97c534ffc)

```
cd ~ && rm -rf input-variables-lab
```
