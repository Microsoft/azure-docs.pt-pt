---
title: Gerenciar logs de fluxo NSG – API REST do Azure
titleSuffix: Azure Network Watcher
description: Esta página explica como gerenciar logs de fluxo do grupo de segurança de rede no observador de rede do Azure com a API REST
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840932"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Configurando logs de fluxo do grupo de segurança de rede usando a API REST

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os logs de fluxo do grupo de segurança de rede são um recurso do observador de rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede. Esses logs de fluxo são gravados no formato JSON e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, informações de 5 tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

## <a name="before-you-begin"></a>Antes de começar

ARMclient é usado para chamar a API REST usando o PowerShell. ARMClient é encontrado no Chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

> [!Important]
> Para a API REST do observador de rede chama o nome do grupo de recursos no URI de solicitação é o grupo de recursos que contém o observador de rede, não os recursos em que você está executando as ações de diagnóstico.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo mostra como habilitar, desabilitar e consultar logs de fluxo usando a API REST. Para saber mais sobre os logs de fluxo do grupo de segurança de rede, visite [log de fluxo do grupo de segurança de rede-visão geral](network-watcher-nsg-flow-logging-overview.md).

Nesse cenário, você irá:

* Habilitar logs de fluxo (versão 2)
* Desabilitar logs de fluxo
* Status dos logs de fluxo de consulta

## <a name="log-in-with-armclient"></a>Fazer logon com ARMClient

Faça logon no armclient com suas credenciais do Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o log de fluxo funcione com êxito, o provedor **Microsoft. insights** deve ser registrado. Se você não tiver certeza se o provedor **Microsoft. insights** está registrado, execute o script a seguir.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Habilitar logs de fluxo do grupo de segurança de rede

O comando para habilitar os logs de fluxo versão 2 é mostrado no exemplo a seguir. Para a versão 1, substitua o campo ' version ' por ' 1 ':

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

A resposta retornada do exemplo anterior é a seguinte:

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

## <a name="disable-network-security-group-flow-logs"></a>Desabilitar logs de fluxo do grupo de segurança de rede

Use o exemplo a seguir para desabilitar os logs de fluxo. A chamada é a mesma que Habilitar logs de fluxo, exceto que **false** é definido para a propriedade Enabled.

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

A resposta retornada do exemplo anterior é a seguinte:

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

## <a name="query-flow-logs"></a>Logs de fluxo de consulta

A chamada REST a seguir consulta o status dos logs de fluxo em um grupo de segurança de rede.

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

Veja a seguir um exemplo da resposta retornada:

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

## <a name="download-a-flow-log"></a>Baixar um log de fluxo

O local de armazenamento de um log de fluxo é definido na criação. Uma ferramenta conveniente para acessar esses logs de fluxo salvos em uma conta de armazenamento é Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixada aqui: https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [Visualizar seus logs de fluxo do NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualizar seus logs de fluxo do NSG com ferramentas de](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) software livre
