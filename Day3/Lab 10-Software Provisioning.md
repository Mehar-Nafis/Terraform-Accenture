```
provider "aws" {
  region = var.region
}

resource "aws_key_pair" "mykeypair" {
  key_name   = var.key_name
  public_key = file(var.public_key)
}

# to create 2 EC2 instances
resource "aws_instance" "my-machine" {
  # Launch 2 servers
  for_each = toset(var.my-servers)

  ami                    = var.ami_id
  key_name               = var.key_name
  vpc_security_group_ids = [var.sg_id]
  instance_type          = var.ins_type
  depends_on             = [aws_key_pair.mykeypair]

  # Read from the list my-servers to name each server
  tags = {
    Name = each.key
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
      echo [${each.key}] >> /etc/ansible/hosts
      echo ${self.public_ip} >> /etc/ansible/hosts
    EOT
  }
}
```
