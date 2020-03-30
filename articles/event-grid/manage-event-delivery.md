---
title: Políticas de carta morta e retry - Azure Event Grid
description: Descreve como personalizar as opções de entrega de eventos para a Grelha de Eventos. Estabeleça um destino de letra morta e especifique quanto tempo para voltar a tentar a entrega.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794042"
---
# <a name="dead-letter-and-retry-policies"></a>Carta morta e políticas de retry

Ao criar uma subscrição de evento, pode personalizar as definições para entrega de eventos. Este artigo mostra-lhe como configurar um local de letra morta e personalizar as definições de retry. Para obter informações sobre estas funcionalidades, consulte a entrega e nova tentativa de [mensagem da Rede de Eventos.](delivery-and-retry.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Definir localização de letra morta

Para definir um local de carta morta, você precisa de uma conta de armazenamento para realizar eventos que não podem ser entregues em um ponto final. Os exemplos obtêm a identificação de recursos de uma conta de armazenamento existente. Criam uma subscrição de evento que utiliza um contentor nessa conta de armazenamento para o ponto final da letra morta.

> [!NOTE]
> - Crie uma conta de armazenamento e um recipiente de bolha no armazenamento antes de executar comandos neste artigo.
> - O serviço Da Grelha de Eventos cria bolhas neste recipiente. Os nomes das bolhas terão o nome da subscrição da Grelha de Eventos com todas as letras em maiúsculas. Por exemplo, se o nome da subscrição for My-Blob-Subscription, os nomes das bolhas de letra mortas terão MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/1111111111-1111-1111-1111-1111-11111.json). Este comportamento é proteger contra diferenças no tratamento de casos entre os serviços azure.


### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Para desligar as letras mortas, refaça o comando para criar a subscrição do evento, mas não forneça um valor para `deadletter-endpoint`. Não precisa de apagar a subscrição do evento.

> [!NOTE]
> Se estiver a utilizar o Azure CLI na sua máquina local, utilize a versão Azure CLI 2.0.56 ou superior. Para obter instruções sobre a instalação da versão mais recente do Azure CLI, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Para desligar as letras mortas, refaça o comando para criar a subscrição do evento, mas não forneça um valor para `DeadLetterEndpoint`. Não precisa de apagar a subscrição do evento.

> [!NOTE]
> Se estiver a utilizar o Azure Poweshell na sua máquina local, utilize a versão 1.1.0 da Azure PowerShell. Descarregue e instale o mais recente Azure PowerShell a partir de [downloads do Azure.](https://azure.microsoft.com/downloads/)

## <a name="set-retry-policy"></a>Definir a política de retry

Ao criar uma subscrição da Grelha de Eventos, pode definir valores para quanto tempo a Grelha de Eventos deve tentar entregar o evento. Por defeito, a Grelha de Eventos tenta durante 24 horas (1440 minutos) ou 30 vezes. Pode definir qualquer um destes valores para a subscrição da sua grelha de eventos. O valor para o tempo de evento-para-viver deve ser um inteiro de 1 a 1440. O valor para as tentativas máximas deve ser um inteiro de 1 a 30.

Não pode configurar o horário de [retry.](delivery-and-retry.md#retry-schedule-and-duration)

### <a name="azure-cli"></a>CLI do Azure

Para definir o tempo de vida do evento para um valor diferente de 1440 minutos, utilize:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Para definir as tentativas máximas para um valor diferente de 30, utilize:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Se definir `event-ttl` ambos `max-deliver-attempts`e , A Grelha de Eventos utiliza o primeiro a expirar para determinar quando parar a entrega do evento.

### <a name="powershell"></a>PowerShell

Para definir o tempo de vida do evento para um valor diferente de 1440 minutos, utilize:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Para definir as tentativas máximas para um valor diferente de 30, utilize:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Se definir `EventTtl` ambos `MaxDeliveryAttempt`e , A Grelha de Eventos utiliza o primeiro a expirar para determinar quando parar a entrega do evento.

## <a name="next-steps"></a>Passos seguintes

* Para uma aplicação de amostra que utilize uma aplicação Azure Function para processar eventos de cartas mortas, consulte [o Azure Event Grid Dead Letter Samples para .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Para obter informações sobre entrega e repetições de eventos, entrega e retentativa de mensagens da Grelha de [Eventos.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
