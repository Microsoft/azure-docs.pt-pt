---
title: Gerir registos de fluxo NSG - Azure CLI
titleSuffix: Azure Network Watcher
description: Esta página explica como gerir os registos do Network Security Group Flow no Azure Network Watcher com o Azure CLI
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
ms.openlocfilehash: 46d12db413fdf01995bc84ae018065e877afb15e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98017821"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Configurar registos de fluxo do grupo de segurança da rede com O Azure CLI

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do Grupo de Segurança de Rede são uma funcionalidade do Network Watcher que lhe permite visualizar informações sobre o tráfego IP de entrada e saída através de um Grupo de Segurança de Rede. Estes registos de fluxo são escritos em formato json e mostram fluxos de saída e entrada por regra, o NIC o fluxo aplica-se a, 5-tuple informações sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

Para executar os passos neste artigo, é necessário [instalar a interface de linha de comando Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que a exploração de fluxo funcione com sucesso, o fornecedor **Microsoft.Insights** tem de estar registado. Se não tiver a certeza se o fornecedor **Microsoft.Insights** está registado, execute o seguinte script.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Ativar registos de fluxo de grupo de segurança de rede

O comando para ativar os registos de fluxo é mostrado no seguinte exemplo:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

A conta de armazenamento que especifica não pode ter regras de rede configuradas para a rede que restringem o acesso à rede apenas a serviços da Microsoft ou a redes virtuais específicas. A conta de armazenamento pode estar na mesma, ou numa subscrição Azure diferente, do que a NSG para a que ativar o registo de fluxo. Se utilizar subscrições diferentes, ambas devem estar associadas ao mesmo inquilino do Azure Ative Directory. A conta que utiliza para cada subscrição deve ter as [permissões necessárias.](required-rbac-permissions.md) 

Se a conta de armazenamento estiver num grupo de recursos diferente, ou subscrição, do que o grupo de segurança da rede, especifique o ID completo da conta de armazenamento, em vez do seu nome. Por exemplo, se a conta de armazenamento estiver num grupo de recursos chamado *RG-Storage*, em vez de especificar *o armazenamentoAconseme* no comando anterior, especificaria */subscrições/{SubscriçãoID}/grupos de recursos/RG-Storage/providers/Microsoft.Storage/storageAcounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Desativar registos de fluxo de grupo de segurança de rede

Utilize o seguinte exemplo para desativar os registos de fluxo:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Faça o download de um registo flow

A localização de armazenamento de um registo de fluxo é definida na criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:  https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os ficheiros de registo de fluxo são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Passos Seguintes

Saiba como [visualizar os seus registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [visualizar os seus registos de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
