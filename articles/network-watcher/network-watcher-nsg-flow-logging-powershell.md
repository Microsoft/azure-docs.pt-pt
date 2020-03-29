---
title: Gerir os registos de fluxo NSG - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Esta página explica como gerir os registos de Fluxo do Grupo de Segurança da Rede no Observador da Rede Azure com a PowerShell
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840949"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Configurar registos de fluxo de grupo de segurança de rede com PowerShell

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do Grupo de Segurança da Rede são uma característica do Observador de Rede que lhe permite visualizar informações sobre o tráfego IP de entrada e fuga através de um Grupo de Segurança de Rede. Estes registos de fluxo são escritos em formato JSOn e mostram fluxos de saída e de entrada por regra, o NIC a que o fluxo se aplica a, 5-tuple informações sobre o fluxo (Fonte/Destino IP, Fonte/Porto de Destino, Protocolo), e se o tráfego foi permitido ou negado.

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o registo de fluxos funcione com sucesso, o fornecedor **Microsoft.Insights** deve ser registado. Se não tiver a certeza se o fornecedor **Microsoft.Insights** está registado, execute o seguinte script.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Ativar registos de fluxo de grupo de segurança de rede e análise de tráfego

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

A conta de armazenamento que especifica não pode ter regras de rede configuradas para a sua forma ção que restringem o acesso à rede apenas a serviços da Microsoft ou a redes virtuais específicas. A conta de armazenamento pode estar na mesma, ou numa subscrição Azure diferente, do que o NSG para o que permite o registo de fluxo. Se utilizar assinaturas diferentes, ambas devem estar associadas ao mesmo inquilino do Azure Ative Directory. A conta que utiliza para cada subscrição deve ter as [permissões necessárias](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Desativar registos de fluxo de tráfego e de segurança de rede

Utilize o seguinte exemplo para desativar a análise de tráfego e os registos de fluxo:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Descarregue um registo de Fluxo

A localização de armazenamento de um registo de fluxo é definida na criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:https://storageexplorer.com/

Se for especificada uma conta de armazenamento, os ficheiros de registo de fluxo são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Para obter informações sobre a estrutura do log visit [Network Security Group Flow Visão geral](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [visualizar os seus registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [visualizar os seus registos de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
