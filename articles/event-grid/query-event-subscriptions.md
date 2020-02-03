---
title: Assinaturas de Grelha de Eventos Do Azure
description: Este artigo descreve como listar as subscrições da Event Grid na sua subscrição do Azure. Fornece diferentes parâmetros com base no tipo de subscrição.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2da4ba5946f16092c1d0918aec8dc3109b5fd4a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721547"
---
# <a name="query-event-grid-subscriptions"></a>Assinaturas de Grelha de Eventos de Consulta 

Este artigo descreve como listar as subscrições da Rede de Eventos na sua subscrição Azure. Ao consultar as subscrições existentes da Rede de Eventos, é importante compreender os diferentes tipos de subscrições. Fornece diferentes parâmetros com base no tipo de subscrição que pretende obter.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Grupos de recursos e assinaturas Azure

As assinaturas azure e os grupos de recursos não são recursos Azure. Por isso, as subscrições de redes de eventos de grupos de recursos ou subscrições do Azure não têm as mesmas propriedades que as subscrições da rede de eventos aos recursos do Azure. As subscrições de grelhas de eventos a grupos de recursos ou subscrições do Azure são consideradas globais.

Para obter subscrições de grelha de eventos para uma subscrição Do Azure e seus grupos de recursos, você não precisa fornecer quaisquer parâmetros. Certifique-se de que selecionou a subscrição Azure que pretende consultar. Os seguintes exemplos não recebem subscrições de grelha de eventos para tópicos personalizados ou recursos Azure.

Para a CLI do Azure, utilize:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Para obter subscrições de grelha de eventos para uma subscrição Do Azure, forneça o tipo tópico de **Microsoft.Resources.Subscrições**.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Para obter subscrições de grelha de eventos para todos os grupos de recursos dentro de uma subscrição Azure, forneça o tipo tópico de **Microsoft.Resources.ResourcesGroups**.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Para obter subscrições de grelha de eventos para um grupo de recursos especificado, forneça o nome do grupo de recursos como parâmetro.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Tópicos personalizados e recursos Azure

Os tópicos personalizados da grelha de eventos são recursos Azure. Portanto, você consulta assinaturas de grelha de eventos para tópicos personalizados e outros recursos, como conta de armazenamento Blob, da mesma forma. Para obter subscrições de grelha de eventos para tópicos personalizados, você deve fornecer parâmetros que identifiquem o recurso ou identifiquem a localização do recurso. Não é possível consultar amplamente as subscrições da grelha de eventos para recursos em toda a sua subscrição Azure.

Para obter subscrições de grelha de eventos para tópicos personalizados e outros recursos em um local, forneça o nome da localização.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Para obter subscrições de tópicos personalizados para uma localização, forneça a localização e o tipo tópico de **Microsoft.EventGrid.Topics**.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Para obter subscrições de contas de armazenamento para uma localização, forneça a localização e o tipo tópico de **Microsoft.Storage.StorageAccounts**.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Para obter subscrições de grelha de eventos para um tópico personalizado, forneça o nome do tópico personalizado e o nome do seu grupo de recursos.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Para obter subscrições de grelha de eventos para um determinado recurso, forneça o ID de recursos.

Para a CLI do Azure, utilize:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Para o PowerShell, utilize:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre entrega e repetições de eventos, entrega e retentativa de mensagens da Grelha de [Eventos.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
