---
title: As operações de implementação Azure Resource Manager | Documentos da Microsoft
description: Descreve como ver as operações de implementação Azure Resource Manager com o portal, o PowerShell, a CLI do Azure e a REST API.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 9ff6388c72c631dad870a4f52f86749bfd744d85
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085631"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Ver as operações de implementação Azure Resource Manager

Pode ver as operações de uma implementação através do portal do Azure. Pode estar mais interessado em visualizar as operações quando Recebi um erro durante a implementação para que este artigo se concentra na visualização de operações que falharam. O portal fornece uma interface que permite-lhe facilmente os erros e determinar possíveis correções.

Pode solucionar sua implantação ao observar os registos de auditoria ou as operações de implementação. Este artigo mostra os dois métodos. Para obter ajuda com a resolução de erros de implementação específica, consulte [resolver erros comuns quando implementar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="portal"></a>Portal

Para ver as operações de implementação, utilize os seguintes passos:

1. Para o grupo de recursos envolvido na implementação, tenha em atenção o estado da última implementação. Pode selecionar este estado para obter mais detalhes.
   
    ![Estado da implementação](./media/resource-manager-deployment-operations/deployment-status.png)
2. Ver o histórico de implementação recente. Selecione a implementação que falharam.
   
    ![Estado da implementação](./media/resource-manager-deployment-operations/select-deployment.png)
3. Selecione a ligação para ver uma descrição do motivo pelo qual a implementação falhou. Na imagem abaixo, o registo DNS não exclusivo.  
   
    ![Falha na implementação de ver](./media/resource-manager-deployment-operations/view-error.png)
   
    Esta mensagem de erro deve ser o suficiente para que comece a resolução de problemas. No entanto, se precisar de obter mais detalhes sobre quais tarefas foram concluídas, pode exibir as operações, conforme mostrado nas etapas a seguir.
4. Pode ver todas as operações de implementação. Selecione qualquer operação para ver mais detalhes.
   
    ![ver as operações](./media/resource-manager-deployment-operations/view-operations.png)
   
    Neste caso, verá que a conta de armazenamento, a rede virtual e o conjunto de disponibilidade foram criados com êxito. O endereço IP público falha e outros recursos não foram tentados.
5. Pode ver eventos para a implementação, selecionando **eventos**.
   
    ![Ver eventos](./media/resource-manager-deployment-operations/view-events.png)
6. Ver todos os eventos para a implementação e selecionar qualquer um para obter mais detalhes. Tenha em atenção os IDs de correlação. Este valor pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação.
   
    ![Ver eventos](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Para obter o estado geral de uma implementação, utilize o **Get-AzResourceGroupDeployment** comando. 

   ```powershell
   Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
   ```

   Em alternativa, pode filtrar os resultados para apenas essas implementações que tenham falhado.

   ```powershell
   Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
   ```
   
2. Para obter o ID de correlação, utilize:

   ```powershell
   (Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
   ```

3. Cada implementação inclui várias operações. Cada operação representa uma etapa no processo de implantação. Para descobrir o que aconteceu de errado com uma implementação, terá, normalmente ver detalhes sobre as operações de implementação. Pode ver o estado das operações com **Get-AzResourceGroupDeploymentOperation**.

   ```powershell 
   Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
   ```

    Que retorna várias operações com cada um no seguinte formato:

   ```powershell
   Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
   OperationId    : A3EB2DA598E0A780
   Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
   PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
   ```

4. Para obter mais detalhes sobre as operações com falhas, recuperar as propriedades para as operações com **falhada** estado.

   ```powershell
   (Get-AzResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
   ```
   
    Que retorna todas as operações com falhas com cada um no seguinte formato:

   ```powershell
   provisioningOperation : Create
   provisioningState     : Failed
   timestamp             : 2016-06-14T21:54:55.1468068Z
   duration              : PT3.1449887S
   trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
   serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
   statusCode            : BadRequest
   statusMessage         : @{error=}
   targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
   ```

    Tenha em atenção o serviceRequestId e trackingId para a operação. O serviceRequestId pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação. Irá utilizar o trackingId no próximo passo para se concentrar numa determinada operação.
5. Para obter a mensagem de estado de uma determinada operação com falha, utilize o seguinte comando:

   ```powershell
   ((Get-AzResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
   ```

    Que retorna:

   ```powershell
   code           message                                                                        details
   ----           -------                                                                        -------
   DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
   ```
6. Cada operação de implementação no Azure inclui conteúdo de solicitação e resposta. O conteúdo do pedido é o que é enviado para o Azure, durante a implementação (por exemplo, criar uma VM, disco de SO e outros recursos). O conteúdo de resposta é o que Azure reenviadas a partir de seu pedido de implementação. Durante a implementação, pode utilizar **DeploymentDebugLogLevel** parâmetro para especificar que o pedido de e/ou resposta é mantida no registo. 

   Obter essas informações do log e guarde-o localmente ao utilizar os seguintes comandos do PowerShell:

   ```powershell
   (Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

   (Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
   ```

## <a name="azure-cli"></a>CLI do Azure

1. Obter o estado geral de uma implementação com o **show de implementação de grupo do azure** comando.

   ```azurecli
   az group deployment show -g ExampleGroup -n ExampleDeployment
   ```
  
2. Um dos valores devolvidos é o **correlationId**. Este valor é utilizado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico para resolver problemas de uma implementação.

   ```azurecli
   az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
   ```

3. Para ver as operações de uma implementação, utilize:

   ```azurecli
   az group deployment operation list -g ExampleGroup -n ExampleDeployment
   ```

## <a name="rest"></a>REST

1. Obter informações sobre uma implementação com o [obter informações sobre uma implementação de modelo](https://docs.microsoft.com/rest/api/resources/deployments) operação.

   ```http
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

2. Obter informações sobre as implementações com [listar todas as operações de implementação do modelo](https://docs.microsoft.com/rest/api/resources/deployments). 

   ```http
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

