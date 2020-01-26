---
title: Entidades de mensagens Deôndetada de Serviço Azure
description: Este artigo descreve como acorrentar uma fila de ônibus de serviço Azure ou subscrição de outra fila ou tópico.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8b8883b579233962de61e7247e6ac1cbcb2a6d80
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761054"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Entidades de ônibus de serviço de cadeia com auto-reencaminhamento

A função de *auto-reencaminhamento* de ônibus de serviço permite-lhe acorrentar uma fila ou subscrição a outra fila ou tópico que faz parte do mesmo espaço de nome. Quando o reencaminhamento automático está ativado, o Service Bus remove automaticamente as mensagens que são colocadas na primeira fila ou subscrição (fonte) e coloca-as na segunda fila ou tópico (destino). Ainda é possível enviar uma mensagem diretamente à entidade de destino.

## <a name="using-autoforwarding"></a>Utilização de auto-encaminhamento

Pode ativar o reencaminhamento automático definindo a Descrição da [Fila.ForwardTo][QueueDescription.ForwardTo] ou [SubscriçãoDescription.ForwardTo][SubscriptionDescription.ForwardTo] nas propriedades da [Descrição][QueueDescription] da Fila ou [da SubscriçãoDos][SubscriptionDescription] objetos para a fonte, como no exemplo seguinte:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino deve existir no momento em que a entidade fonte é criada. Se a entidade de destino não existir, a Service Bus devolve uma exceção quando solicitada a criar a entidade fonte.

Pode usar o auto-encaminhamento para escalar um tópico individual. O Ônibus de serviço limita o [número de subscrições num determinado tópico](service-bus-quotas.md) para 2.000. Você pode acomodar subscrições adicionais criando tópicos de segundo nível. Mesmo que não esteja vinculado à limitação do Ônibus de serviço no número de subscrições, adicionar um segundo nível de tópicos pode melhorar a entrada geral do seu tópico.

![Cenário de reencaminhamento automático][0]

Também pode utilizar reencaminhamento automático para separar os remetentes de mensagens dos recetores. Por exemplo, considere um sistema ERP que consiste em três módulos: processamento de encomendas, gestão de inventário e gestão de relações com clientes. Cada um destes módulos gera mensagens que são enquecidas num tópico correspondente. Alice e Bob são representantes de vendas interessados em todas as mensagens relacionadas com os seus clientes. Para receber essas mensagens, Alice e Bob criam cada uma uma fila pessoal e uma subscrição em cada um dos tópicos erp que automaticamente reencaminhom todas as mensagens para a sua fila.

![Cenário de reencaminhamento automático][1]

Se alice for de férias, sua fila pessoal, em vez do tema erp, preenche. Neste cenário, uma vez que um representante comercial não recebeu nenhuma mensagem, nenhum dos tópicos do ERP chegou a atingir a quota.

> [!NOTE]
> Quando o reencaminhamento automático é configurado, o valor para AutoDeleteOnIdle tanto na **Fonte como no Destino** é automaticamente definido para o valor máximo do tipo de dados.
> 
>   - Do lado da Fonte, o reencaminhamento automático atua como uma operação de receção. Assim, a fonte que tem configuração de auto-encaminhamento nunca é realmente "ociosa".
>   - Do lado do destino, isto é feito para garantir que há sempre um destino para encaminhar a mensagem para.

## <a name="autoforwarding-considerations"></a>Considerações de reencaminhamento automático

Se a entidade de destino acumular demasiadas mensagens e exceder a quota, ou se a entidade de destino estiver desativada, a entidade fonte adiciona as mensagens à sua [fila de cartas mortas](service-bus-dead-letter-queues.md) até que haja espaço no destino (ou a entidade seja reativada). Essas mensagens continuam a viver na fila das cartas mortas, por isso deve recebê-las e processá-las explicitamente da fila das cartas mortas.

Ao acorrentar tópicos individuais para obter um tópico composto com muitas subscrições, recomenda-se que tenha um número moderado de subscrições sobre o tema do primeiro nível e muitas subscrições sobre os tópicos do segundo nível. Por exemplo, um tópico de primeiro nível com 20 subscrições, cada uma delas acorrentada a um tema de segundo nível com 200 subscrições, permite uma maior entrada do que um tópico de primeiro nível com 200 subscrições, cada uma acorrentada a um tema de segundo nível com 20 subscrições.

Serviço Bus conta uma operação para cada mensagem reencaminhada. Por exemplo, o envio de uma mensagem para um tópico com 20 subscrições, cada uma delas configurada para enviar mensagens automáticas para outra fila ou tópico, é faturada como 21 operações se todas as subscrições de primeiro nível receberem uma cópia da mensagem.

Para criar uma subscrição que esteja acorrentada a outra fila ou tópico, o criador da subscrição deve ter permissões **de Gestão** tanto na fonte como na entidade de destino. Enviar mensagens para o tópico de origem apenas requer **Enviar** permissões sobre o tópico de origem.

## <a name="next-steps"></a>Passos seguintes

Para obter informações detalhadas sobre o reencaminhamento automático, consulte os seguintes tópicos de referência:

* [Para a frente][QueueDescription.ForwardTo]
* [Descrição da fila][QueueDescription]
* [Descrição da subscrição][SubscriptionDescription]

Para saber mais sobre melhorias de desempenho do Ônibus de serviço, consulte 

* [Melhores práticas para uma melhoria do desempenho com as Mensagens do Service Bus](service-bus-performance-improvements.md)
* [Entidades de mensagens divididas.][Partitioned messaging entities]

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
