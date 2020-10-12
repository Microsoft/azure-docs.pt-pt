---
title: Subscrições de Grelha de Eventos de Consulta Azure
description: Este artigo descreve como listar subscrições da Grade de Eventos na sua subscrição Azure. Fornece diferentes parâmetros com base no tipo de subscrição.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3d700f543bc5e3c7add2a346c10acf975e1c2462
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86120454"
---
# <a name="query-event-grid-subscriptions"></a>Subscrições de Grelha de Eventos de Consulta 

Este artigo descreve como listar as subscrições da Grade de Eventos na sua subscrição Azure. Ao consultar as subscrições existentes da Grade de Eventos, é importante compreender os diferentes tipos de subscrições. Fornece diferentes parâmetros com base no tipo de subscrição que pretende obter.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Grupos de recursos e assinaturas Azure

As assinaturas azure e os grupos de recursos não são recursos Azure. Portanto, as subscrições de grelha de eventos para grupos de recursos ou subscrições Azure não têm as mesmas propriedades que as subscrições da grelha de eventos para recursos Azure. As subscrições da grelha de eventos para grupos de recursos ou subscrições Azure são consideradas globais.

Para obter subscrições de grelha de eventos para uma subscrição do Azure e seus grupos de recursos, você não precisa fornecer quaisquer parâmetros. Certifique-se de que selecionou a subscrição Azure que pretende consultar. Os exemplos a seguir não obtêm subscrições de grelha de eventos para tópicos personalizados ou recursos Azure.

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

Para obter subscrições de grelha de eventos para uma subscrição do Azure, forneça o tipo de tópico de **Microsoft.Resources.Subscrições**.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Para obter subscrições de grelha de eventos para todos os grupos de recursos dentro de uma subscrição do Azure, forneça o tipo de tópico de **Microsoft.Resources.ResourceGroups**.

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

Os tópicos personalizados da grelha de eventos são recursos Azure. Por isso, consulta subscrições de grelha de eventos para tópicos personalizados e outros recursos, como a conta de armazenamento Blob, da mesma forma. Para obter subscrições de grelha de eventos para tópicos personalizados, deve fornecer parâmetros que identifiquem o recurso ou identifiquem a localização do recurso. Não é possível consultar amplamente subscrições de grelha de eventos para recursos em toda a sua subscrição do Azure.

Para obter subscrições de grelha de eventos para tópicos personalizados e outros recursos em um local, forneça o nome da localização.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Para obter subscrições de tópicos personalizados para uma localização, forneça a localização e o tipo de tópico de **Microsoft.EventGrid.Topics**.

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Para o PowerShell, utilize:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Para obter subscrições para contas de armazenamento para uma localização, forneça a localização e o tipo de tópico de **Microsoft.Storage.Storage.StorageAccounts**.

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

Para obter subscrições de grelha de eventos para um determinado recurso, forneça o ID do recurso.

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

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre entrega de eventos e retrórias, [entrega e redação de mensagens de Event Grid.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
