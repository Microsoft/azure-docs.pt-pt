---
title: Gerir registos de fluxo do grupo de segurança da rede com o observador de rede do Azure - PowerShell | Documentos da Microsoft
description: Esta página explica como gerir registos de fluxo do grupo de segurança da rede de mensagens em fila no observador de rede do Azure com o PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ebeebfa4490797493a781bf462d363d1cbcf2d55
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857985"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Configurar registos de fluxo do grupo de segurança da rede com o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do grupo de segurança de rede são uma funcionalidade do observador de rede permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede. Estes registos de fluxo são escritos no formato json e Mostrar fluxos de saída e entrados numa base por regra, o NIC que o fluxo de mensagens em fila aplica-se a informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o fluxo de registo para trabalhar com êxito, o **Microsoft. insights** fornecedor tem de estar registado. Se não tiver a certeza se o **Microsoft. insights** fornecedor está registado, execute o seguinte script.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Registos de fluxo de ativar o grupo de segurança de rede e de análise de tráfego

O comando para ativar os registos de fluxo é mostrado no exemplo a seguir:

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
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

A conta de armazenamento especificada não pode ter regras de rede configuradas para o mesmo que restringem o acesso de rede apenas aos serviços da Microsoft ou as redes virtuais específicas. A conta de armazenamento pode ser o mesmo, ou uma subscrição do Azure diferente, que o NSG que ativar o registo de fluxo para. Se usar a subscrições diferentes, têm ambos de ser associados ao mesmo inquilino do Azure Active Directory. A conta que utiliza para cada subscrição tem de ter o [as permissões necessárias](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Desativar os registos de análise de tráfego e o fluxo do grupo de segurança da rede

Utilize o exemplo seguinte para desativar a análise de tráfego e registos de fluxo:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Transferir o registo de um fluxo

A localização de armazenamento de um registo de fluxo é definida durante a criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados para uma conta de armazenamento é o Explorador de armazenamento do Microsoft Azure, que pode ser baixado aqui:  https://storageexplorer.com/

Se for especificada uma conta de armazenamento, ficheiros de registo de fluxo são guardados para uma conta de armazenamento na seguinte localização:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Para obter informações sobre a estrutura do registo de visite [descrição geral do fluxo do grupo de segurança da rede registo](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Próximos Passos

Saiba como [Visualize os seus registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualize os seus registos de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
