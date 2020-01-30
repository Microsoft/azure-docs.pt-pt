---
title: Gerenciar logs de fluxo NSG-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Esta página explica como gerenciar logs de fluxo do grupo de segurança de rede no observador de rede do Azure com o PowerShell
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
ms.openlocfilehash: 9612afdb63c6988c0027f003caeacd456b5e50e2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840949"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Configurando logs de fluxo do grupo de segurança de rede com o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os logs de fluxo do grupo de segurança de rede são um recurso do observador de rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede. Esses logs de fluxo são gravados no formato JSON e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, informações de 5 tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o log de fluxo funcione com êxito, o provedor **Microsoft. insights** deve ser registrado. Se você não tiver certeza se o provedor **Microsoft. insights** está registrado, execute o script a seguir.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Habilitar logs e Análise de Tráfego de fluxo do grupo de segurança de rede

O comando para habilitar logs de fluxo é mostrado no exemplo a seguir:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

A conta de armazenamento especificada não pode ter regras de rede configuradas para ela que restrinjam o acesso à rede apenas a serviços da Microsoft ou a redes virtuais específicas. A conta de armazenamento pode estar na mesma ou em uma assinatura do Azure diferente da NSG para a qual você habilita o log de fluxo. Se você usar assinaturas diferentes, elas deverão estar associadas ao mesmo locatário Azure Active Directory. A conta usada para cada assinatura deve ter as [permissões necessárias](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Desabilitar Análise de Tráfego e logs de fluxo do grupo de segurança de rede

Use o exemplo a seguir para desabilitar a análise de tráfego e os logs de fluxo:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Baixar um log de fluxo

O local de armazenamento de um log de fluxo é definido na criação. Uma ferramenta conveniente para acessar esses logs de fluxo salvos em uma conta de armazenamento é Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixada aqui: https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de log de fluxo serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Para obter informações sobre a estrutura do log, visite [visão geral do log de fluxo do grupo de segurança de rede](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Próximos Passos

Saiba como [Visualizar seus logs de fluxo do NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualizar seus logs de fluxo do NSG com ferramentas de](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) software livre
