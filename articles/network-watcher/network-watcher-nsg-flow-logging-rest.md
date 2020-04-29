---
title: Gerir os registos de fluxo nsg - Azure REST API
titleSuffix: Azure Network Watcher
description: Esta página explica como gerir os registos de fluxo do Grupo de Segurança da Rede no Observador da Rede Azure com a Rest API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7cc47414dc985f6fc2fff3c57d809f307b142e30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76840932"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Configurar os registos de fluxo do Grupo de Segurança da Rede utilizando a API REST

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do Grupo de Segurança da Rede são uma característica do Observador de Rede que lhe permite visualizar informações sobre o tráfego IP de entrada e fuga através de um Grupo de Segurança de Rede. Estes registos de fluxo são escritos em formato JSOn e mostram fluxos de saída e de entrada por regra, o NIC a que o fluxo se aplica a, 5-tuple informações sobre o fluxo (Fonte/Destino IP, Fonte/Porto de Destino, Protocolo), e se o tráfego foi permitido ou negado.

## <a name="before-you-begin"></a>Antes de começar

O ARMclient é usado para chamar a API REST usando powerShell. ARMClient é encontrado em chocolate na [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já seguiu os passos na [Create a Network Watcher](network-watcher-create.md) para criar um Observador de Rede.

> [!Important]
> Para o Network Watcher REST A API chama o nome do grupo de recursos no pedido URI é o grupo de recursos que contém o Observador de Rede, e não os recursos em que está a executar as ações de diagnóstico.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo mostra-lhe como ativar, desativar e consultar registos de fluxo utilizando a API REST. Para saber mais sobre os registos de fluxo do Grupo de Segurança da Rede, visite o fluxo de registo do Grupo de [Segurança da Rede - Visão geral](network-watcher-nsg-flow-logging-overview.md).

Neste cenário, irá:

* Ativar registos de fluxo (versão 2)
* Desativar os registos de fluxo
* Estado dos registos de fluxo de consulta

## <a name="log-in-with-armclient"></a>Iniciar sessão com a ARMClient

Faça login no armclient com as suas credenciais Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o registo de fluxos funcione com sucesso, o fornecedor **Microsoft.Insights** deve ser registado. Se não tiver a certeza se o fornecedor **Microsoft.Insights** está registado, execute o seguinte script.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Ativar os registos de fluxo do Grupo de Segurança da Rede

O comando para ativar os registos de fluxo versão 2 é mostrado no seguinte exemplo. Para a versão 1 substitua o campo 'versão' por '1':

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

A resposta devolvida do exemplo anterior é a seguinte:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
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
```

## <a name="disable-network-security-group-flow-logs"></a>Desativar os registos de fluxo do Grupo de Segurança da Rede

Utilize o seguinte exemplo para desativar os registos de fluxo. A chamada é a mesma que ativar os registos de fluxo, exceto **que é falso** para a propriedade ativada.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

A resposta devolvida do exemplo anterior é a seguinte:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
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
```

## <a name="query-flow-logs"></a>Registos de fluxo de consulta

A chamada REST seguinte consulta o estado dos registos de fluxo num Grupo de Segurança da Rede.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Segue-se um exemplo da resposta devolvida:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
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
```

## <a name="download-a-flow-log"></a>Descarregue um registo de fluxo

A localização de armazenamento de um registo de fluxo é definida na criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:https://storageexplorer.com/

Se for especificada uma conta de armazenamento, os ficheiros de captura de pacotes são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [visualizar os seus registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [visualizar os seus registos de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
