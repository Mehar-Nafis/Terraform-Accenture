### Launching your first VM using Terraform 
```
cd ~
mkdir Lab2
```
```
vi provider.tf
```
Add the given lines, by pressing "INSERT"

```hcl
provider "google" {
  project     = "deloitte-team2"
  zone   = "us-central1-c"
  region      = "us-central1"
}
```
```
vi main.tf
```
```hcl

resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance-test"
  machine_type = "g1-small"

  boot_disk {
    initialize_params {
      image = "debian-12-bookworm-v20240709"
    }
  }

  network_interface {
    # A default network is created for all GCP projects
    network = "default1"
    access_config {
    }
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
```
#List the files
ls 
```
To see what is saved in `terraform.tfstate` use the below command.

```
terraform show
```
Use the `terraform destroy` command for cleaning the infrastructure used in this lab
```
terraform destroy
```
Finally, verify that the resources are deleted in the Console.
```
cd ~
rm -rf Lab2
```
