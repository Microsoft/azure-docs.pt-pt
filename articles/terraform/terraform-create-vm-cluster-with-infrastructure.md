---
title: Tutorial - Criar um cluster Azure VM com Terraform e HCL
description: Neste tutorial, você usa Terraform e HCL para criar um cluster de máquinas virtuais Linux com um equilibrador de carga em Azure
keywords: azure devops terraform vm virtual cluster de máquina
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ae1b8eac15309ff27297d9472e70d32e68acaaac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945275"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Tutorial: Criar um cluster Azure VM com Terraform e HCL

Neste tutorial, você vê como criar um pequeno cluster de computação usando [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Aprenderá a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Instale a autenticação Azure.
> * Crie um ficheiro de configuração Terraform.
> * Utilize um ficheiro de configuração Terraform para criar um equilibrador de carga.
> * Utilize um ficheiro de configuração Terraform para implantar dois VMs Linux num conjunto de disponibilidade.
> * Inicie o Terraform.
> * Crie um plano de execução terraforme.
> * Aplique o plano de execução terraforme para criar os recursos Azure.

## <a name="1-set-up-azure-authentication"></a>1. Configurar a autenticação Azure

> [!NOTE]
> Se [utilizar variáveis de ambiente do Terraform](terraform-install-configure.md) ou executar este tutorial no [Azure Cloud Shell](terraform-cloud-shell.md), ignore esta secção.

Nesta secção, vai gerar um principal de serviço do Azure e dois ficheiros de configuração do Terraform com as credenciais do principal de segurança.

1. [Configure um principal de serviço do Azure AD](terraform-install-configure.md#set-up-terraform-access-to-azure) para permitir que o Terraform aprovisione recursos no Azure. Ao criar o principal, tenha em atenção os valores do ID da subscrição, do inquilino, do appId e da palavra-passe.

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
      version = "~>1.40"
     
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Crie um novo ficheiro com os valores das suas variáveis do Terraform. É comum nomear o seu `terraform.tfvars` ficheiro variável Terraform como `terraform.tfvars` Terraform automaticamente `*.auto.tfvars`carrega qualquer ficheiro nomeado (ou seguindo um padrão de ) se presente no diretório atual. 

7. Copie o seguinte código para o seu ficheiro de variáveis. Certifique-se de que substitui os marcadores de posição da seguinte forma: para `subscription_id`, utilize o ID da subscrição do Azure que especificou quando executou `az account set`. Para `tenant_id`, utilize o valor `tenant` devolvido de `az ad sp create-for-rbac`. Para `client_id`, utilize o valor `appId` devolvido de `az ad sp create-for-rbac`. Para `client_secret`, utilize o valor `password` devolvido de `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Criar um ficheiro de configuração Terraform

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

## <a name="3-initialize-terraform"></a>3. Inicializar terraforma 

O [comando init do terraform](https://www.terraform.io/docs/commands/init.html) é utilizado para inicializar um diretório com os ficheiros de configuração do Terraform que criou nas secções anteriores. É uma boa prática sempre `terraform init` dirigir o comando depois de escrever uma nova configuração Terraforme. 

> [!TIP]
> O comando `terraform init` é idempotent, o que significa que pode ser chamado repetidamente e produzir o mesmo resultado. Desta forma, se estiver a trabalhar num ambiente de colaboração e achar que os ficheiros de configuração poderão ter sido alterados, é sempre uma boa ideia chamar o comando `terraform init` antes de executar ou aplicar um plano.

Para inicializar o Terraform execute o seguinte comando:

  ```bash
  terraform init
  ```

  ![Inicializar o Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Criar um plano de execução terraforme

O [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. Para gerar um plano de execução, o Terraform agrega todos os ficheiros `.tf` no diretório atual. 

O [parâmetro-out](https://www.terraform.io/docs/commands/plan.html#out-path) salva o plano de execução para um ficheiro de saída. Esta funcionalidade aborda questões de condivisões comuns em ambientes multi-dev. Um desses problemas resolvidos pelo ficheiro de saída é o seguinte cenário:

1. Dev 1 cria o ficheiro de configuração.
1. Dev 2 modifica o ficheiro de configuração.
1. Dev 1 aplica (executa) o ficheiro de configuração.
1. Dev 1 obtém resultados inesperados sem saber que Dev 2 modificou a configuração.

Dev 1 especificando um ficheiro de saída impede que o Dev 2 afete o Dev 1. 

Se não precisar de salvar o seu plano de execução, faça o seguinte comando:

  ```bash
  terraform plan
  ```

Se precisar de salvar o seu plano de execução, faça o seguinte comando. Substitua os espaços reservados por valores adequados para o seu ambiente.

  ```bash
  terraform plan -out=<path>
  ```

Outro parâmetro útil é [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Por padrão, a Terraform tentou encontrar o seu ficheiro de variáveis da seguinte forma:
- Arquivo nomeado`terraform.tfvars`
- Ficheiro nomeado com o seguinte padrão:`*.auto.tfvars`

No entanto, o seu ficheiro de variáveis não precisa de seguir nenhuma das duas convenções anteriores. Nesse caso, especifique o `-var-file` nome do ficheiro das suas variáveis com o parâmetro onde o nome do ficheiro variável não tem uma extensão. O exemplo que se segue ilustra este ponto:

```hcl
terraform plan -var-file <my-variables-file>
```

A Terraform determina as ações necessárias para alcançar o estado especificado no ficheiro de configuração.

![Criar um plano de execução do Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Aplicar o plano de execução terraforme

O último passo deste tutorial é utilizar o [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) para aplicar o conjunto de ações geradas pelo comando `terraform plan`.

Se quiser aplicar o plano de execução mais recente, execute o seguinte comando:

  ```bash
  terraform apply
  ```

Se quiser aplicar um plano de execução previamente guardado, execute o seguinte comando. Substitua os espaços reservados por valores adequados para o seu ambiente:

  ```bash
  terraform apply <path>
  ```

![Aplicar um plano de execução do Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Criar um conjunto de escala de máquina virtual Azure usando terrafora](terraform-create-vm-scaleset-network-disks-hcl.md)