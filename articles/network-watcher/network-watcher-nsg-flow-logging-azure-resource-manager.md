---
title: Network Watcher - Crie registos de fluxo NSG usando um modelo de Gestor de Recursos Azure
description: Utilize um modelo de Gestor de Recursos Azure e PowerShell para configurar facilmente os Registos de Fluxo NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: a7244aebef6adcfbf96884d377592b575e2c3acb
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519882"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configurar registos de fluxo NSG de um modelo de gestor de recursos Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[O Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) é a forma nativa e poderosa da Azure de gerir a sua [infraestrutura como código.](/azure/devops/learn/what-is-infrastructure-as-code)

Este artigo mostra como você pode ativar [NSG Flow Logs](./network-watcher-nsg-flow-logging-overview.md) programáticamente usando um modelo de Gestor de Recursos Azure e Azure PowerShell. Começamos por fornecer uma visão geral das propriedades do objeto NSG Flow Log, seguido de alguns modelos de amostra. Em seguida, nós o modelo de implementação usando uma instância local PowerShell.


## <a name="nsg-flow-logs-object"></a>Objeto de registos de fluxo NSG

O objeto NSG Flow Logs com todos os parâmetros é mostrado abaixo.
Para uma visão geral completa das propriedades, pode ler a referência do [modelo NSG Flow Logs](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Para criar um recurso Microsoft.Network/networkWatchers/flowLogs, adicione o JSON acima na secção de recursos do seu modelo.


## <a name="creating-your-template"></a>Criando o seu modelo

Se estiver a utilizar os modelos do Azure Resource Manager pela primeira vez, pode saber mais sobre eles utilizando os links abaixo.

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
* [Tutorial: Criar e implementar o seu primeiro modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Abaixo estão dois exemplos de modelos completos para configurar registos de fluxo NSG.

**Exemplo 1**: A versão mais simples do que precede com parâmetros mínimos passados. O modelo abaixo permite registos de fluxo NSG num NSG alvo e armazena-os numa determinada conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * O nome do recurso tem o formato "Recurso Resource_Child dos pais". Aqui, o recurso principal é a instância regional do Observador de Rede (Formato: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centraluseuap)
> * targetResourceId é o ID de recurso do NSG alvo
> * storageId é o ID de recursos da conta de armazenamento de destino

**Exemplo 2**: Os seguintes modelos que permitem registos de fluxo NSG (versão 2) com uma retenção durante 5 dias. Ativar o Traffic Analytics com um intervalo de processamento de 10 minutos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Implementar o seu modelo do Azure Resource Manager

Este tutorial pressupõe que tem um grupo de Recursos existente e um NSG que pode ativar o fluxo de iniciar sessão.
Pode guardar qualquer um dos modelos de exemplo acima localmente como `azuredeploy.json` . Atualize os valores da propriedade para que apia recursos válidos na sua subscrição.

Para implementar o modelo, executar o seguinte comando em PowerShell.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Os comandos acima estão a implantar um recurso para o grupo de recursos NetworkWatcherRG e não para o grupo de recursos que contém o NSG


## <a name="verifying-your-deployment"></a>Verificação da sua implantação

Há algumas maneiras de verificar se a sua implantação foi bem sucedida. A sua consola PowerShell deve mostrar "ProvisioningState" como "Bem sucedido". Além disso, pode visitar a página do [portal NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar as suas alterações. Se houver problemas com a implantação, dê uma olhada nos [erros comuns de implementação do Azure com o Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Apagar o seu recurso
O Azure permite a eliminação de recursos através do modo de implementação "Completo". Para eliminar um recurso Flow Logs, especifique uma implementação no modo Completo sem incluir o recurso que pretende eliminar. Leia mais sobre o [modo de implementação completo](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os seus dados NSG Flow utilizando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](./traffic-analytics.md)