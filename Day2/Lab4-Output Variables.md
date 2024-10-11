## Using Output Feature 

```
cd ~ && mkdir output-variable-lab && cd output-variable-lab
```
Create the instance.tf file
```
vi instance.tf
```
```
provider "aws" {
  region     = var.AWS_REGION
}

resource "aws_instance" "example" {
  ami           = "ami-0866a3c8686eaeeba"
  instance_type = "t2.micro"
 }
```
Save the changes
```
vi output.tf
```
```
output "Public_ip" {
  description = "Public IP of the instance"
  value = aws_instance.example.public_ip
}

output "Private_ip" {
  sensitive = true
  description = "Private IP of the instance"
  value = aws_instance.example.private_ip

}
```
Save the file

In `vars.tf` ensure to replace your `region`. 
```
vi vars.tf
```
```
variable "AWS_REGION" {
  default = "us-east-1"
}
```
Once replaced, save the changes.
```
terraform init
```
```
terraform plan
```
```
terraform apply -auto-approve
```
![image](https://github.com/user-attachments/assets/be203935-9e47-4c0f-96fe-6a06a2781ee4)

```
terraform output Public_ip
```
![image](https://github.com/user-attachments/assets/47a6da01-a155-40ed-a31f-5af3199eacfa)

```
terraform output Private_ip
```
![image](https://github.com/user-attachments/assets/fbd09a05-c7ca-4f7c-992d-83e1bbcaa11b)

Use the `terraform destroy` command to clean the infrastructure used in this lab.
```
terraform destroy -auto-approve
```
![image](https://github.com/user-attachments/assets/20d3af1b-1de7-48e7-bd61-f70127eacce7)

```
cd ~ && rm -rf output-variable-lab
```

