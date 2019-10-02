---
title: Cota de implantação do Azure excedida
description: Descreve como resolver o erro de ter mais de 800 implantações no histórico do grupo de recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/01/2019
ms.author: tomfitz
ms.openlocfilehash: f06aff74e2cf800d44115f34921825122b09a9e7
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719438"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Resolver erro quando a contagem de implantação exceder 800

Cada grupo de recursos é limitado a 800 implantações em seu histórico de implantação. Este artigo descreve o erro que você recebe quando uma implantação falha porque ela excederia as implantações permitidas do 800. Para resolver esse erro, exclua as implantações do histórico do grupo de recursos. A exclusão de uma implantação do histórico não afeta nenhum dos recursos que foram implantados.

## <a name="symptom"></a>Sintoma

Durante a implantação, você receberá um erro informando que a implantação atual excederá a cota de implantações de 800.

## <a name="solution"></a>Solução

### <a name="azure-cli"></a>CLI do Azure

Use o comando [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete) para excluir implantações do histórico.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Você pode obter a contagem atual no histórico de implantação com o seguinte comando:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Use o comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) para excluir implantações do histórico.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Você pode obter a contagem atual no histórico de implantação com o seguinte comando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

