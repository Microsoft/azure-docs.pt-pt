---
title: Utilize o Terraform para criar uma VM de Linux completa no Azure | Documentos da Microsoft
description: Saiba como utilizar o Terraform para criar e gerir um ambiente completo de máquina virtual do Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: 572cbeec9a259c6996f40696f115218720397227
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651145"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Criar uma infraestrutura completa de máquina virtual Linux no Azure com o Terraform

Terraform permite-lhe definir e criar implementações de toda a infraestrutura no Azure. Criar modelos de Terraform num formato legível por humanos que criar e configurar recursos do Azure de forma consistente e reproduzível. Este artigo mostra-lhe como criar um ambiente completo do Linux e de recursos com o Terraform de suporte. Também pode saber como [instalar e configurar o Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Criar a ligação do Azure e o grupo de recursos

Vamos percorrer cada secção de um modelo do Terraform. Também pode ver a versão completa dos [Terraform modelo](#complete-terraform-script) que pode copiar e colar.

O `provider` secção informa o Terraform para utilizar um fornecedor do Azure. Para obter valores para *subscription_id*, *client_id*, *client_secret*, e *tenant_id*, consulte [instalar e configurar o Terraform](terraform-install-configure.md). 

> [!TIP]
> Se criar variáveis de ambiente para os valores ou estiver a utilizar o [do Azure Cloud Shell Bash experiência](/azure/cloud-shell/overview) , não precisa de incluir as declarações de variável nesta secção.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A seção a seguir cria um grupo de recursos chamado `myResourceGroup` no `eastus` localização:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}
```

Nas secções adicionais, referencia o grupo de recursos com *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Criar a rede virtual
A seção a seguir cria uma rede virtual denominada *myVnet* no *10.0.0.0/16* espaço de endereços:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```

A seção a seguir cria uma sub-rede denominada *mySubnet* no *myVnet* rede virtual:

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Criar um endereço IP público
Para acessar recursos na Internet, criar e atribuir um endereço IP público à VM. A seção a seguir cria um endereço IP público com o nome *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Criar grupo de segurança de rede
Grupos de segurança de rede controlar o fluxo de tráfego de rede e para a sua VM. A seção a seguir cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup* e define uma regra para permitir o tráfego SSH na porta TCP 22:

```tf
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
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

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Criar placa de interface de rede virtual
Uma placa de interface de rede virtual (NIC) liga-se a sua VM para uma determinada rede virtual, o endereço IP público e o grupo de segurança de rede. A seção a seguir num modelo do Terraform cria uma NIC virtual com o nome *myNIC* ligado para os recursos de rede virtual que criou:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Criar conta de armazenamento para diagnóstico
Para armazenar o diagnóstico de arranque para uma VM, precisa de uma conta de armazenamento. Estes diagnósticos de arranque podem ajudá-lo a resolver problemas e monitorizar o estado da sua VM. Criar a conta de armazenamento é apenas armazenar os dados de diagnóstico de arranque. À medida que cada conta de armazenamento tem de ter um nome exclusivo, a seção a seguir gera texto aleatório:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Agora, pode criar uma conta de armazenamento. A seção a seguir cria uma conta de armazenamento, com o nome com base no texto aleatório gerado no passo anterior:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Criar a máquina virtual

A etapa final é criar uma VM e utilizar todos os recursos criados. A seção a seguir cria uma VM com o nome *myVM* e anexa o NIC virtual com o nome *myNIC*. A versão mais recente *Ubuntu 16.04-LTS* imagem é utilizada e o nome de um usuário *azureuser* é criado com a autenticação de palavra-passe desativada.

 Dados de chaves SSH são fornecidos no *ssh_keys* secção. Fornecer uma chave SSH pública válida no *key_data* campo.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
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
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Concluir o script do Terraform

Para reunir todas estas secções e ver o Terraform em ação, crie um ficheiro chamado *terraform_azure.tf* e cole o seguinte conteúdo:

```tf
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

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
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

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
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
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Criar e implementar a infraestrutura
Com o modelo de Terraform criado, o primeiro passo é inicializar o Terraform. Este passo garante que o Terraform tem todos os pré-requisitos para criar o seu modelo no Azure.

```bash
terraform init
```

A próxima etapa é fazer com Terraform, analisar e validar o modelo. Este passo compara os recursos solicitados para as informações de estado guardado pelo Terraform e, em seguida, devolve a execução planeada. Os recursos estão *não* criada no Azure.

```bash
terraform plan
```

Depois de executar o comando anterior, deverá ver algo semelhante ao seguinte ecrã:

```bash
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

Se tudo parece correta e está pronto para criar a infraestrutura no Azure, aplicam-se o modelo no Terraform:

```bash
terraform apply
```

Uma vez concluída a Terraform, sua infraestrutura VM está pronta. Obtenha o endereço IP público da VM com [show de vm de az](/cli/azure/vm):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Pode, em seguida, SSH à VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Passos Seguintes
Criou infraestrutura básica no Azure com o Terraform. Para cenários mais complexos, incluindo exemplos que se dimensionam de máquina virtual e Balanceadores de carga de utilização Consulte vários conjuntos, [Terraform exemplos para o Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para obter uma lista atualizada de fornecedores do Azure suportados, consulte a [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
