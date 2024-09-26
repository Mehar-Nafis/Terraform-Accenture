## Creating an Azure Linux Machine using Terraform
```
mkdir vm-lab && cd vm-lab
```
```
vi az-linux-vm.tf
```
Add the given content, by pressing "INSERT"
```
provider "azurerm" {
  features {}
  skip_provider_registration = true
}

resource "azurerm_resource_group" "RG" {
  name     = "mehar-tf-resource-grp"
  location = "East US"
}

resource "azurerm_virtual_network" "az-net" {
  name                = "mehar-az-network"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.RG.location
  resource_group_name = azurerm_resource_group.RG.name
}

resource "azurerm_subnet" "az-subnet" {
  name                 = "mehar-internal"
  resource_group_name  = azurerm_resource_group.RG.name
  virtual_network_name = azurerm_virtual_network.az-net.name
  address_prefixes     = ["10.0.2.0/24"]
}


resource "azurerm_network_interface" "az-net-int" {
  name                = "mehar-az-nic"
  location            = azurerm_resource_group.RG.location
  resource_group_name = azurerm_resource_group.RG.name
  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.az-subnet.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_linux_virtual_machine" "az-linux-vm" {
  name                = "mehar-az-linux-vm1"
  resource_group_name = azurerm_resource_group.RG.name
  location            = azurerm_resource_group.RG.location
  size                = "Standard_F2"
 
  disable_password_authentication = false

  admin_username      = "ubuntu"
  admin_password      = "admin@1234"
 
  network_interface_ids = [
    azurerm_network_interface.az-net-int.id,
  ]

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
}
```
Save the file using "ESCAPE + :wq!" and execute following terraform commands
```
terraform init
```
```
terraform plan -out linux-vm-plan
```
```
terraform apply linux-vm-plan
```
To check whether the Linux vm is created follow the command or visit azure virtual machine page in the portal
```
az vm list -o table --resource-group  tf-resource-grp
```
Once Verified, Cleanup the resources using below command
```
terraform destroy -auto-approve
```

rm -rf vm-lab
