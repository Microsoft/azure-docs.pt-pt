---
title: 'Quickstart: Criar um perfil e ponto final - Modelo de Gestor de Recursos'
titleSuffix: Azure Content Delivery Network
description: Neste arranque rápido, aprenda a criar um perfil de Rede de Entrega de Conteúdos Azure e aponte um modelo de Gestor de Recursos
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: 31f0510ed50c9d48a46524a353d7c872b368f75c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779044"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Quickstart: Criar um perfil de CDN azul e ponto final - modelo ARM

Inicie-se com a Rede de Entrega de Conteúdos Azure (CDN) utilizando um modelo de Gestor de Recursos Azure (modelo ARM). O modelo implementa um perfil e um ponto final.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Este modelo está configurado para criar um:

* Perfil
* Ponto final

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

No modelo, está definido um recurso do Azure:

* **[Microsoft.Cdn/perfis](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Implementar o modelo

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portal

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **grupos** de recursos do painel esquerdo.

3. Selecione o grupo de recursos que criou na secção anterior. O nome de grupo de recursos predefinidos é **myResourceGroupCDN**

4. Verifique se foram criados os seguintes recursos no grupo de recursos:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Grupo de recursos Azure CDN" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="azure-cli"></a>CLI do Azure

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portal

Quando já não for necessário, elimine o grupo de recursos, o perfil cdn e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupCDN** que contém o perfil e o ponto final do CDN e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:

* Perfil CDN
* Ponto final

Para saber mais sobre a Azure CDN e Azure Resource Manager, continue para os artigos abaixo.

> [!div class="nextstepaction"]
> [Tutorial: Use o CDN para servir conteúdo estático a partir de uma aplicação web](cdn-add-to-web-app.md)
