## Commonly used State Commands

### terrafrom state List
This command lists all resources that are being tracked in the Terraform state. Print all the resource  addresses of all the resources and no other details
```
terraform state list
```
![image](https://github.com/user-attachments/assets/7c110386-3454-48ed-9015-ffc3c255e730)


### terraform state show
Displays detailed information about a specific resource in the Terraform state, including its attributes and metadata.
```
terraform state show aws_instance.terraform-remoteState
```

### terraform state mv
Moves an item in the Terraform state from one address to another. This is useful when you want to rename a resource or move items from one state file to another.<\br>
To rename a file
```
terraform state mv aws_instance.terraform-remoteState aws_instance.new_example1
```
![image](https://github.com/user-attachments/assets/4eedca9d-1648-4209-ab60-aef6d7028d33)

The above command will update the state file. Once the command is executed the name should be manually updated in the configuration file. Now if you execute a terraform apply, no changes should be applied for this resource

### terraform state pull
Manually pulls the state and outputs it as JSON. This is useful for debugging or inspecting the current state of your infrastructure.
```
terraform state pull > terraform_state.json
```
![image](https://github.com/user-attachments/assets/cd4a1d0a-96c4-45b9-8a2a-5735d5ed7d66)

### terraform state rm
Removes one or more items from the Terraform state. This is typically used when you want to delete a resource from your infrastructure and stop managing it with Terraform.
```
terraform state rm aws_instance.new_example1
```
![image](https://github.com/user-attachments/assets/b62847d2-f4fe-4dc8-b5a4-6c9402ecfdf0)

The above command will update the state file. Once the command is executed the resource should be manually removed from the configuration file. Now if you execute a terraform apply, no changes should be applied for this resource
