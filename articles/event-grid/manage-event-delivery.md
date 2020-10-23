---
title: Carta morta e políticas de repetição - Azure Event Grid
description: Descreve como personalizar opções de entrega de eventos para a Grade de Eventos. Desabrar um destino de letra morta e especificar quanto tempo para voltar a tentar a entrega.
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 7d8cd74ccfb77bcec45d06071a4f46fb2a640cf8
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460942"
---
# <a name="set-dead-letter-location-and-retry-policy"></a>Definir a localização e a política de repetição das mensagens não entregues

Ao criar uma subscrição de eventos, pode personalizar as definições para entrega de eventos. Este artigo mostra-lhe como configurar um local de letra morta e personalizar as definições de repetição. Para obter informações sobre estas funcionalidades, consulte [a entrega de mensagens de Grade de Eventos e redaça.](delivery-and-retry.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Para saber mais sobre a entrega de mensagens, retrórias e letras mortas, consulte o artigo conceptual: [Event Grid message delivery and retry](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Definir localização de letra morta

Para definir um local de carta morta, você precisa de uma conta de armazenamento para realizar eventos que não podem ser entregues a um ponto final. Os exemplos obtêm o ID de recurso de uma conta de armazenamento existente. Eles criam uma subscrição de evento que usa um recipiente na conta de armazenamento para o ponto final de letra morta.

> [!NOTE]
> - Crie uma conta de armazenamento e um recipiente de bolhas no armazenamento antes de executar os comandos neste artigo.
> - O serviço 'Grade de Eventos' cria bolhas neste recipiente. Os nomes das bolhas terão o nome da subscrição da Grade de Eventos com todas as letras em maiúsculas. Por exemplo, se o nome da subscrição for My-Blob-Subscrição, os nomes das bolhas de letra morta terão MY-BLOB-SUBSCRIÇÃO (myblobcontainer/MY-BLOB-SUBSCRIÇÃO/2019/8/8/5/111111111-1111-1111-1111-111111111111.js). Este comportamento é para proteger contra diferenças no caso de manipulação entre os serviços Azure.


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

Para desligar a inscrição sem saída, reexame o comando para criar a subscrição do evento, mas não forneça um valor para `deadletter-endpoint` . Não precisa de apagar a subscrição do evento.

> [!NOTE]
> Se estiver a utilizar o Azure CLI na sua máquina local, utilize a versão 2.0.56 do Azure CLI. Para obter instruções sobre a instalação da versão mais recente do Azure [CLI, consulte instalar o Azure CLI](/cli/azure/install-azure-cli).

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

Para desligar a inscrição sem saída, reexame o comando para criar a subscrição do evento, mas não forneça um valor para `DeadLetterEndpoint` . Não precisa de apagar a subscrição do evento.

> [!NOTE]
> Se estiver a utilizar o Azure Poweshell na sua máquina local, utilize a versão 1.1.0 ou superior da Azure PowerShell. Descarregue e instale os mais recentes downloads Azure PowerShell da [Azure.](https://azure.microsoft.com/downloads/)

## <a name="set-retry-policy"></a>Definir política de relíndi

Ao criar uma subscrição de Grade de Eventos, pode definir valores por quanto tempo a Grade de Eventos deve tentar entregar o evento. Por predefinição, a Grade de Eventos tenta durante 24 horas (1440 minutos) ou 30 vezes. Pode definir qualquer um destes valores para a subscrição da grelha de eventos. O valor para o evento time-to-live deve ser um inteiro de 1 a 1440. O valor para as retrações máximas deve ser um inteiro de 1 a 30.

Não pode configurar o [horário de retíria.](delivery-and-retry.md#retry-schedule-and-duration)

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

Para definir as retrações máximas a um valor diferente de 30, utilize:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

> [!NOTE]
> Se definir ambos `event-ttl` `max-deliver-attempts` e, o Event Grid utiliza o primeiro para expirar para determinar quando parar a entrega do evento. Por exemplo, se definir 30 minutos como tentativas de entrega de tempo para viver (TTL) e 10 no máximo. Quando um evento não é entregue após 30 minutos (ou) não é entregue após 10 tentativas, o que acontecer primeiro, o evento é sem carta.  

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

Para definir as retrações máximas a um valor diferente de 30, utilize:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

> [!NOTE]
> Se definir ambos `event-ttl` `max-deliver-attempts` e, o Event Grid utiliza o primeiro para expirar para determinar quando parar a entrega do evento. Por exemplo, se definir 30 minutos como tentativas de entrega de tempo para viver (TTL) e 10 no máximo. Quando um evento não é entregue após 30 minutos (ou) não é entregue após 10 tentativas, o que acontecer primeiro, o evento é sem carta.  

## <a name="next-steps"></a>Passos seguintes

* Para uma aplicação de amostra que utiliza uma aplicação Azure Function para processar eventos de letras mortas, consulte [amostras de letra morta da grelha de eventos Azure para .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Para obter informações sobre entrega de eventos e retrórias, [entrega e redação de mensagens de Event Grid.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
