---
title: Quota de implantação excedida
description: Descreve como resolver o erro de ter mais de 800 implementações na história do grupo de recursos.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245094"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Resolver erro quando a contagem de distribuição exceder 800

Cada grupo de recursos está limitado a 800 implantações no seu histórico de implantação. Este artigo descreve o erro que recebe quando uma implementação falha porque excederia as 800 implementações permitidas. Para resolver este erro, elimine as implementações do histórico do grupo de recursos. Apagar uma implantação da história não afeta nenhum dos recursos que foram implantados.

## <a name="symptom"></a>Sintoma

Durante a implantação, recebe um erro indicando que a implantação atual excederá a quota de 800 implantações.

## <a name="solution"></a>Solução

### <a name="azure-cli"></a>CLI do Azure

Utilize o grupo de [implantação az eliminar](/cli/azure/group/deployment) o comando para eliminar as implementações do histórico.

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

* [Aplicativos da Lógica Azure e soluções PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Extensão AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
