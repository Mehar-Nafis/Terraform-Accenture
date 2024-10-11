## Software Provisioning

Create the terraform directory and set up the config files in it
```
cd ~ && mkdir provisioning-lab && cd provisioning-lab
```
As a first step, create a keyPair using `ssh-keygen` Command.
```
ssh-keygen -t rsa -b 2048 
```
```
vi main.tf
```
Copy and paste the below code into `main.tf`
```
provider "aws" {
  region = var.region
}

resource "aws_key_pair" "mykeypair" {
  key_name   = var.key_name
  public_key = file(var.public_key)
}

# to create  EC2 instances
resource "aws_instance" "my-machine" {
  ami                    = var.ami_id
  key_name               = var.key_name
  vpc_security_group_ids = [var.sg_id]
  instance_type          = var.ins_type
  depends_on             = [aws_key_pair.mykeypair]

  tags = {
    Name = Mehar-Provisioning-EC2
  }

  # Ensure the .ssh directory exists before copying the file
  provisioner "remote-exec" {
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("/home/ubuntu/.ssh/id_rsa")  # Replace with the path to your private key
      host        = self.public_ip
    }

    inline = [
      "mkdir -p /home/ubuntu/.ssh"   # Ensure .ssh directory exists before copying the file
    ]
  }

  provisioner "file" {
    source      = "/home/ubuntu/.ssh/id_rsa"   # Source path on Host machine
    destination = "/home/ubuntu/.ssh/id_rsa"  # Destination path on EC2 instance
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("/home/ubuntu/.ssh/id_rsa")  # Replace with the path to your private key
      host        = self.public_ip
    }
  }

  # After the file is copied, change its permissions
  provisioner "remote-exec" {
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("/home/ubuntu/.ssh/id_rsa")  # Replace with the path to your private key
      host        = self.public_ip
    }

    inline = [
      "chmod 400 /home/ubuntu/.ssh/id_rsa"  # Change the permissions of the copied file after it's been transferred
    ]
  }

  provisioner "local-exec" {
    command = <<-EOT
      echo ${self.public_ip} >> /home/ubuntu/provisioning-lab/public-ip
    EOT
  }
}
```
Now, create the variables file with all variables to be used in the `main.tf` config file.
```
vi variables.tf
```
```
variable "region" {
    default = "us-east-1"
}

# Change the SG ID. You can use the same SG ID used for your CICD Jump server
# Basically the SG should open ports 22, 80, 8080, 9999, and 4243
variable  "sg_id" {
    default = "sgr-0e75d1ce8dc269c10" # us-east-1
}

# Choose a free tier Ubuntu AMI. You can use below. 
variable "ami_id" {
    default = "ami-0866a3c8686eaeeba" # us-east-1; Ubuntu
}

# We are only using t2.micro for this lab
variable "ins_type" {
    default = "t2.micro"
}

# Replace 'yourname' with your first name
variable key_name {
    default = "Mehar-Provisioner-KeyPair"
}

variable public_key {
    default = "/home/ubuntu/.ssh/id_rsa.pub"   #Ubuntu OS
}

```
Now, execute the terraform commands to launch the new servers
```
terraform init
```
```
terraform plan
```
```
terraform apply -auto-approve
```
Once the Changes are applied, Go to `EC2 Dashboard` and check that `New Instances` is launched. Also check the file `public-ip` and ensure the below output.
```
cat /home/ubuntu/provisioning-lab/public-ip
```
From `Jump Server` SSH into `Jenkins-Server`, check they are accessible.

```
ssh ubuntu@<ec2 ip address>
```
```
exit
````
Now destroy the infra
```
terraform destroy auto-approve
```
