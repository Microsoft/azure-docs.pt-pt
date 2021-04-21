---
title: Azure Service Bus - contagem de mensagens
description: Recupere a contagem de mensagens realizadas em filas e subscrições utilizando o Azure Resource Manager e o Azure Service Bus NamespaceManager APIs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814617"
---
# <a name="get-message-counters"></a>Receba contadores de mensagens
Este artigo mostra-lhe diferentes formas de obter a seguinte mensagem conta para uma fila ou subscrição. Conhecer a contagem de mensagens ativas é útil para determinar se uma fila constrói um atraso que requer mais recursos para processar do que o que foi atualmente implementado. 

| Contador | Description |
| ----- | ---------- | 
| ActiveMessageCount | Número de mensagens na fila ou subscrição que estão no estado ativo e prontas para entrega. |
| AgendamentoMessageCount | Número de mensagens no estado programado. |
| DeadLetterMessageCount | Número de mensagens na fila da carta morta. |
| TransferMessageCount | Número de mensagens pendentes de transferência para outra fila ou tópico. |
| TransferDeadLetterMessageCount | Número de mensagens que não foram transferidas para outra fila ou tópico e foram transferidas para a fila de transferências de cartas mortas. |

Se uma aplicação quiser escalar os recursos com base no comprimento da fila, deve fazê-lo com um ritmo medido. A aquisição dos contadores de mensagens é uma operação dispendiosa dentro do corretor de mensagens, e executá-la frequentemente direta e negativamente impacta o desempenho da entidade.

> [!NOTE]
> As mensagens enviadas para um tópico de Service Bus são reencaminhadas para subscrições para esse tópico. Assim, a contagem de mensagens ativas sobre o tema em si é 0, uma vez que essas mensagens foram reencaminhadas com sucesso para a subscrição. Obtenha a contagem de mensagens na subscrição e verifique se é maior que 0. Apesar de ver mensagens na subscrição, elas são armazenadas num armazenamento propriedade do tópico. Se olharmos para as subscrições, então terão uma contagem de mensagens não-zero (que somam 323 MB de espaço para toda esta entidade).


## <a name="using-azure-portal"></a>Com o Portal do Azure
Navegue para o seu espaço de nome e selecione a fila. Vê contadores de mensagens na página **'Visão Geral'** para a fila.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Contadores de mensagens na página geral da fila":::

Navegue para o seu espaço de nome, selecione o tópico e, em seguida, selecione a subscrição para o tópico. Vê contadores de mensagens na página **'Visão Geral'** para a fila.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Contadores de mensagens na página geral da subscrição":::

## <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Utilize o [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) comando para obter os detalhes da contagem de mensagens para uma fila como mostrado no exemplo seguinte. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Aqui está uma amostra de saída:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Utilize o [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) comando para obter os detalhes da contagem de mensagens para uma subscrição, como mostrado no exemplo seguinte. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Com o PowerShell, pode obter os detalhes da contagem de mensagens para uma fila da seguinte forma:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Aqui está a saída da amostra:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

pode obter os detalhes da contagem de mensagens para uma subscrição da seguinte forma:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

O objeto devolvido `MessageCountDetails` tem as seguintes propriedades: `ActiveMessageCount` , , , , , `DeadLetterMessageCount` `ScheduledMessageCount` `TransferDeadLetterMessageCount` `TransferMessageCount` . 

## <a name="next-steps"></a>Passos seguintes

Experimente as amostras no idioma à sua escolha para explorar as funcionalidades do Azure Service Bus. 

- [Amostras da biblioteca de clientes do Azure Service Bus para Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Amostras da biblioteca do cliente do Azure Service Bus para Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Amostras de biblioteca de clientes do Azure Service Bus para JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Amostras de biblioteca de clientes do Azure Service Bus para TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Amostras de Azure.Messaging.ServiceBus para .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Encontre amostras para as bibliotecas clientes mais antigas .NET e Java abaixo:
- [Amostras microsoft.Azure.ServiceBus para .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [amostras de azure-servicebus para Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
