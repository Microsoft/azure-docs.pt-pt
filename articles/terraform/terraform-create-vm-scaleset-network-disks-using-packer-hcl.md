---
title: Tutorial - Crie um conjunto de máquinas virtuais Azure a partir de uma imagem personalizada packer usando terrafora
description: Utilize o Terraform para configurar e criar a versão de um conjunto de dimensionamento de máquinas virtuais do Azure a partir de uma imagem personalizada gerada pelo Packer (incluindo uma rede virtual e discos anexados geridos).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472214"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Tutorial: Criar um conjunto de máquinas virtuais Azure a partir de uma imagem personalizada packer usando terrafora

Neste tutorial, você usa [terrafora](https://www.terraform.io/) para criar e implementar um conjunto de [escala de máquina virtual Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) criado com uma imagem personalizada produzida usando [Packer](https://www.packer.io/intro/index.html) com discos geridos que usam a Linguagem de [Configuração HashiCorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL). 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instale a sua implantação terraforme.
> * Utilize variáveis e saídas para a implantação da Terraform.
> * Criar e implantar uma infraestrutura de rede.
> * Crie uma imagem de máquina virtual personalizada utilizando o Packer.
> * Crie e implemente uma escala de máquina virtual utilizando a imagem personalizada.
> * Crie e implante uma caixa de salto.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- **Terraform**: [Instale terraforma e configure o acesso ao Azure](terraform-install-configure.md).
- **Par de teclas SSH**: Criar um par de [teclas SSH](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Packer**: [Instale packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>Criar a estrutura de ficheiros

Crie três novos ficheiros num diretório vazio com os seguintes nomes:

- `variables.tf`: Este ficheiro contém os valores das variáveis utilizadas no modelo.
- `output.tf`: Este ficheiro descreve as definições que se vislumbem após a implantação.
- `vmss.tf`: Este ficheiro contém o código da infraestrutura que está a implantar.

##  <a name="create-the-variables"></a>Criar as variáveis 

Neste passo, irá definir as variáveis que personalizam os recursos criados pelo Terraform.

Editar `variables.tf` o ficheiro, copiar o seguinte código e, em seguida, guardar as alterações.

```hcl
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
> O valor padrão da variável resource_group_name é desdefinido. Defina o seu próprio valor.

Guarde o ficheiro.

Ao implementar o seu modelo Terraform, pretende obter o nome de domínio totalmente qualificado que é usado para aceder à aplicação. Utilize o tipo de recurso `output` do Terraform e obtenha a propriedade `fqdn` do recurso. 

Edite o ficheiro `output.tf` e copie o código seguinte para expor o nome de domínio completamente qualificado para as máquinas virtuais. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definir a infraestrutura de rede num modelo 

Nesta etapa, irá criar a seguinte infraestrutura de rede num novo grupo de recursos do Azure: 
  - Uma rede virtual com o espaço de endereço de 10.0.0.0.0/16.
  - Uma sub-rede com o espaço de endereço de 10.0.2.0/24.
  - Dois endereços IP públicos. Um é utilizado pelo equilíbrio de carga de conjunto de máquinavirtual. O outro é usado para ligar à caixa de salto SSH.

Precisa também de um grupo de recursos onde todos os recursos são criados. 

Edite e copie o seguinte código para o ficheiro `vmss.tf`: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Marque os recursos que estão a ser implantados no Azure para facilitar a sua identificação no futuro.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede

Inicialize o ambiente do Terraform ao executar o seguinte comando no diretório onde criou os ficheiros `.tf`:

```bash
terraform init 
```
 
Os plug-ins do fornecedor descarregam do `.terraform` registo Terraform para a pasta no diretório onde dirigia o comando.

Execute o seguinte comando para implementar a infraestrutura no Azure.

```bash
terraform apply
```

Verifique se o nome de domínio completamente qualificado do endereço IP público corresponde à sua configuração.

![Conjunto de escala de máquina virtual Terraform nome de domínio totalmente qualificado para endereço IP público](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

O grupo de recursos contém os seguintes recursos:

![Recursos de rede do conjunto de dimensionamento de máquinas virtuais do Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Criar uma imagem Azure usando packer
Crie uma imagem linux personalizada seguindo os passos do tutorial Como usar o [Packer para criar imagens de máquinas virtuais Linux em Azure](/azure/virtual-machines/linux/build-image-with-packer).
 
Siga o tutorial para criar uma imagem Ubuntu desprovisionada com Nginx instalada.

![Depois de criar a imagem Packer, tem uma imagem](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Para efeitos deste tutorial, na imagem packer, é executado um comando para instalar Nginx. Pode também executar o seu próprio script durante a criação.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Editar a infraestrutura para adicionar o conjunto de dimensionamento de máquinas virtuais

Neste passo, vai criar os seguintes recursos na rede que foi anteriormente implementada:
- Um equilibrador de carga Azure para servir a aplicação. Prenda-o ao endereço IP público que foi implantado anteriormente.
- Um equilibrador de carga Azure e regras para servir a aplicação. Prenda-o ao endereço IP público que foi configurado anteriormente.
- Uma piscina de endereços azure back-end. Atribua-o ao equilibrador de carga.
- Uma porta de sonda de saúde utilizada pela aplicação e configurada no equilibrador de carga.
- Um conjunto de escala de máquina virtual que fica atrás do equilibrador de carga e funciona na rede virtual que foi implementada anteriormente.
- [Nginx](https://nginx.org/) nos nódosos da escala virtual da máquina instalada a partir de uma imagem personalizada.


Adicione o seguinte código ao fim do ficheiro `vmss.tf`.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
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
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Personalize a implementação, ao adicionar o seguinte código a `variables.tf`:

```hcl
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
- Uma interface de rede ligada à mesma subnet que o conjunto de escala de máquina virtual
- Uma máquina virtual com esta interface de rede

Adicione o seguinte código ao fim do ficheiro `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
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
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Editar `outputs.tf` para adicionar o seguinte código que exibe o nome de anfitrião da caixa de salto quando a implementação terminar:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Implementar a jumpbox

Implemente a jumpbox.

```bash
terraform apply 
```

Após a implementação ter concluído, o conteúdo do grupo de recursos parece a seguinte imagem:

![Grupo de recursos do conjunto de dimensionamento de máquinas virtuais do Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> O sessão com uma palavra-passe é desativado na caixa de salto e na balança de máquinavirtual que implementou. Inscreva-se na SSH para aceder aos VMs.

## <a name="clean-up-the-environment"></a>Limpar o ambiente

Os seguintes comandos eliminam os recursos criados neste tutorial:

```bash
terraform destroy
```

Introduza *sim* quando lhe pedirem para confirmar a eliminação dos recursos. O processo de destruição pode demorar alguns minutos a ser concluído.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre a utilização da Terraform em Azure](/azure/terraform)
