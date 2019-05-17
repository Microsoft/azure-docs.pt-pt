---
title: Histórico de implementação Azure Resource Manager | Documentos da Microsoft
description: Descreve como ver as operações de implementação Azure Resource Manager com o portal, o PowerShell, a CLI do Azure e a REST API.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605976"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Ver histórico de implementação Azure Resource Manager

O Azure Resource Manager permite-lhe ver o seu histórico de implementação e examinar as operações específicas nas implementações anteriores. Pode ver os recursos que foram implementados e obter informações sobre os erros.

Para obter ajuda com a resolução de erros de implementação específica, consulte [resolver erros comuns quando implementar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

Para obter detalhes sobre uma implementação a partir do histórico de implementação.

1. Selecione o grupo de recursos que pretende examinar.

1. Selecione a ligação em **implementações**.

   ![Selecione o histórico de implementação](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Selecione uma das implementações do histórico de implementação.

   ![Selecione a implementação](./media/resource-manager-deployment-operations/select-details.png)

1. É apresentado um resumo da implementação, incluindo uma lista dos recursos que foram implementadas.

    ![Resumo da implementação](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Para ver o modelo utilizado para a implementação, selecione **modelo**. Pode transferir o modelo para reutilizá-la.

    ![Mostrar modelo](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Se a implementação falhou, verá uma mensagem de erro. Selecione a mensagem de erro para obter mais detalhes.

    ![Falha na implementação de ver](./media/resource-manager-deployment-operations/show-error.png)

1. É apresentada a mensagem de erro detalhadas.

    ![Ver detalhes do erro](./media/resource-manager-deployment-operations/show-details.png)

1. O ID de correlação é utilizado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação.

    ![Obter o ID de correlação](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Para saber mais sobre o passo que falhou, selecione **detalhes da operação**.

    ![Selecione as operações de implementação](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Consulte os detalhes para esse passo da implementação.

    ![Mostrar detalhes da operação](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o estado geral de uma implementação, utilize o **Get-AzResourceGroupDeployment** comando.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Em alternativa, pode filtrar os resultados para apenas essas implementações que tenham falhado.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

O ID de correlação é utilizado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação. Para obter o ID de correlação, utilize:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Cada implementação inclui várias operações. Cada operação representa uma etapa no processo de implantação. Para descobrir o que aconteceu de errado com uma implementação, terá, normalmente ver detalhes sobre as operações de implementação. Pode ver o estado das operações com **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Que retorna várias operações com cada um no seguinte formato:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Para obter mais detalhes sobre as operações com falhas, recuperar as propriedades para as operações com **falhada** estado.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Que retorna todas as operações com falhas com cada um no seguinte formato:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Tenha em atenção o serviceRequestId e trackingId para a operação. O serviceRequestId pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação. Usará o trackingId no próximo passo para se concentrar numa determinada operação.

Para obter a mensagem de estado de uma determinada operação com falha, utilize o seguinte comando:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Que retorna:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Cada operação de implementação no Azure inclui conteúdo de solicitação e resposta. Durante a implementação, pode utilizar **DeploymentDebugLogLevel** parâmetro para especificar que o pedido de e/ou resposta é registada.

Obter essas informações do log e guarde-o localmente ao utilizar os seguintes comandos do PowerShell:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>CLI do Azure

Para obter o estado geral de uma implementação, utilize o **show de implementação de grupo do azure** comando.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
O ID de correlação é utilizado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Para ver as operações de uma implementação, utilize:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Para obter informações sobre uma implementação, utilize o [obter informações sobre uma implementação de modelo](https://docs.microsoft.com/rest/api/resources/deployments) operação.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Em resposta, tenha em atenção em particular a **provisioningState**, **correlationId**, e **erro** elementos. O **correlationId** é utilizado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Para obter informações sobre implementações, utilize [listar todas as operações de implementação do modelo](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
A resposta inclui informações de pedido de e/ou resposta com base no que especificou no **debugSetting** propriedade durante a implementação.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter ajuda com a resolução de erros de implementação específica, consulte [resolver erros comuns quando implementar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como utilizar os registos de atividades para monitorizar os outros tipos de ações, veja [ver registos de atividade para gerir recursos do Azure](resource-group-audit.md).
* Para validar a sua implementação antes de executá-lo, consulte [implementar um grupo de recursos com o modelo Azure Resource Manager](resource-group-template-deploy.md).

