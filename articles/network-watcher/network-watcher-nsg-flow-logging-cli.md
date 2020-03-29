---
title: Gerir os registos de fluxo NSG - Azure CLI
titleSuffix: Azure Network Watcher
description: Esta página explica como gerir os registos de Fluxo do Grupo de Segurança da Rede no Observador da Rede Azure com o Azure CLI
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
ms.openlocfilehash: 285d19dbd0e7b8a94eada66f837d33b787006f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840966"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Configurar registos de fluxo de grupo de segurança de rede com O CLI Azure

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do Grupo de Segurança da Rede são uma característica do Observador de Rede que lhe permite visualizar informações sobre o tráfego IP de entrada e fuga através de um Grupo de Segurança de Rede. Estes registos de fluxo são escritos em formato JSOn e mostram fluxos de saída e de entrada por regra, o NIC a que o fluxo se aplica a, 5-tuple informações sobre o fluxo (Fonte/Destino IP, Fonte/Porto de Destino, Protocolo), e se o tráfego foi permitido ou negado.

Para executar os passos deste artigo, é necessário [instalar a interface de linha de comando Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o registo de fluxos funcione com sucesso, o fornecedor **Microsoft.Insights** deve ser registado. Se não tiver a certeza se o fornecedor **Microsoft.Insights** está registado, execute o seguinte script.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Ativar os registos de fluxo do grupo de segurança da rede

O comando para ativar os registos de fluxo é mostrado no seguinte exemplo:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

A conta de armazenamento que especifica não pode ter regras de rede configuradas para a sua configuração que restringem o acesso à rede apenas a serviços da Microsoft ou redes virtuais específicas. A conta de armazenamento pode estar na mesma, ou numa subscrição Azure diferente, do que o NSG para o que permite o registo de fluxo. Se utilizar assinaturas diferentes, ambas devem estar associadas ao mesmo inquilino do Azure Ative Directory. A conta que utiliza para cada subscrição deve ter as [permissões necessárias](required-rbac-permissions.md). 

Se a conta de armazenamento estiver num grupo de recursos diferente, ou subscrição, do que o grupo de segurança da rede, especifique a identificação completa da conta de armazenamento, em vez do seu nome. Por exemplo, se a conta de armazenamento estiver num grupo de recursos chamado *RG-Storage,* em vez de especificar *o storageAccountName* no comando anterior, especificará */subscrições/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Desativar os registos de fluxo do grupo de segurança da rede

Utilize o seguinte exemplo para desativar os registos de fluxo:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Descarregue um registo de Fluxo

A localização de armazenamento de um registo de fluxo é definida na criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:https://storageexplorer.com/

Se for especificada uma conta de armazenamento, os ficheiros de registo de fluxo são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Passos Seguintes

Saiba como [visualizar os seus registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [visualizar os seus registos de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
