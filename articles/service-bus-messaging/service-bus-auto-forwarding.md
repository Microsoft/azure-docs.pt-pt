---
title: Encaminhamento automático de entidades de mensagens do barramento de serviço do Azure | Microsoft Docs
description: Como encadear uma fila ou assinatura do barramento de serviço a outra fila ou tópico.
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
ms.openlocfilehash: 1d7b76a58a427b687d0dc36d13cfc00f32196853
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390137"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Encadeando entidades do barramento de serviço com encaminhamento automático

O recurso de *encaminhamento* automático do barramento de serviço permite encadear uma fila ou assinatura a outra fila ou tópico que faz parte do mesmo namespace. Quando o encaminhamento automático está habilitado, o barramento de serviço remove automaticamente as mensagens que são colocadas na primeira fila ou assinatura (origem) e as coloca na segunda fila ou tópico (destino). Ainda é possível enviar uma mensagem diretamente para a entidade de destino.

## <a name="using-autoforwarding"></a>Usando o encaminhamento automático

Você pode habilitar o encaminhamento automático definindo as propriedades [QueueDescription. ForwardTo][QueueDescription.ForwardTo] ou [SubscriptionDescription. ForwardTo][SubscriptionDescription.ForwardTo] nos objetos [QueueDescription][QueueDescription] ou [SubscriptionDescription][SubscriptionDescription] para a origem, como no exemplo a seguir:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino deve existir no momento em que a entidade de origem é criada. Se a entidade de destino não existir, o barramento de serviço retornará uma exceção quando solicitado a criar a entidade de origem.

Você pode usar o encaminhamento automático para escalar horizontalmente um tópico individual. O barramento de serviço limita o [número de assinaturas em um determinado tópico](service-bus-quotas.md) para 2.000. Você pode acomodar assinaturas adicionais criando tópicos de segundo nível. Mesmo que você não esteja associado à limitação do barramento de serviço no número de assinaturas, a adição de um segundo nível de tópicos pode melhorar a produtividade geral do seu tópico.

![Cenário de encaminhamento automático][0]

Você também pode usar o encaminhamento automático para separar remetentes de mensagens dos destinatários. Por exemplo, considere um sistema de ERP que consiste em três módulos: processamento de pedidos, gerenciamento de estoque e gerenciamento de relações com o cliente. Cada um desses módulos gera mensagens que são enfileiradas em um tópico correspondente. Alice e Bob são representantes de vendas interessados em todas as mensagens relacionadas a seus clientes. Para receber essas mensagens, Alice e Bob criam uma fila pessoal e uma assinatura em cada um dos tópicos de ERP que encaminham automaticamente todas as mensagens para sua fila.

![Cenário de encaminhamento automático][1]

Se Alice entrar em férias, sua fila pessoal, em vez do tópico ERP, será preenchida. Nesse cenário, como um representante de vendas não recebeu nenhuma mensagem, nenhum dos tópicos de ERP atinge a cota.

> [!NOTE]
> Quando o encaminhamento automático é configurado, o valor de AutoDeleteOnIdle na **origem e no destino** é definido automaticamente como o valor máximo do tipo de dados.
> 
>   - No lado da origem, o encaminhamento automático atua como uma operação de recebimento. Portanto, a origem que tem a configuração de encaminhamento automático nunca é realmente "ociosa".
>   - No lado do destino, isso é feito para garantir que sempre haja um destino para encaminhar a mensagem.

## <a name="autoforwarding-considerations"></a>Considerações de encaminhamento automático

Se a entidade de destino acumular muitas mensagens e exceder a cota, ou se a entidade de destino estiver desabilitada, a entidade de origem adicionará as mensagens à sua fila de mensagens [mortas](service-bus-dead-letter-queues.md) até que haja espaço no destino (ou a entidade seja reabilitada). Essas mensagens continuam a residir na fila de mensagens mortas, portanto, você deve receber e processá-las explicitamente da fila de mensagens mortas.

Ao encadear tópicos individuais para obter um tópico composto com muitas assinaturas, é recomendável que você tenha um número moderado de assinaturas no tópico de primeiro nível e em várias assinaturas nos tópicos de segundo nível do. Por exemplo, um tópico de primeiro nível com 20 assinaturas, cada uma delas encadeada a um tópico de segundo nível com assinaturas 200, permite maior taxa de transferência que um tópico de primeiro nível com assinaturas 200, cada uma encadeada a um tópico de segundo nível com 20 assinaturas.

O barramento de serviço fatura uma operação para cada mensagem encaminhada. Por exemplo, enviar uma mensagem para um tópico com 20 assinaturas, cada uma delas configurada para encaminhar mensagens para outra fila ou tópico, será cobrada como 21 operações se todas as assinaturas de primeiro nível receberem uma cópia da mensagem.

Para criar uma assinatura encadeada a outra fila ou tópico, o criador da assinatura deve ter permissões de **Gerenciamento** na entidade de origem e de destino. O envio de mensagens para o tópico de origem só requer permissões de **envio** no tópico de origem.

## <a name="next-steps"></a>Passos Seguintes

Para obter informações detalhadas sobre o encaminhamento automático, consulte os seguintes tópicos de referência:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Para saber mais sobre as melhorias de desempenho do barramento de serviço, consulte 

* [Melhores práticas para uma melhoria do desempenho com as Mensagens do Service Bus](service-bus-performance-improvements.md)
* [Entidades de mensagens particionadas][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
