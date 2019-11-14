---
title: Usar o Terraform para criar uma VM Linux completa no Azure
description: Saiba como usar o Terraform para criar e gerenciar um ambiente de máquina virtual Linux completo no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: b97d9563f0bddcc2b3bf82807f41af68f3abbff1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034708"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Criar uma infraestrutura completa de máquina virtual do Linux no Azure com o Terraform

O Terraform permite que você defina e crie implantações de infraestrutura completas no Azure. Você cria modelos de Terraform em um formato legível que cria e configura recursos do Azure de maneira consistente e reproduzível. Este artigo mostra como criar um ambiente completo do Linux e recursos de suporte com o Terraform. Você também pode aprender a [instalar e configurar o Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Criar uma conexão do Azure e um grupo de recursos

Vamos percorrer cada seção de um modelo Terraform. Você também pode ver a versão completa do [modelo Terraform](#complete-terraform-script) que você pode copiar e colar.

A seção `provider` informa Terraform para usar um provedor do Azure. Para obter valores para *SUBSCRIPTION_ID*, *client_id*, *Client_secret*e *Tenant_id*, consulte [instalar e configurar o Terraform](terraform-install-configure.md). 

> [!TIP]
> Se você criar variáveis de ambiente para os valores ou estiver usando a [experiência de Bash Azure cloud Shell](/azure/cloud-shell/overview) , não precisará incluir as declarações de variáveis nesta seção.

```hcl
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A seção a seguir cria um grupo de recursos chamado `myResourceGroup` no local `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

Em seções adicionais, você faz referência ao grupo de recursos com *$ {azurerm_resource_group. myterraformgroup. Name}* .

## <a name="create-virtual-network"></a>Criar a rede virtual
A seção a seguir cria uma rede virtual chamada *myVnet* no espaço de endereço *10.0.0.0/16* :

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

A seção a seguir cria uma sub-rede chamada *mysubnet* na rede virtual *myVnet* :

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Criar endereço IP público
Para acessar recursos na Internet, crie e atribua um endereço IP público à sua VM. A seção a seguir cria um endereço IP público chamado *myPublicIP*:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Criar grupo de segurança de rede
Os grupos de segurança de rede controlam o fluxo de entrada e saída de tráfego de rede de sua VM. A seção a seguir cria um grupo de segurança de rede chamado *myNetworkSecurityGroup* e define uma regra para permitir o tráfego ssh na porta TCP 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Criar placa de interface de rede virtual
Uma NIC (placa de interface de rede) virtual conecta sua VM a uma determinada rede virtual, endereço IP público e grupo de segurança de rede. A seção a seguir em um modelo Terraform cria uma NIC virtual denominada *myNIC* conectada aos recursos de rede virtual que você criou:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    network_security_group_id   = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Criar conta de armazenamento para diagnóstico
Para armazenar o diagnóstico de inicialização para uma VM, você precisa de uma conta de armazenamento. Esses diagnósticos de inicialização podem ajudá-lo a solucionar problemas e monitorar o status de sua VM. A conta de armazenamento que você cria é apenas para armazenar os dados de diagnóstico de inicialização. Como cada conta de armazenamento deve ter um nome exclusivo, a seção a seguir gera um texto aleatório:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Agora você pode criar uma conta de armazenamento. A seção a seguir cria uma conta de armazenamento com o nome com base no texto aleatório gerado na etapa anterior:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Criar a máquina virtual

A etapa final é criar uma VM e usar todos os recursos criados. A seção a seguir cria uma VM chamada *myVM* e anexa a NIC virtual chamada *myNIC*. A imagem mais recente do *Ubuntu 16, 4-LTS* é usada e um usuário chamado *azureuser* é criado com a autenticação de senha desabilitada.

 Os dados da chave SSH são fornecidos na seção *ssh_keys* . Forneça uma chave SSH pública válida no campo *key_data* .

```hcl
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Concluir script Terraform

Para reunir todas essas seções e ver Terraform em ação, crie um arquivo chamado *terraform_azure. TF* e cole o seguinte conteúdo:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Criar e implantar a infraestrutura
Com o modelo Terraform criado, a primeira etapa é inicializar o Terraform. Essa etapa garante que o Terraform tenha todos os pré-requisitos para criar seu modelo no Azure.

```bash
terraform init
```

A próxima etapa é fazer com que o Terraform reveja e valide o modelo. Esta etapa compara os recursos solicitados com as informações de estado salvas por Terraform e, em seguida, gera a execução planejada. Os recursos *não* são criados no Azure.

```bash
terraform plan
```

Depois de executar o comando anterior, você verá algo semelhante à tela a seguir:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Se tudo parecer correto e você estiver pronto para criar a infraestrutura no Azure, aplique o modelo em Terraform:

```bash
terraform apply
```

Quando o Terraform for concluído, sua infraestrutura de VM estará pronta. Obtenha o endereço IP público de sua VM com [AZ VM show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Em seguida, você pode SSH para sua VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Passos seguintes
Você criou a infraestrutura básica no Azure usando o Terraform. Para cenários mais complexos, incluindo exemplos que usam balanceadores de carga e conjuntos de dimensionamento de máquinas virtuais, consulte vários [exemplos de Terraform para o Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para obter uma lista atualizada de provedores do Azure com suporte, consulte a [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
