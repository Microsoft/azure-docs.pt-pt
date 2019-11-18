---
title: Cota de implantação excedida
description: Descreve como resolver o erro de ter mais de 800 implantações no histórico do grupo de recursos.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 7f389827513562a3add67f022fec360081754b02
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147202"
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

Para excluir todas as implantações com mais de cinco dias, use:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az group deployment list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az group deployment delete --resource-group exampleGroup --name $deployment
done
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

Para excluir todas as implantações com mais de cinco dias, use:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Você pode obter a contagem atual no histórico de implantação com o seguinte comando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Soluções de terceiros

As soluções externas a seguir abordam cenários específicos:

* [Aplicativos lógicos do Azure e soluções do PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Extensão AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
