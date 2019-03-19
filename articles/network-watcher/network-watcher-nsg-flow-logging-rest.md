---
title: Os registos de fluxo de gerir o grupo de segurança de rede com o observador de rede do Azure - REST API | Documentos da Microsoft
description: Esta página explica como gerir os registos de fluxo do grupo de segurança de rede no observador de rede do Azure com a REST API
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2ab25379-0fd3-4bfe-9d82-425dfc7ad6bb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: eadff8e2db6e5b8ea19b041614e0d1f3908da8a2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876889"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Registos de fluxo de configurar o grupo de segurança de rede com a REST API

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do grupo de segurança de rede são uma funcionalidade do observador de rede permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede. Estes registos de fluxo são escritos no formato json e Mostrar fluxos de saída e entrados numa base por regra, o NIC que o fluxo de mensagens em fila aplica-se a informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

## <a name="before-you-begin"></a>Antes de começar

ARMclient é utilizada para chamar a API de REST com o PowerShell. ARMClient encontra-se no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

> [!Important]
> Para chamadas de API de REST do observador de rede, que o nome do grupo de recursos no URI do pedido é o grupo de recursos que contém o observador de rede, não os recursos está a efetuar as ações de diagnóstico no.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo mostra como ativar, desativar e consultar os registos de fluxo com a API REST. Para saber mais sobre loggings de fluxo do grupo de segurança de rede, visite [registo de fluxo do grupo de segurança de rede - descrição geral](network-watcher-nsg-flow-logging-overview.md).

Neste cenário, irá:

* Ativar registos de fluxo (versão 2)
* Desativar os registos de fluxo
* Estado de registos de fluxo de consulta

## <a name="log-in-with-armclient"></a>Inicie sessão com ARMClient

Inicie sessão no armclient com as suas credenciais do Azure.

```PowerShell
armclient login
```

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o fluxo de registo para trabalhar com êxito, o **Microsoft. insights** fornecedor tem de estar registado. Se não tiver a certeza se o **Microsoft. insights** fornecedor está registado, execute o seguinte script.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Registos de fluxo de ativar o grupo de segurança de rede

O comando para ativar a versão de registos de fluxo 2 é mostrado no exemplo a seguir. Para a versão 1 substitua o campo 'versão' '1':

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

A resposta devolvida pelo exemplo anterior é o seguinte:

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

## <a name="disable-network-security-group-flow-logs"></a>Registos de fluxo de desativar o grupo de segurança de rede

Utilize o exemplo seguinte para desativar os registos de fluxo. A chamada é o mesmo que ativar registos de fluxo, exceto **false** está definida para a propriedade enabled.

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

A resposta devolvida pelo exemplo anterior é o seguinte:

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

As seguintes consultas de chamada REST o estado do fluxo de registos num grupo de segurança de rede.

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

## <a name="download-a-flow-log"></a>Transferir o registo de um fluxo

A localização de armazenamento de um registo de fluxo é definida durante a criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados para uma conta de armazenamento é o Explorador de armazenamento do Microsoft Azure, que pode ser baixado aqui:  https://storageexplorer.com/

Se for especificada uma conta de armazenamento, os arquivos de captura de pacotes são guardados para uma conta de armazenamento na seguinte localização:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como [Visualize os seus registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualize os seus registos de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
