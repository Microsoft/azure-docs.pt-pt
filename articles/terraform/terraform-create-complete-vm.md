---
title: Use terraforma para criar um VM Linux completo em Azure
description: Aprenda a usar a Terraform para criar e gerir um ambiente completo de máquinas virtuais Linux em Azure
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
ms.openlocfilehash: 819aeb225c4f55f803a5fad19eff33bd1748bf46
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472742"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Crie uma infraestrutura virtual de máquinas linux completa em Azure com terraforma

A Terraform permite-lhe definir e criar implementações completas de infraestruturas em Azure. Você constrói modelos Terraform em um formato legível pelo homem que cria e configura recursos Azure de uma forma consistente e reprodutível. Este artigo mostra-lhe como criar um ambiente Linux completo e apoiar recursos com a Terraform. Também pode aprender a [instalar e configurar terrafora.](terraform-install-configure.md)

> [!NOTE]
> Para um suporte específico terraforme, contacte a Terraform diretamente utilizando um dos seus canais comunitários:
>
>   • A [secção Terraforme](https://discuss.hashicorp.com/c/terraform-core) do portal comunitário contém perguntas, casos de utilização e padrões úteis.
>
>   • Para questões relacionadas com o fornecedor, visite a secção [fornecedores terraformedo](https://discuss.hashicorp.com/c/terraform-providers) do portal comunitário.


## <a name="create-azure-connection-and-resource-group"></a>Criar o grupo de ligação e recursos Azure

Vamos ver cada secção de um modelo terraforme. Você também pode ver a versão completa do [modelo Terraform](#complete-terraform-script) que você pode copiar e colar.

A secção `provider` diz à Terraform para usar um fornecedor Azure. Para obter valores para *subscription_id*, *client_id,* *client_secret*e *tenant_id,* consulte [Instalar e configurar terrafora.](terraform-install-configure.md) 

> [!TIP]
> Se criar variáveis ambientais para os valores ou estiver a usar a [experiência Azure Cloud Shell Bash,](/azure/cloud-shell/overview) não precisa de incluir as declarações variáveis nesta secção.

```hcl
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A seguinte secção cria um grupo de recursos chamado `myResourceGroup` no local `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

Em secções adicionais, você referencia o grupo de recursos com *${azurerm_resource_group.myterraformgroup.name}* .

## <a name="create-virtual-network"></a>Criar a rede virtual
A seguinte secção cria uma rede virtual chamada *myVnet* no espaço de *endereço10.0.0.0/16:*

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

A seguinte secção cria uma subnet chamada *mySubnet* na rede virtual *myVnet:*

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Criar endereço IP público
Para aceder a recursos através da Internet, crie e atribua um endereço IP público ao seu VM. A seguinte secção cria um endereço IP público chamado *myPublicIP*:

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
Os Grupos de Segurança da Rede controlam o fluxo de tráfego de rede dentro e fora do seu VM. A seguinte secção cria um grupo de segurança de rede chamado *myNetworkSecurityGroup* e define uma regra para permitir o tráfego sSH na porta TCP 22:

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


## <a name="create-virtual-network-interface-card"></a>Criar cartão de interface de rede virtual
Um cartão de interface de rede virtual (NIC) liga o seu VM a uma determinada rede virtual, endereço IP público e grupo de segurança de rede. A seguinte secção num modelo terraforme cria um NIC virtual chamado *myNIC* ligado aos recursos de rede virtuais que criou:

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


## <a name="create-storage-account-for-diagnostics"></a>Criar conta de armazenamento para diagnósticos
Para armazenar diagnósticos de arranque para um VM, precisa de uma conta de armazenamento. Estes diagnósticos de arranque podem ajudá-lo a resolver problemas e monitorizar o estado do seu VM. A conta de armazenamento que cria é apenas para armazenar os dados de diagnóstico de arranque. Como cada conta de armazenamento deve ter um nome único, a seguinte secção gera algum texto aleatório:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Agora pode criar uma conta de armazenamento. A seguinte secção cria uma conta de armazenamento, com o nome baseado no texto aleatório gerado na etapa anterior:

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

O passo final é criar um VM e usar todos os recursos criados. A secção seguinte cria um VM chamado *myVM* e anexa o NIC virtual chamado *myNIC*. A mais recente imagem *Ubuntu 16.04-LTS* é utilizada e um utilizador chamado *azureuser* é criado com a autenticação de palavra-passe desativada.

 Os dados-chave do SSH são fornecidos na secção *ssh_keys.* Forneça uma chave SSH pública válida no campo *key_data.*

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

## <a name="complete-terraform-script"></a>Roteiro terraforme completo

Para juntar todas estas secções e ver a Terraform em ação, crie um ficheiro chamado *terraform_azure.tf* e colá-lo o seguinte conteúdo:

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


## <a name="build-and-deploy-the-infrastructure"></a>Construir e implantar a infraestrutura
Com o seu modelo Terraform criado, o primeiro passo é inicializar terraforma. Este passo garante que a Terraform tem todos os pré-requisitos para construir o seu modelo em Azure.

```bash
terraform init
```

O próximo passo é fazer a revisão da Terraform e validar o modelo. Este passo compara os recursos solicitados com as informações estatais guardadas pela Terraform e, em seguida, produz a execução planeada. Os recursos *não* são criados em Azure.

```bash
terraform plan
```

Depois de executar o comando anterior, deve ver algo como o seguinte ecrã:

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

Se tudo estiver correto e estiver pronto para construir a infraestrutura em Azure, aplique o modelo em Terraform:

```bash
terraform apply
```

Assim que a Terraform estiver concluída, a sua infraestrutura VM está pronta. Obtenha o endereço IP público do seu VM com [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Em seguida, pode sSH para o seu VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Passos seguintes
Criou uma infraestrutura básica em Azure utilizando a Terraform. Para cenários mais complexos, incluindo exemplos que utilizam equilibradores de carga e conjuntos de escala de máquinas virtuais, consulte inúmeros [exemplos terraformes para o Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para obter uma lista atualizada de fornecedores Azure suportados, consulte a [documentação Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).
