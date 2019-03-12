---
title: Utilizar módulos do Terraform para criar um cluster de VMs no Azure
description: Aprenda a utilizar módulos do Terraform para criar um cluster de máquina virtual do Windows no Azure
services: terraform
ms.service: azure
keywords: terraform, devops, máquina virtual, rede, módulos
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: c6aa780b04c85b8156463011c2b90da2da4541f6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776279"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Criar um cluster de VMs com o Terraform através do Registo de Módulo

Este artigo explica como criar um pequeno cluster de VM com o [módulo de computação do Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) do Terraform. Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
> * Configurar autenticação com o Azure
> * Criar o modelo do Terraform
> * Visualizar as alterações com o plano
> * Aplicar a configuração para criar o cluster da VM

Para obter mais informações sobre o Terraform, veja a [documentação do Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Configurar autenticação com o Azure

> [!TIP]
> Se [utilizar variáveis de ambiente do Terraform](/azure/virtual-machines/linux/terraform-install-configure) ou executar este tutorial no [Azure Cloud Shell](/azure/cloud-shell/overview), ignore este passo.

 Reveja [Instalar o Terraform e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure) para criar um principal de serviço do Azure. Utilize este principal de serviço para preencher um novo ficheiro `azureProviderAndCreds.tf` num diretório vazio com o código a seguir:

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Criar o modelo

Criar um novo modelo do Terraform com o nome `main.tf` com o código a seguir:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Execute `terraform init` no seu diretório de configuração. Utilizar uma versão do Terraform de, pelo menos, 0.10.6 mostra o resultado seguinte:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualizar as alterações com o plano

Execute `terraform plan` para pré-visualizar a infraestrutura da máquina virtual criada pelo modelo.

![Plano do Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Crie as máquinas virtuais com o apply

Execute `terraform apply` para aprovisionar as VMs no Azure.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passos Seguintes

- Navegar na lista de [módulos do Terraform no Azure](https://registry.terraform.io/modules/Azure)
- Criar um [conjunto de dimensionamento de máquinas virtuais com o Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
