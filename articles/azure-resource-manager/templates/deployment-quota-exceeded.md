---
title: Quota de implantação excedida
description: Descreve como resolver o erro de ter mais de 800 implementações no histórico do grupo de recursos.
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 8996d7817eea2f8daf44fbc9b4416c884b05940f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87987057"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Resolver erro quando a contagem de implementação excede 800

Cada grupo de recursos está limitado a 800 implementações no seu histórico de implantação. Este artigo descreve o erro que recebe quando uma implementação falha porque excederia as 800 implementações permitidas. Para resolver este erro, elimine as implementações do histórico do grupo de recursos. Excluir uma implantação da história não afeta nenhum dos recursos que foram mobilizados.

O Azure Resource Manager elimina automaticamente as implementações do seu histórico à medida que se aproxima do limite. Pode ainda ver este erro por uma das seguintes razões:

1. Tem um bloqueio CanNotDelete no grupo de recursos que impede as supressões do histórico de implantação.
1. Optou por não fazer supressões automáticas.
1. Tem um grande número de implementações em execução simultaneamente e as eliminações automáticas não são processadas rapidamente o suficiente para reduzir o número total.

Para obter informações sobre a remoção do bloqueio ou a exclusão automática, consulte [supressões automáticas do histórico de implantação](deployment-history-deletions.md).

Este artigo descreve como apagar manualmente as implementações da história.

## <a name="symptom"></a>Sintoma

Durante a implantação, recebe um erro indicando que a implementação atual excederá a quota de 800 implementações.

## <a name="solution"></a>Solução

### <a name="azure-cli"></a>CLI do Azure

Utilize o [grupo de implantação az eliminar](/cli/azure/group/deployment) o comando para eliminar as implementações do histórico.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Para eliminar todas as implementações com mais de cinco dias, utilize:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Pode obter a contagem atual no histórico de implantação com o seguinte comando:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) para eliminar as implementações do histórico.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Para eliminar todas as implementações com mais de cinco dias, utilize:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Pode obter a contagem atual no histórico de implantação com o seguinte comando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Soluções de terceiros

As seguintes soluções externas abordam cenários específicos:

* [Aplicativos Azure Logic e soluções PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Extensão AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
