---
title: 'Quickstart: Criar um perfil e ponto final - Modelo de Gestor de Recursos'
titleSuffix: Azure Content Delivery Network
description: Saiba como criar um perfil de Rede de Entrega de Conteúdo Azure e endpoint um modelo de Gestor de Recursos
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: bdb30fed4dadef84fe012c11893661b8d9d70325
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483045"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---resource-manager-template"></a>Quickstart: Criar um perfil e ponto final do Azure CDN - Modelo de Gestor de Recursos

Inicie com a Azure CDN utilizando um modelo de gestor de recursos Azure.  Este modelo implementa um perfil e um ponto final.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-cdn-profile-and-endpoint"></a>Criar perfil cdn e ponto final

Este modelo está configurado para criar um:

* Perfil
* Ponto Final

### <a name="review-the-template"></a>Rever o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json" range="1-125" highlight="46-117":::


No modelo, está definido um recurso do Azure:

**Microsoft.Cdn**

* **[Microsoft.Cdn/perfis](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

### <a name="deploy-the-template"></a>Implementar o modelo

**CLI do Azure**

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Portal do Azure**

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **grupos** de recursos do painel esquerdo.

3. Selecione o grupo de recursos que criou na secção anterior. O nome de grupo de recursos predefinidos é **myResourceGroupCDN**

4. Verifique se foram criados os seguintes recursos no grupo de recursos:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Grupo de recursos Azure CDN" border="true":::

## <a name="clean-up-resources"></a>Limpar recursos

**CLI do Azure**

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

**Azure PowerShell**

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

**Portal do Azure**

Quando já não for necessário, elimine o grupo de recursos, o perfil cdn e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupCDN** que contém o perfil e o ponto final do CDN e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:

* Perfil CDN
* Ponto Final

Para saber mais sobre a Azure CDN e Azure Resource Manager, continue para os artigos abaixo.

* Leia uma [visão geral da Azure CDN](cdn-overview.md)
* Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)