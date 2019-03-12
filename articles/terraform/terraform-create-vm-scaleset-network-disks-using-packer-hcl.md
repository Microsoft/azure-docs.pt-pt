---
title: Utilizar o Terraform para criar um conjunto de dimensionamento de máquinas virtuais do Azure a partir de uma imagem personalizada do Packer
description: Utilize o Terraform para configurar e criar a versão de um conjunto de dimensionamento de máquinas virtuais do Azure a partir de uma imagem personalizada gerada pelo Packer (incluindo uma rede virtual e discos anexados geridos).
services: terraform
ms.service: azure
keywords: terraform, programadores, conjunto de dimensionamento, máquina virtual, rede, armazenamento, módulos, imagens personalizadas, packer
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/29/2017
ms.openlocfilehash: b9c7c20b6b3bbad8731dc00e2c6e3d88f92936df
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765412"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Utilizar o Terraform para criar um conjunto de dimensionamento de máquinas virtuais do Azure a partir de uma imagem personalizada do Packer

Neste tutorial, vai utilizar o [Terraform](https://www.terraform.io/) para criar e implementar um [conjunto de dimensionamento de máquinas virtuais do Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) criado com uma imagem personalizada produzida com o [Packer](https://www.packer.io/intro/index.html) com discos geridos que utilizam o [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a sua implementação do Terraform
> * Utilizar variáveis e resultados para a implementação do Terraform 
> * Criar e implementar uma infraestrutura de rede
> * Criar uma imagem de máquina virtual personalizada com o Packer
> * Criar e implementar um conjunto de dimensionamento de máquinas virtuais com a imagem personalizada
> * Criar e implementar uma jumpbox 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
> * [Instale o Terraform e configurar o acesso ao Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Crie um par de chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys), se ainda não tiver um
> * [Instale o Packer](https://www.packer.io/docs/install/index.html), se ainda não tiver o Packer instalado no seu computador local


## <a name="create-the-file-structure"></a>Criar a estrutura de ficheiros

Crie três novos ficheiros num diretório vazio com os seguintes nomes:

- ```variables.tf``` – este ficheiro contém os valores das variáveis utilizadas no modelo.
- ```output.tf``` – este ficheiro descreve as definições que são apresentadas após a implementação.
- ```vmss.tf``` – este ficheiro contém o código da infraestrutura que está a implementar.

##  <a name="create-the-variables"></a>Criar as variáveis 

Neste passo, irá definir as variáveis que personalizam os recursos criados pelo Terraform.

Edite o ficheiro `variables.tf`, copie o código seguinte e, em seguida, guarde as alterações.

```tf 
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> O valor predefinido da variável resource_group_name não está definido: defina o seu próprio valor.

Guarde o ficheiro.

Ao implementar o modelo do Terraform, deve obter o nome de domínio completamente qualificado que é utilizado para aceder à aplicação. Utilize o tipo de recurso ```output``` do Terraform e obtenha a propriedade ```fqdn``` do recurso. 

Edite o ficheiro `output.tf` e copie o código seguinte para expor o nome de domínio completamente qualificado para as máquinas virtuais. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definir a infraestrutura de rede num modelo 

Nesta etapa, irá criar a seguinte infraestrutura de rede num novo grupo de recursos do Azure: 
  - Uma VNET com o espaço de endereços 10.0.0.0/16 
  - Uma sub-rede com o espaço de endereços 10.0.2.0/24
  - Dois endereços IP públicos. Um utilizado pelo balanceador de carga do conjunto de dimensionamento de máquinas virtuais; e o outro utilizado para ligar à jumpbox SSH

Precisa também de um grupo de recursos onde todos os recursos são criados. 

Edite e copie o seguinte código para o ficheiro ```vmss.tf```: 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Recomendamos que etiquete os recursos a implementar no Azure para facilitar a sua identificação no futuro.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede

Inicialize o ambiente do Terraform ao executar o seguinte comando no diretório onde criou os ficheiros `.tf`:

```bash
terraform init 
```
 
Os plug-ins do fornecedor são transferidos a partir do registo do Terraform para a pasta ```.terraform``` no diretório onde executou o comando.

Execute o seguinte comando para implementar a infraestrutura no Azure.

```bash
terraform apply
```

Verifique se o nome de domínio completamente qualificado do endereço IP público corresponde à sua configuração.

![Nome de domínio completamente qualificado do conjunto de dimensionamento de máquinas virtuais do Terraform para o endereço IP Público](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

O grupo de recursos contém os seguintes recursos:

![Recursos de rede do conjunto de dimensionamento de máquinas virtuais do Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Criar uma imagem do Azure com o Packer
Crie uma imagem personalizada do Linux através dos passos descritos no tutorial [How to use Packer to create Linux virtual machine images in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer) (Como utilizar o Packer para criar imagens de máquinas virtuais do Linux no Azure).
 
Siga o tutorial para criar uma imagem de Ubuntu desaprovisionada com o NGINX instalado.

![Depois de criar a imagem do Packer, tem uma imagem](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Para efeitos deste tutorial, na imagem do Packer, é executado um comando para instalar o nginx. Pode também executar o seu próprio script durante a criação.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Editar a infraestrutura para adicionar o conjunto de dimensionamento de máquinas virtuais

Neste passo, vai criar os seguintes recursos na rede que foi anteriormente implementada:
- O balanceador de carga do Azure para servir a aplicação e anexá-la ao endereço IP público que foi implementado no passo 4
- Um balanceador de carga do Azure e regras para servir a aplicação e anexá-la ao endereço IP público configurado anteriormente
- Um conjunto de endereços de back-end do Azure e atribuí-lo ao balanceador de carga 
- Uma porta de sonda de estado de funcionamento utilizada pela aplicação e configurada no balanceador de carga 
- Um conjunto de dimensionamento de máquinas virtuais por detrás do balanceador de carga que é executado na VNET implementada anteriormente
- [Nginx](http://nginx.org/) nos nós do conjunto de dimensionamento de máquinas virtuais instalado a partir da imagem personalizada


Adicione o seguinte código ao fim do ficheiro `vmss.tf`.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Personalize a implementação, ao adicionar o seguinte código a `variables.tf`:

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Implementar o conjunto de dimensionamento de máquinas virtuais no Azure

Execute o seguinte comando para visualizar a implementação do conjunto de dimensionamento de máquinas virtuais:

```bash
terraform plan
```

O resultado desse comando tem o aspeto da imagem seguinte:

![Adição do plano do conjunto de dimensionamento de máquinas virtuais pelo Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Implemente os recursos adicionais no Azure: 

```bash
terraform apply 
```

O conteúdo do grupo de recursos tem o aspeto da imagem seguinte:

![Grupo de recursos do conjunto de dimensionamento de máquinas virtuais do Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Abra um browser e ligue ao nome de domínio completamente qualificado que foi devolvido pelo comando. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Adicionar uma jumpbox à rede existente 

Este passo opcional ativa o acesso SSH às instâncias do conjunto de dimensionamento de máquinas virtuais através de uma jumpbox.

Adicione os seguintes recursos à sua implementação existente:
- Uma interface de rede ligada à mesma sub-rede que o conjunto de dimensionamento de máquinas virtuais
- Uma máquina virtual com esta interface de rede

Adicione o seguinte código ao fim do ficheiro `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Edite `outputs.tf` para adicionar o seguinte código que apresenta o nome do anfitrião da jumpbox quando a implementação é concluída:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Implementar a jumpbox

Implemente a jumpbox.

```bash
terraform apply 
```

Depois de concluída a implementação, o conteúdo do grupo de recursos é semelhante ao apresentado na imagem seguinte:

![Grupo de recursos do conjunto de dimensionamento de máquinas virtuais do Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> A opção de iniciar sessão com uma palavra-passe está desativada na jumpbox e no conjunto de dimensionamento de máquinas virtuais que implementou. Inicie sessão com SSH para aceder às VMs.

## <a name="clean-up-the-environment"></a>Limpar o ambiente

Os seguintes comandos eliminam os recursos criados neste tutorial:

```bash
terraform destroy
```

Escreva `yes` quando lhe for pedido para confirmar a eliminação dos recursos. O processo de destruição pode demorar alguns minutos a ser concluído.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou um conjunto de dimensionamento de máquinas virtuais e uma jumpbox no Azure através do Terraform. Aprendeu a:

> [!div class="checklist"]
> * Inicializar a implementação do Terraform
> * Utilizar variáveis e resultados para a implementação do Terraform 
> * Criar e implementar uma infraestrutura de rede
> * Criar uma imagem de máquina virtual personalizada com o Packer
> * Criar e implementar um conjunto de dimensionamento de máquinas virtuais com a imagem personalizada
> * Criar e implementar uma jumpbox 