---
title: Instalar e configurar o Terraform para utilização com o Azure | Documentos da Microsoft
description: Saiba como instalar e configurar o Terraform para criar recursos do Azure
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
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 71cf07b227a75e53119f2f35e79ccd7926b551e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60418868"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalar e configurar o Terraform para aprovisionar VMs e outras infraestruturas no Azure
 
Terraform fornece uma forma fácil de definir, visualização e implementar a infraestrutura de nuvem, utilizando um [linguagem de modelagem simples](https://www.terraform.io/docs/configuration/syntax.html). Este artigo descreve os passos necessários para utilizar o Terraform para aprovisionar os recursos no Azure.

Para saber mais sobre como utilizar o Terraform com o Azure, visite o [Terraform Hub](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform é instalado por predefinição no [Cloud Shell](/azure/terraform/terraform-cloud-shell). Se optar por instalar o Terraform localmente, concluir o passo seguinte, caso contrário, avance para [configurar o acesso do Terraform no Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Instalar o Terraform

Para instalar o Terraform, [transferir](https://www.terraform.io/downloads.html) o pacote apropriado para o seu sistema operacional num separado de diretório de instalação. O download contém um único arquivo executável, para que também deve definir um caminho global. Para obter instruções sobre como definir o caminho no Linux e Mac, aceda a [esta página da Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obter instruções sobre como definir o caminho no Windows, aceda a [esta página da Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Verificar a configuração de caminho com o `terraform` comando. É apresentada uma lista de opções do Terraform disponíveis, como no resultado do exemplo seguinte:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurar o acesso do Terraform no Azure

Para ativar o Terraform para Aprovisionar recursos para o Azure, crie uma [principal de serviço do Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). O principal de serviço concede aos seus scripts de Terraform para aprovisionar os recursos na sua subscrição do Azure.

Se tiver várias subscrições do Azure, primeiro consultar a sua conta com [show de conta de az](/cli/azure/account#az-account-show) para obter valores de ID de inquilino e ID de uma lista de subscrição:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Para utilizar uma subscrição selecionada, defina a subscrição para esta sessão com [conjunto de conta de az](/cli/azure/account#az-account-set). Definir o `SUBSCRIPTION_ID` variável de ambiente para conter o valor retornado de `id` campo da subscrição que pretende utilizar:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Agora, pode criar um principal de serviço para utilização com o Terraform. Uso [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)e defina a *âmbito* à sua subscrição da seguinte forma:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Sua `appId`, `password`, `sp_name`, e `tenant` são devolvidos. Anote o `appId` e `password`.

## <a name="configure-terraform-environment-variables"></a>Configurar variáveis de ambiente do Terraform

Para configurar o Terraform para utilizar o seu principal de serviço do Azure AD, defina as seguintes variáveis de ambiente, que, em seguida, são utilizadas pela [Azure Terraform modules](https://registry.terraform.io/modules/Azure). Também pode definir o ambiente de se trabalhar com uma cloud do Azure em vez de em público do Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Pode utilizar o seguinte exemplo de script de shell para definir essas variáveis:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Executar um script de exemplo

Crie um ficheiro `test.tf` num diretório vazio e cole o seguinte script.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Guarde o ficheiro e, em seguida, Inicialize a implementação do Terraform. Este passo transfere os módulos do Azure necessários para criar um grupo de recursos do Azure.

```bash
terraform init
```

O resultado é semelhante ao seguinte exemplo:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Pode visualizar as ações a serem concluídas pelo script do Terraform com `terraform plan`. Quando estiver pronto para criar o grupo de recursos, aplique o seu plano de Terraform da seguinte forma:

```bash
terraform apply
```

O resultado é semelhante ao seguinte exemplo:

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, instalado Terraform ou utilizado o Cloud Shell para configurar as credenciais do Azure e começar a criar recursos na sua subscrição do Azure. Para criar uma implementação mais completa do Terraform no Azure, consulte o artigo seguinte:

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](terraform-create-complete-vm.md)
