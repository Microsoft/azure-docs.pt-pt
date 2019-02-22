---
title: Azure Service Bus entidades de mensagens de reencaminhamento automático | Documentos da Microsoft
description: Como encadear uma subscrição para outra fila ou tópico ou fila do Service Bus.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 85ab5e3bb963ee692e5b70af3eb90cc68cec361f
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593391"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Encadeamento de entidades do Service Bus com autoforwarding

O Service Bus *autoforwarding* funcionalidade permite-lhe uma fila ou subscrição para outra fila ou tópico que faz parte do mesmo espaço de nomes da cadeia. Quando autoforwarding está ativada, o Service Bus é automaticamente remove as mensagens que são colocadas na primeira fila ou subscrição (origem) e coloca-as no segundo fila ou tópico (destino). Ainda é possível enviar uma mensagem para a entidade de destino diretamente.

## <a name="using-autoforwarding"></a>Usando autoforwarding

Pode ativar autoforwarding definindo a [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] ou [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] as propriedades do [QueueDescription] [ QueueDescription] ou [SubscriptionDescription] [ SubscriptionDescription] objetos para a origem, como mostra a exemplo a seguir:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino tem de existir no momento que a entidade de origem é criada. Se a entidade de destino não existir, o barramento de serviço retorna uma exceção quando solicitado a criar a entidade de origem.

Pode usar autoforwarding para aumentar horizontalmente um tópico individual. Limites do Service Bus a [número de subscrições num determinado tópico](service-bus-quotas.md) para 2.000. Capaz de acomodar subscrições adicionais através da criação de tópicos de segundo nível. Mesmo que não estão vinculados pela limitação do Service Bus no número de subscrições, adicionar um segundo nível de tópicos pode melhorar o débito global do seu tópico.

![Cenário de reencaminhamento automático][0]

Também pode utilizar autoforwarding desassociar remetentes de mensagens dos destinatários. Por exemplo, considere um sistema ERP que consiste em três módulos: ordem de processamento, gerenciamento de estoque e gerenciamento de relações do cliente. Cada um desses módulos gera mensagens que são colocados em fila para um tópico correspondente. Alice e Bob é representantes de vendas que estejam interessados em todas as mensagens relacionadas aos seus clientes. Para receber essas mensagens, de Alice e Bob criar uma fila de pessoal e uma subscrição em cada um dos tópicos ERP que reencaminhar automaticamente todas as mensagens da sua fila.

![Cenário de reencaminhamento automático][1]

Se a Alice está em férias, seu pessoal fila, em vez do tópico ERP, é preenchida. Neste cenário, uma vez que um representante de vendas não recebeu quaisquer mensagens, nenhum dos tópicos de ERP nunca atingir quota.

## <a name="autoforwarding-considerations"></a>Considerações de Autoforwarding

Se a entidade de destino acumula demasiadas mensagens e excede a quota, ou a entidade de destino está desativada, a entidade de origem adiciona as mensagens para seus [fila de mensagens não entregues](service-bus-dead-letter-queues.md) até que haja espaço no destino (ou a entidade seja ativado novamente). Essas mensagens continuam a live na fila de mensagens não entregues, para que deve receber explicitamente e processá-las a partir da fila de mensagens não entregues.

Quando encadeando individuais tópicos para obter um tópico composto com muitas subscrições, recomenda-se que tem um número moderado de subscrições sobre o tópico de primeiro nível e o número de subscrições de tópicos de segundo nível. Por exemplo, um tópico de primeiro nível com 20 subscrições, cada um deles encadeado a um tópico de segundo nível com 200 subscrições, permite um débito mais elevado do que um tópico de primeiro nível com 200 subscrições, cada encadeado a um tópico de segundo nível com 20 subscrições.

Do Service Bus cobra uma operação para cada mensagem reencaminhada. Por exemplo, enviar uma mensagem para um tópico com 20 subscrições, cada um deles configurado para mensagens de autoforward para outra fila ou tópico, é faturada como 21 operações se todas as subscrições de primeiro nível recebem uma cópia da mensagem.

Para criar uma subscrição que é encadeada a outra fila ou tópico, o criador da subscrição tem de ter **gerir** permissões sobre a origem e a entidade de destino. Enviar mensagens para o tópico de origem requer apenas **enviar** permissões sobre o tópico de origem.

## <a name="next-steps"></a>Passos Seguintes

Para obter informações detalhadas sobre autoforwarding, consulte os seguintes tópicos de referência:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Para saber mais sobre as melhorias de desempenho do Service Bus, consulte 

* [Melhores práticas para melhoramentos do desempenho através de mensagens do Service Bus](service-bus-performance-improvements.md)
* [Entidades de mensagens particionadas][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
