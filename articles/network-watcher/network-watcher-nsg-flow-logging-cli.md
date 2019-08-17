---
title: Gerenciar logs de fluxo do grupo de segurança de rede com o observador de rede do Azure-CLI do Azure | Microsoft Docs
description: Esta página explica como gerenciar logs de fluxo do grupo de segurança de rede no observador de rede do Azure com o CLI do Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 5e7c09c1a06a94a2ed64f3624ee38dc42606d7bc
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563489"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Configurando logs de fluxo do grupo de segurança de rede com o CLI do Azure

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Os logs de fluxo do grupo de segurança de rede são um recurso do observador de rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede. Esses logs de fluxo são gravados no formato JSON e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, informações de 5 tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

Para executar as etapas neste artigo, você precisa [instalar a interface de linha de comando do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

Para que o log de fluxo funcione com êxito, o provedor **Microsoft.** insights deve ser registrado. Se você não tiver certeza se o provedor **Microsoft.** insights está registrado, execute o script a seguir.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Habilitar logs de fluxo do grupo de segurança de rede

O comando para habilitar logs de fluxo é mostrado no exemplo a seguir:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

A conta de armazenamento especificada não pode ter regras de rede configuradas para ela que restrinjam o acesso à rede apenas a serviços da Microsoft ou a redes virtuais específicas. A conta de armazenamento pode estar na mesma ou em uma assinatura do Azure diferente da NSG para a qual você habilita o log de fluxo. Se você usar assinaturas diferentes, elas deverão estar associadas ao mesmo locatário Azure Active Directory. A conta usada para cada assinatura deve ter as [permissões necessárias](required-rbac-permissions.md). 

Se a conta de armazenamento estiver em um grupo de recursos ou assinatura diferente do grupo de segurança de rede, especifique a ID completa da conta de armazenamento, em vez de seu nome. Por exemplo, se a conta de armazenamento estiver em um grupo de recursos chamado *RG-Storage*, em vez de especificar *storageAccountName* no comando anterior, você deverá especificar */subscriptions/{SubscriptionId}/resourceGroups/RG-storage/providers/ Microsoft. Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Desabilitar logs de fluxo do grupo de segurança de rede

Use o exemplo a seguir para desabilitar os logs de fluxo:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Baixar um log de fluxo

O local de armazenamento de um log de fluxo é definido na criação. Uma ferramenta conveniente para acessar esses logs de fluxo salvos em uma conta de armazenamento é Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixada aqui: https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de log de fluxo serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

> [!IMPORTANT]
> Atualmente, há um problema em que [os logs de fluxo do NSG (grupo de segurança de rede)](network-watcher-nsg-flow-logging-overview.md) para o observador de rede não são automaticamente excluídos do armazenamento de BLOBs com base nas configurações da política de retenção. Se você tiver uma política de retenção diferente de zero, recomendamos que você exclua periodicamente os blobs de armazenamento que ultrapassaram seu período de retenção para evitar qualquer cobrança incorrida. Para obter mais informações sobre como excluir o blog de armazenamento de log de fluxo do NSG, consulte [excluir blobs de armazenamento de log de fluxo NSG](network-watcher-delete-nsg-flow-log-blobs.md).

## <a name="next-steps"></a>Próximos Passos

Saiba como [Visualizar seus logs de fluxo do NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualizar seus logs de fluxo do NSG com ferramentas de](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) software livre
