---
title: Histórico de implantação
description: Descreve como visualizar as operações de implementação do Azure Resource Manager com o portal, PowerShell, Azure CLI e REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: b7439a70a3bd802a5f8a7c371fc04ab3eed31a5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117838"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Ver histórico de implementação com O Gestor de Recursos Azure

O Azure Resource Manager permite-lhe visualizar o seu histórico de implementação. Pode examinar operações específicas em implementações anteriores e ver quais os recursos que foram implementados. Este histórico contém informações sobre quaisquer erros.

O histórico de implantação de um grupo de recursos está limitado a 800 implementações. À medida que se aproxima do limite, as implementações são automaticamente eliminadas do histórico. Para obter mais informações, consulte [as supressões automáticas do histórico de implantação](deployment-history-deletions.md).

Para obter ajuda na resolução de erros de implementação específicos, consulte [Resolver erros comuns ao utilizar recursos para a Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Obtenha implementações e iD de correlação

Pode ver detalhes sobre uma implementação através do portal Azure, PowerShell, Azure CLI ou REST API. Cada implantação tem um ID de correlação, que é usado para rastrear eventos relacionados. Pode ser útil quando se trabalha com suporte técnico para resolver problemas de uma implantação.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione o grupo de recursos que pretende examinar.

1. Selecione o link em **Implementações**.

   ![Selecione o histórico de implementação](./media/deployment-history/select-deployment-history.png)

1. Selecione uma das implementações do histórico de implantação.

   ![Selecione implantação](./media/deployment-history/select-details.png)

1. É apresentado um resumo da implantação, incluindo o ID de correlação.

    ![Resumo da implementação](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para listar todas as implementações de um grupo de recursos, utilize o comando [Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Para obter uma implantação específica de um grupo de recursos, adicione o parâmetro **Menu de Implantação.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Para obter o ID de correlação, use:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para listar a implantação de um grupo de recursos, utilize [a lista de grupos de implantação az](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Para obter uma implantação específica, utilize o [show do grupo de implantação az](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Para obter o ID de correlação, use:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Para listar as implementações de um grupo de recursos, utilize a seguinte operação. Para que o número de versão API mais recente seja utilizado no pedido, consulte [Implementações - List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Para obter uma implantação específica. utilizar a seguinte operação. Para que o número de versão API mais recente seja utilizado no pedido, consulte [Implementações - Obter](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

A resposta inclui o ID de correlação.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Obtenha operações de implantação e mensagem de erro

Cada implantação pode incluir múltiplas operações. Para ver mais detalhes sobre uma implantação, consulte as operações de implantação. Quando uma implementação falha, as operações de implantação incluem uma mensagem de erro.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No resumo para uma implantação, selecione **detalhes da operação**.

    ![Selecione detalhes da operação](./media/deployment-history/get-operation-details.png)

1. Vê os detalhes do passo da implantação. Quando ocorre um erro, os detalhes incluem a mensagem de erro.

    ![Mostrar detalhes da operação](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para visualizar as operações de implantação para implantação num grupo de recursos, utilize o comando [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Para visualizar as operações falhadas, filtrar as operações com **estado falhado.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Para obter a mensagem de estado das operações falhadas, utilize o seguinte comando:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para visualizar as operações de implantação para implantação num grupo de recursos, utilize o comando [da lista de grupos de operação de implantação az.](/cli/azure/deployment/operation/group#az-deployment-operation-group-list) Deve ter Azure CLI 2.6.0 ou mais tarde.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Para visualizar as operações falhadas, filtrar as operações com **estado falhado.**

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Para obter a mensagem de estado das operações falhadas, utilize o seguinte comando:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Para obter operações de implantação, utilize a seguinte operação. Para o número de versão API mais recente a utilizar no pedido, consulte [Operações de Implantação - Lista](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

A resposta inclui uma mensagem de erro.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Próximos passos

* Para obter ajuda na resolução de erros de implementação específicos, consulte [Resolver erros comuns ao utilizar recursos para a Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para saber como as implementações são geridas na história, consulte [as supressões automáticas do histórico de implantação](deployment-history-deletions.md).
* Para validar a sua implementação antes de executá-la, consulte [implementar um grupo de recursos com o modelo Azure Resource Manager](deploy-powershell.md).

