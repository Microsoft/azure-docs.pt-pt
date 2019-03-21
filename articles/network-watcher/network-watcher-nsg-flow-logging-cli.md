---
title: Gerir registos de fluxo do grupo de segurança da rede com o observador de rede do Azure - CLI do Azure | Documentos da Microsoft
description: Esta página explica como gerir registos de fluxo do grupo de segurança da rede de mensagens em fila no observador de rede do Azure com a CLI do Azure
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
ms.openlocfilehash: 43e18326aa9afe41ddfd1183bf689972c1877b0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904989"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Configurar registos de fluxo do grupo de segurança da rede com o CLI do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os registos de fluxo do grupo de segurança de rede são uma funcionalidade do observador de rede permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede. Estes registos de fluxo são escritos no formato json e Mostrar fluxos de saída e entrados numa base por regra, o NIC que o fluxo de mensagens em fila aplica-se a informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

Para efetuar os passos neste artigo, precisa [instale a interface de linha de comandos do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o fluxo de registo para trabalhar com êxito, o **Microsoft. insights** fornecedor tem de estar registado. Se não tiver a certeza se o **Microsoft. insights** fornecedor está registado, execute o seguinte script.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Registos de fluxo de ativar o grupo de segurança de rede

O comando para ativar os registos de fluxo é mostrado no exemplo a seguir:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

A conta de armazenamento que especificou não pode ter regras de rede configuradas para o mesmo que restringem o acesso de rede apenas aos serviços da Microsoft ou as redes virtuais específicas. A conta de armazenamento pode ser o mesmo, ou uma subscrição do Azure diferente, que o NSG que ativar o registo de fluxo para. Se usar a subscrições diferentes, têm ambos de ser associados ao mesmo inquilino do Azure Active Directory. A conta que utiliza para cada subscrição tem de ter o [as permissões necessárias](required-rbac-permissions.md). 

Se a conta de armazenamento é num grupo de recursos diferente ou subscrição, que o grupo de segurança de rede, especifica o ID completo da conta de armazenamento, em vez de seu nome. Por exemplo, se a conta de armazenamento está no grupo de recursos chamado *RG-Storage*, em vez de especificar *storageAccountName* no comando anterior, deve especificar   */subscrições / { SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Registos de fluxo de desativar o grupo de segurança de rede

Utilize o exemplo seguinte para desativar os registos de fluxo:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
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
