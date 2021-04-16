---
title: Gerir registos de fluxo NSG - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Esta página explica como gerir os registos do Network Security Group Flow no Azure Network Watcher com PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 29340852cabcc77b7488f734a4677697b4a9b972
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535231"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Configurar registos de fluxo do grupo de segurança da rede com powerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do Grupo de Segurança de Rede são uma funcionalidade do Network Watcher que lhe permite visualizar informações sobre o tráfego IP de entrada e saída através de um Grupo de Segurança de Rede. Estes registos de fluxo são escritos em formato json e mostram fluxos de saída e entrada por regra, o NIC o fluxo aplica-se a, 5-tuple informações sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

A especificação detalhada de todos os comandos de registos de fluxo NSG para várias versões do AzPowerShell pode ser encontrada [aqui](https://docs.microsoft.com/powershell/module/az.network/#network-watcher)

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que a exploração de fluxo funcione com sucesso, o fornecedor **Microsoft.Insights** tem de estar registado. Se não tiver a certeza se o fornecedor **Microsoft.Insights** está registado, execute o seguinte script.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Ativar registos de fluxo de fluxo do grupo de segurança de rede e análise de tráfego

O comando para ativar os registos de fluxo é mostrado no seguinte exemplo:

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

A conta de armazenamento que especifica não pode ter regras de rede configuradas para a rede que restringem o acesso à rede apenas a serviços da Microsoft ou a redes virtuais específicas. A conta de armazenamento pode estar na mesma, ou numa subscrição Azure diferente, do que a NSG para a que ativar o registo de fluxo. Se utilizar subscrições diferentes, ambas devem estar associadas ao mesmo inquilino do Azure Ative Directory. A conta que utiliza para cada subscrição deve ter as [permissões necessárias.](required-rbac-permissions.md)

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Desativar os registos de fluxo de tráfego e de segurança de rede

Utilize o seguinte exemplo para desativar a análise do tráfego e os registos de fluxo:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Faça o download de um registo flow

A localização de armazenamento de um registo de fluxo é definida na criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:  https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os ficheiros de registo de fluxo são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Para obter informações sobre a estrutura do registo visite [a Visão geral do registo do fluxo de fluxo de rede](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [visualizar os seus registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [visualizar os seus registos de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
