---
title: Mensagens mortas e novas políticas para assinaturas da grade de eventos do Azure
description: Descreve como personalizar opções de entrega de eventos para a grade de eventos. Defina um destino de letra mortas e especifique o tempo de repetição da entrega.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 63bae62ed89bd0bbc167a88274002d1fa1e9b86d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933361"
---
# <a name="dead-letter-and-retry-policies"></a>Mensagens mortas e novas políticas

Ao criar uma assinatura de evento, você pode personalizar as configurações de entrega de eventos. Este artigo mostra como configurar um local de mensagens mortas e personalizar as configurações de repetição. Para obter informações sobre esses recursos, consulte [entrega de mensagem de grade de eventos e tentar novamente](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Definir local de mensagens mortas

Para definir um local de mensagens mortas, você precisa de uma conta de armazenamento para manter eventos que não podem ser entregues a um ponto de extremidade. Os exemplos obtêm a ID de recurso de uma conta de armazenamento existente. Eles criam uma assinatura de evento que usa um contêiner nessa conta de armazenamento para o ponto de extremidade de mensagens mortas.

> [!NOTE]
> - Crie uma conta de armazenamento e um contêiner de blob no armazenamento antes de executar os comandos neste artigo.
> - O serviço de grade de eventos cria BLOBs neste contêiner. Os nomes dos BLOBs terão o nome da assinatura da grade de eventos com todas as letras em maiúsculas. Por exemplo, se o nome da assinatura for meu-blob-Subscription, os nomes dos BLOBs de mensagens mortas terão meu-BLOB-SUBSCRIPTION (myblobcontainer/meu-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111 -111111111111. JSON). Esse comportamento é para proteger contra diferenças na manipulação de casos entre os serviços do Azure.


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

Para desativar mensagens mortas, execute novamente o comando para criar a assinatura de evento, mas não forneça um valor para `deadletter-endpoint`. Você não precisa excluir a assinatura de evento.

> [!NOTE]
> Se você estiver usando CLI do Azure em seu computador local, use CLI do Azure versão 2.0.56 ou superior. Para obter instruções sobre como instalar a versão mais recente do CLI do Azure, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

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

Para desativar mensagens mortas, execute novamente o comando para criar a assinatura de evento, mas não forneça um valor para `DeadLetterEndpoint`. Você não precisa excluir a assinatura de evento.

> [!NOTE]
> Se você estiver usando o Azure PoweShell em seu computador local, use Azure PowerShell versão 1.1.0 ou superior. Baixe e instale a Azure PowerShell mais recente dos [downloads do Azure](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Definir política de repetição

Ao criar uma assinatura de grade de eventos, você pode definir valores para o tempo durante o qual a grade de eventos deve tentar entregar o evento. Por padrão, a grade de eventos tenta por 24 horas (1440 minutos) ou 30 vezes. Você pode definir qualquer um desses valores para sua assinatura de grade de eventos. O valor da vida útil do evento deve ser um inteiro de 1 a 1440. O valor de tentativas máximas deve ser um inteiro de 1 a 30.

Não é possível configurar o [agendamento de repetição](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>CLI do Azure

Para definir a vida útil do evento com um valor diferente de 1440 minutos, use:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Para definir o número máximo de tentativas para um valor diferente de 30, use:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Se você definir `event-ttl` e `max-deliver-attempts`, a grade de eventos usará a primeira para expirar para determinar quando parar a entrega de eventos.

### <a name="powershell"></a>PowerShell

Para definir a vida útil do evento com um valor diferente de 1440 minutos, use:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Para definir o número máximo de tentativas para um valor diferente de 30, use:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Se você definir `EventTtl` e `MaxDeliveryAttempt`, a grade de eventos usará a primeira para expirar para determinar quando parar a entrega de eventos.

## <a name="next-steps"></a>Passos Seguintes

* Para um aplicativo de exemplo que usa um aplicativo de funções do Azure para processar eventos de mensagens mortas, consulte [amostras de mensagens mortas da grade de eventos do Azure para .net](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
