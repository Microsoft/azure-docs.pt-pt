---
title: Criar um cluster de VMs com o Terraform e o HCL
description: Utilizar o HashiCorp Configuration Language (HCL) e o Terraform para criar um cluster de máquinas virtuais do Linux com um balanceador de carga no Azure
services: terraform
ms.service: azure
keywords: terraform, devops, máquina virtual, rede, módulos
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/13/2017
ms.openlocfilehash: a0358859d6f806a94c529bae2eb6fa9d1ab82963
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077841"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Criar um cluster de VMs com o Terraform e o HCL

Este tutorial demostra como criar um pequeno cluster de cálculo com o [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). A configuração cria um balanceador de carga, duas VMs do Linux num [conjunto de disponibilidade](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) e todos os recursos de rede necessários.

Neste tutorial:

> [!div class="checklist"]
> * Configurar a autenticação do Azure
> * Criar um ficheiro de configuração do Terraform
> * Inicializar o Terraform
> * Criar um plano de execução do Terraform
> * Aplicar o plano de execução do Terraform

## <a name="1-set-up-azure-authentication"></a>1. Configurar a autenticação do Azure

> [!NOTE]
> Se [utilizar variáveis de ambiente do Terraform](/azure/virtual-machines/linux/terraform-install-configure) ou executar este tutorial no [Azure Cloud Shell](terraform-cloud-shell.md), ignore esta secção.

Nesta secção, vai gerar um principal de serviço do Azure e dois ficheiros de configuração do Terraform com as credenciais do principal de segurança.

1. [Configure um principal de serviço do Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) para permitir que o Terraform aprovisione recursos no Azure. Ao criar o principal, tenha em atenção os valores do ID da subscrição, do inquilino, do appId e da palavra-passe.

2. Abra uma linha de comandos.

3. Crie um diretório vazio para armazenar os seus ficheiros do Terraform.

4. Crie um novo ficheiro para guardar as suas declarações de variáveis. Pode dar o nome que quiser a este ficheiro, com uma extensão `.tf`.

5. Copie o seguinte código para a variável do seu ficheiro de declaração:

   ```tf
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
   }
   ```

6. Crie um novo ficheiro com os valores das suas variáveis do Terraform. É normal atribuir o nome `terraform.tfvars` ao seu ficheiro de variáveis do Terraform, uma vez que o Terraform carrega automaticamente qualquer ficheiro com o nome `terraform.tfvars` (ou segue um padrão `*.auto.tfvars`), se este estiver presente no diretório atual. 

7. Copie o seguinte código para o seu ficheiro de variáveis. Certifique-se substituir os marcadores de posição da seguinte forma: Para `subscription_id`, utilize o ID de subscrição do Azure especificado durante a execução `az account set`. Para `tenant_id`, utilize o valor `tenant` devolvido de `az ad sp create-for-rbac`. Para `client_id`, utilize o valor `appId` devolvido de `az ad sp create-for-rbac`. Para `client_secret`, utilize o valor `password` devolvido de `az ad sp create-for-rbac`.

   ```tf
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Criar um ficheiro de configuração do Terraform

Nesta secção, o utilizador cria um ficheiro com as definições dos recursos para a sua infraestrutura.

1. Crie um novo ficheiro com o nome `main.tf`. 

2. Copie as seguintes definições de recursos de exemplo para o seu novo ficheiro `main.tf` criado: 

   ```tf
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Inicializar o Terraform 

O [comando init do terraform](https://www.terraform.io/docs/commands/init.html) é utilizado para inicializar um diretório com os ficheiros de configuração do Terraform que criou nas secções anteriores. É uma prática recomendada executar sempre o comando `terraform init` após escrever uma nova configuração do Terraform. 

> [!TIP]
> O comando `terraform init` é idempotent, o que significa que pode ser chamado repetidamente e produzir o mesmo resultado. Desta forma, se estiver a trabalhar num ambiente de colaboração e achar que os ficheiros de configuração poderão ter sido alterados, é sempre uma boa ideia chamar o comando `terraform init` antes de executar ou aplicar um plano.

Para inicializar o Terraform execute o seguinte comando:

  ```cmd
  terraform init
  ```

  ![Inicializar o Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Criar um plano de execução do Terraform

O [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. Para gerar um plano de execução, o Terraform agrega todos os ficheiros `.tf` no diretório atual. 

Se estiver a trabalhar num ambiente de colaboração onde a configuração poderá ser alterada entre a altura em que cria o plano de execução e a altura da aplicação do plano de execução, deve utilizar o [parâmetro -out do comando terraform plan](https://www.terraform.io/docs/commands/plan.html#out-path) para guardar o plano de execução num ficheiro. De outra forma, se estiver a trabalhar num ambiente com uma só pessoa, pode omitir o parâmetro `-out`.

Se o nome dos seus ficheiros de variáveis do Terraform não for `terraform.tfvars` e não seguir o padrão `*.auto.tfvars`, tem de especificar o nome do ficheiro com o [parâmetro -var-file do comando terraform plan](https://www.terraform.io/docs/commands/plan.html#var-file-foo) ao executar o comando `terraform plan`.

Ao processar o comando `terraform plan`, o Terraform realiza uma atualização e determina quais as ações necessárias para alcançar o estado especificado nos seus ficheiros de configuração.

Se não precisar de guardar o seu plano de execução, execute o seguinte comando:

  ```cmd
  terraform plan
  ```

Se precisar de guardar o seu plano de execução, execute o seguinte comando (substitua o marcador de posição &lt;path> pelo caminho de saída pretendido):

  ```cmd
  terraform plan -out=<path>
  ```

![Criar um plano de execução do Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Aplicar o plano de execução do Terraform

O último passo deste tutorial é utilizar o [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) para aplicar o conjunto de ações geradas pelo comando `terraform plan`.

Se quiser aplicar o plano de execução mais recente, execute o seguinte comando:

  ```cmd
  terraform apply
  ```

Se quiser aplicar um plano de execução guardado anteriormente, execute o seguinte comando (substitua o marcador de posição &lt;path> pelo caminho com o plano de execução guardado):

  ```cmd
  terraform apply <path>
  ```

![Aplicar um plano de execução do Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passos Seguintes

- Navegar na lista de [módulos do Terraform no Azure](https://registry.terraform.io/modules/Azure)
- Criar um [conjunto de dimensionamento de máquinas virtuais com o Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
