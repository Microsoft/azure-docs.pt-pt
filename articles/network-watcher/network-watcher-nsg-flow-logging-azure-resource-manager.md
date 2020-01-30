---
title: Network Watcher - Crie registos de fluxo NSG usando um modelo de Gestor de Recursos Azure
description: Utilize um modelo de Gestor de Recursos Azure e PowerShell para configurar facilmente os registos de fluxo NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 2ae6314accf5ad9143e1e103239c2e7c884ec947
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776641"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configure os registos de fluxo nsg de um modelo de Gestor de Recursos Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[O Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) é a forma nativa e poderosa da Azure de gerir a sua [infraestrutura como código.](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

Este artigo mostra como permite ativar os Registos de [Fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programáticamente usando um modelo de Gestor de Recursos Azure e Azure PowerShell. Começamos por fornecer uma visão geral das propriedades do objeto DE Log De Fluxo NSG, seguido de alguns modelos de amostra. Em seguida, nós o modelo de implantação usando uma instância local PowerShell.


## <a name="nsg-flow-logs-object"></a>Objeto de logs de fluxo NSG

O objeto NSG Flow Logs com todos com parâmetros é mostrado abaixo.
Para obter uma visão geral completa das propriedades, poderá ler a referência do [modelo nsg Flow Logs](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

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
Para criar um recurso Microsoft.Network/NetworkWatchers/flowLogs, adicione o JSON acima à secção de recursos do seu modelo.


## <a name="creating-your-template"></a>Criando o seu modelo

Se estiver a utilizar modelos do Gestor de Recursos Azure pela primeira vez, pode aprender mais sobre eles usando os links abaixo.

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Tutorial: Crie e implante o seu primeiro modelo de Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Abaixo estão dois exemplos de modelos completos para configurar registos de fluxo NSG.

**Exemplo 1:** A versão mais simples do acima com parâmetros mínimos passados. O modelo abaixo permite registos de fluxo NSG num NSG alvo e armazena-os numa determinada conta de armazenamento.

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
> * O nome do recurso tem o formato "Recursos Parent>/Recurso infantil". Aqui, o recurso-mãe é a instância regional do Observador da Rede (Formato: NetworkWatcher_<RegionName>. Exemplo: NetworkWatcher_centraluseuap)
> * targetResourceId é o ID de recurso do NSG alvo
> * armazenamentoId é o iD de recursos da conta de armazenamento de destino

**Exemplo 2**: Os seguintes modelos que permitem os registos de fluxo NSG (versão 2) com uma retenção durante 5 dias. Habilitar o Traffic Analytics com um intervalo de processamento de 10 minutos.

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
            "enabled": true,
        "workspaceResourceId": "91a3d1e9-698e-4a49-96dc-f6fc585ae888",
        "trafficAnalyticsInterval": 10
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 1
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Implementando o seu modelo de Gestor de Recursos Azure

Este tutorial assume que você tem um grupo de Recursos existente e um NSG que você pode ativar o flow loging on.
Pode guardar qualquer um dos modelos de exemplo acima localmente como `azuredeploy.json`. Atualize os valores da propriedade de modo a que apontem recursos válidos na sua subscrição.

Para implementar o modelo, execute o seguinte comando no PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Verificar a sua implantação

Há algumas maneiras de verificar se a sua implantação foi bem sucedida. A sua consola PowerShell deve mostrar "ProvisioningState" como "Succeeded". Além disso, pode visitar a página do [portal NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar as suas alterações. Se houver problemas com a implementação, veja os erros comuns de implantação do [Azure com o Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)


## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os seus dados de Fluxo NSG utilizando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Análise de Tráfego Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
