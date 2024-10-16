
## Remote State using Amazon Simple Storage Service 

### Task-1: Manually Create a S3 Bucket using AWS Console 

* Create a new S3 bucket in your Allocated Region by name: `yourname-terraform` (S3 allows lowercase only)
* While creating,
    - Select `"ACLs enabled"`
    - Uncheck `"block public access"` and select `"I acknowledge that the current settings might result in this bucket and the objects within becoming public"`
    - `"Enable versioning"`
    - Then, click on `Create bucket`
* Once done, To cross-check whether the Bucket is created or not, run the below command in CLI.
```
aws s3 ls 
```

### Task-2: Manually Create a Dynamo DB table 

* Open the DynamoDB Service
* Click on the `Create table` button.
* Configure the Table Details
  - `Table name`: Enter a name for your table, such as terraform-state-lock.
  - `Partition key`: Enter `LockID` as the partition key name and set the data type to `String (S)`.
  - Choose the default settings
* Click `Create table`

### Task-3: Configure Remote State
```
cd ~ && mkdir S3-Lab && cd S3-Lab
```
Create a New Configuration File 
```
vi main.tf
```
Add the below given lines, by pressing "INSERT"  
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "terraform-remoteState" {
  ami           = "ami-0866a3c8686eaeeba"
  instance_type = "t2.nano"
  tags = {
    Name = "Mehar-remote-ec2"}
 }

output "ip" {
  value = aws_instance.terraform-remoteState.public_ip
}
```
Save the file using "ESCAPE + :wq!"

Now, Create a New Configuration File for storing "`terraform.tfstate`" file in the backend. (ie. `Amazon S3.`)
```
vi backend.tf
```
Add the given lines, by pressing "INSERT" and add your `bucket's region` and `Bucket Name`
```
terraform {
  backend "s3" {
    region = "<Replace your s3 bucket region>"
    bucket = "<Replace your s3 bucket name>"
    key    = "terraform/remotestate"
    dynamodb_table = "<replace your table name>"
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

![image](https://github.com/user-attachments/assets/ec7b6bb8-14e3-4f6f-ae8c-e022da9e102e)

* Go to the S3 bucket and click on `terraform` > `remotestate` > In Properties Copy the `Object URL` and paste it in Browser.
  (By default it shows Access Denied)
* To view the content of the file, in S3 Bucket tab, Click on `permission` and click on `Edit` under `Access control list (ACL)` > `Everyone (public access)` > Check `"Read"` then check `I understand the effects of these changes on this object` and then Click on `Save changes`
* Refresh the Object URL Page in the browser (or again Copy-paste the `object URL` into the web browser).
* Now, You should be able to access the state file and View the resources.
  (It shows the attributes of a single resource in the Terraform state of `aws_instance.terraform-remoteState`.)

Check out the State Commands Lab before destroying your resources.

Use the `terraform destroy` command to clean the infrastructure used in this lab, 
```
terraform destroy -auto-approve
```
Once done, Remove the directory.
```
cd ~ && rm -rf S3-Lab
```
**Note:** `Also Ensure to delete the  Dynamo DB Table and the S3 Bucket (To delete, first empty the Bucket and then Delete it.) `
