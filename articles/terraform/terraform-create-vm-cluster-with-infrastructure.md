---
title: Tutorial-criar um cluster de VM do Azure com o Terraform e a HCL
description: Use Terraform e HCL para criar um cluster de máquina virtual Linux com um balanceador de carga no Azure
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 1ff13f05a5be463ed7477b4bbbc3e1f977a04a75
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665361"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Tutorial: criar um cluster de VM do Azure com Terraform e HCL

Neste tutorial, você verá como criar um cluster de computação pequeno usando a [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Você aprenderá a executar as seguintes tarefas:

> [!div class="checklist"]
> * Configure a autenticação do Azure.
> * Crie um arquivo de configuração do Terraform.
> * Use um arquivo de configuração do Terraform para criar um balanceador de carga.
> * Use um arquivo de configuração do Terraform para implantar duas VMs do Linux em um conjunto de disponibilidade.
> * Inicialize o Terraform.
> * Crie um plano de execução Terraform.
> * Aplique o plano de execução Terraform para criar os recursos do Azure.

## <a name="1-set-up-azure-authentication"></a>1. configurar a autenticação do Azure

> [!NOTE]
> Se [utilizar variáveis de ambiente do Terraform](/azure/virtual-machines/linux/terraform-install-configure) ou executar este tutorial no [Azure Cloud Shell](terraform-cloud-shell.md), ignore esta secção.

Nesta secção, vai gerar um principal de serviço do Azure e dois ficheiros de configuração do Terraform com as credenciais do principal de segurança.

1. [Configure um principal de serviço do Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) para permitir que o Terraform aprovisione recursos no Azure. Ao criar o principal, tenha em atenção os valores do ID da subscrição, do inquilino, do appId e da palavra-passe.

2. Abra uma linha de comandos.

3. Crie um diretório vazio para armazenar os seus ficheiros do Terraform.

4. Crie um novo ficheiro para guardar as suas declarações de variáveis. Pode dar o nome que quiser a este ficheiro, com uma extensão `.tf`.

5. Copie o seguinte código para a variável do seu ficheiro de declaração:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Crie um novo ficheiro com os valores das suas variáveis do Terraform. É comum nomear o arquivo de variável Terraform `terraform.tfvars` como Terraform carrega automaticamente qualquer arquivo chamado `terraform.tfvars` (ou seguindo um padrão de `*.auto.tfvars`) se estiver presente no diretório atual. 

7. Copie o seguinte código para o seu ficheiro de variáveis. Certifique-se de que substitui os marcadores de posição da seguinte forma: para `subscription_id`, utilize o ID da subscrição do Azure que especificou quando executou `az account set`. Para `tenant_id`, utilize o valor `tenant` devolvido de `az ad sp create-for-rbac`. Para `client_id`, utilize o valor `appId` devolvido de `az ad sp create-for-rbac`. Para `client_secret`, utilize o valor `password` devolvido de `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. criar um arquivo de configuração Terraform

Nesta secção, o utilizador cria um ficheiro com as definições dos recursos para a sua infraestrutura.

1. Crie um novo ficheiro com o nome `main.tf`. 

2. Copie as seguintes definições de recursos de exemplo para o seu novo ficheiro `main.tf` criado: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
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
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. inicializar Terraform 

O [comando init do terraform](https://www.terraform.io/docs/commands/init.html) é utilizado para inicializar um diretório com os ficheiros de configuração do Terraform que criou nas secções anteriores. É uma prática recomendada sempre executar o comando `terraform init` depois de escrever uma nova configuração de Terraform. 

> [!TIP]
> O comando `terraform init` é idempotent, o que significa que pode ser chamado repetidamente e produzir o mesmo resultado. Desta forma, se estiver a trabalhar num ambiente de colaboração e achar que os ficheiros de configuração poderão ter sido alterados, é sempre uma boa ideia chamar o comando `terraform init` antes de executar ou aplicar um plano.

Para inicializar o Terraform execute o seguinte comando:

  ```bash
  terraform init
  ```

  ![Inicializar o Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. criar um plano de execução do Terraform

O [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. Para gerar um plano de execução, o Terraform agrega todos os ficheiros `.tf` no diretório atual. 

O [parâmetro-out](https://www.terraform.io/docs/commands/plan.html#out-path) salva o plano de execução em um arquivo de saída. Esse recurso aborda problemas de simultaneidade comuns em ambientes de vários desenvolvimentos. Um desses problemas resolvido pelo arquivo de saída é o seguinte cenário:

1. O dev 1 cria o arquivo de configuração.
1. O dev 2 modifica o arquivo de configuração.
1. O dev 1 aplica-se (executa) o arquivo de configuração.
1. O dev 1 obtém resultados inesperados que não estão sabendo que o dev 2 modificou a configuração.

O dev 1 especificando um arquivo de saída impede que o dev 2 afete o dev 1. 

Se você não precisar salvar seu plano de execução, execute o seguinte comando:

  ```bash
  terraform plan
  ```

Se você precisar salvar seu plano de execução, execute o comando a seguir. Substitua os espaços reservados pelos valores apropriados para o seu ambiente.

  ```bash
  terraform plan -out=<path>
  ```

Outro parâmetro útil é [-var-File](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Por padrão, o Terraform tentou encontrar o arquivo de variáveis da seguinte maneira:
- Arquivo chamado `terraform.tfvars`
- Arquivo chamado com usando o seguinte padrão: `*.auto.tfvars`

No entanto, o arquivo de variáveis não precisa seguir nenhuma das duas convenções anteriores. Nesse caso, especifique o nome do arquivo de variáveis com o parâmetro `-var-file` em que o nome do arquivo de variável não carrega uma extensão. O exemplo a seguir ilustra esse ponto:

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform determina as ações necessárias para atingir o estado especificado no arquivo de configuração.

![Criar um plano de execução do Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. aplicar o plano de execução Terraform

O último passo deste tutorial é utilizar o [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) para aplicar o conjunto de ações geradas pelo comando `terraform plan`.

Se quiser aplicar o plano de execução mais recente, execute o seguinte comando:

  ```bash
  terraform apply
  ```

Se você quiser aplicar um plano de execução salvo anteriormente, execute o comando a seguir. Substitua os espaços reservados pelos valores apropriados para o seu ambiente:

  ```bash
  terraform apply <path>
  ```

![Aplicar um plano de execução do Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Criar um conjunto de dimensionamento de máquinas virtuais do Azure usando Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
