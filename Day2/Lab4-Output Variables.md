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
  default = "us-east-2"
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
terraform apply
```
```
terraform output Public_ip
```
```
terraform output Private_ip
```
Use the `terraform destroy` command to clean the infrastructure used in this lab.
```
terraform destroy
```


