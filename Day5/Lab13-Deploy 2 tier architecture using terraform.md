## Deploy 2 tier architecture using terraform

### Task 1: Create the infra
```
cd ~ && wget https://github.com/Mehar-Nafis/Terraform-Two-Tier-Architecture/archive/refs/heads/main.zip -O Terraform-Two-Tier-Architecture.zip
```
```
unzip Terraform-Two-Tier-Architecture.zip
```
```
cd Terraform-Two-Tier-Architecture-main
```
Make sure to replace all occurrences of `mehar` with your `name` to avoid any conflicts
```
terraform init
```
```
terraform plan
```
```
terraform apply -auto-approve
```
Cross verify by accessing your infra and finally destroy and clean up
```
terraform destroy -auto-approve
```
```
cd ~ && rm -rf Terraform-Two-Tier-Architecture-main
```
