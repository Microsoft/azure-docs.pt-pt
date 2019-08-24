---
title: Instalar e configurar o Terraform para uso com o Azure | Microsoft Docs
description: Saiba como instalar e configurar o Terraform para criar recursos do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: gwallace
ms.openlocfilehash: c7251b24ccd15971a704b6b47288f49168b27039
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980885"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalar e configurar o Terraform para provisionar VMs e outras infraestruturas no Azure
 
O Terraform fornece uma maneira fácil de definir, Visualizar e implantar a infraestrutura de nuvem usando uma [linguagem de modelagem simples](https://www.terraform.io/docs/configuration/syntax.html). Este artigo descreve as etapas necessárias para usar o Terraform para provisionar recursos no Azure.

Para saber mais sobre como usar o Terraform com o Azure, visite o [Hub Terraform](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

O Terraform é instalado por padrão no [Cloud Shell](/azure/terraform/terraform-cloud-shell). Se você optar por instalar o Terraform localmente, conclua a próxima etapa, caso contrário, continue a [Configurar o acesso do Terraform ao Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Instalar o Terraform

Para instalar o Terraform, [Baixe](https://www.terraform.io/downloads.html) o pacote apropriado para seu sistema operacional em um diretório de instalação separado. O download contém um único arquivo executável, para o qual você também deve definir um caminho global. Para obter instruções sobre como definir o caminho no Linux e no Mac, vá para [esta página da Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obter instruções sobre como definir o caminho no Windows, vá para [esta página da Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Verifique a configuração do caminho com `terraform` o comando. Uma lista de opções de Terraform disponíveis é mostrada, como na seguinte saída de exemplo:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurar o acesso do Terraform ao Azure

Para habilitar o Terraform a provisionar recursos no Azure, crie uma [entidade de serviço do Azure ad](/cli/azure/create-an-azure-service-principal-azure-cli). A entidade de serviço concede aos scripts do Terraform para provisionar recursos em sua assinatura do Azure.

Se você tiver várias assinaturas do Azure, primeiro consulte sua conta com a [lista de contas AZ](/cli/azure/account#az-account-list) para obter uma lista de IDs de assinatura e valores de ID de locatário:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Para usar uma assinatura selecionada, defina a assinatura para esta sessão com o [conjunto de contas AZ](/cli/azure/account#az-account-set). Defina a `SUBSCRIPTION_ID` variável de ambiente para manter o valor do campo `id` retornado da assinatura que você deseja usar:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Agora você pode criar uma entidade de serviço para uso com Terraform. Use [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)e defina o *escopo* para sua assinatura da seguinte maneira:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Seus `appId`, `password`, e`sp_name`sãoretornados. `tenant` Anote o e `appId` `password`o.

## <a name="configure-terraform-environment-variables"></a>Configurar variáveis de ambiente Terraform

Para configurar o Terraform para usar sua entidade de serviço do Azure AD, defina as seguintes variáveis de ambiente, que são usadas pelos [módulos do Terraform do Azure](https://registry.terraform.io/modules/Azure). Você também pode definir o ambiente se estiver trabalhando com uma nuvem do Azure que não seja o público do Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Você pode usar o seguinte script de Shell de exemplo para definir essas variáveis:

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

Crie um arquivo `test.tf` em um diretório vazio e cole o script a seguir.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Salve o arquivo e, em seguida, inicialize a implantação do Terraform. Esta etapa baixa os módulos do Azure necessários para criar um grupo de recursos do Azure.

```bash
terraform init
```

O resultado é semelhante ao seguinte exemplo:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Você pode visualizar as ações a serem concluídas pelo script Terraform com `terraform plan`. Quando estiver pronto para criar o grupo de recursos, aplique o plano do Terraform da seguinte maneira:

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

Neste artigo, você instalou o Terraform ou usou o Cloud Shell para configurar as credenciais do Azure e iniciar a criação de recursos em sua assinatura do Azure. Para criar uma implantação mais completa do Terraform no Azure, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com Terraform](terraform-create-complete-vm.md)
