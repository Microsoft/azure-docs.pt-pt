---
title: Entidades de mensagens de autocarro da Azure Service de reencaminhamento automático
description: Este artigo descreve como acorrentar uma fila de autocarros da Azure Service ou subscrição de outra fila ou tópico.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 28c3e8985f12163e871fa4de5fb6cc92d68110b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337742"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Entidades de autocarros de serviço de chaining com autoforwarding

A funcionalidade *de auto-ordenação* do Service Bus permite-lhe acorrentar uma fila ou subscrição a outra fila ou tópico que faça parte do mesmo espaço de nome. Quando o auto-ordenamento está ativado, o Service Bus remove automaticamente as mensagens que são colocadas na primeira fila ou subscrição (fonte) e coloca-as na segunda fila ou tópico (destino). Ainda é possível enviar uma mensagem diretamente à entidade de destino.

## <a name="using-autoforwarding"></a>Utilização de auto-via

Pode ativar a via automática definindo as propriedades [QueueDescription.ForwardTo][QueueDescription.ForwardTo] ou [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] nos objetos [dedescrição][QueueDescription] ou [subscrição][SubscriptionDescription] da fila para a fonte, como no exemplo seguinte:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino deve existir no momento da criação da entidade de origem. Se a entidade de destino não existir, a Service Bus devolve uma exceção quando solicitado para criar a entidade de origem.

Pode utilizar o auto-ordenamento para escalar um tópico individual. O Service Bus limita o [número de subscrições num determinado tópico](service-bus-quotas.md) para 2.000. Pode acomodar subscrições adicionais criando tópicos de segundo nível. Mesmo que não esteja vinculado à limitação do Service Bus no número de subscrições, adicionar um segundo nível de tópicos pode melhorar o rendimento geral do seu tópico.

![Cenário de encaminhamento automático][0]

Também pode utilizar o auto-ordenamento para separar os remetentes de mensagens dos recetores. Por exemplo, considere um sistema ERP que consiste em três módulos: processamento de encomendas, gestão de inventário e gestão de relações com clientes. Cada um destes módulos gera mensagens que são encadeadas num tópico correspondente. Alice e Bob são representantes de vendas que estão interessados em todas as mensagens que se relacionam com os seus clientes. Para receber essas mensagens, Alice e Bob criam cada uma uma fila pessoal e uma subscrição em cada um dos tópicos ERP que reencaminham automaticamente todas as mensagens para a sua fila.

![Cenário de encaminhamento automático][1]

Se alice for de férias, sua fila pessoal, em vez do tópico ERP, preenche. Neste cenário, uma vez que um representante de vendas não recebeu nenhuma mensagem, nenhum dos tópicos do ERP nunca chegou a atingir a quota.

> [!NOTE]
> Quando a auto-via é configurada, o valor para AutoDeleteOnIdle tanto na **Fonte como no Destino** é automaticamente definido para o valor máximo do tipo de dados.
> 
>   - Do lado da Fonte, o auto-acompanhamento atua como uma operação de receção. Assim, a fonte que tem a configuração de auto-via nunca é realmente "ocioso".
>   - Do lado do destino, isto é feito para garantir que há sempre um destino para encaminhar a mensagem para.

## <a name="autoforwarding-considerations"></a>Considerações de auto-via

Se a entidade de destino acumular demasiadas mensagens e exceder a quota, ou se a entidade de destino for desativada, a entidade de origem adiciona as mensagens à sua [fila de cartas mortas](service-bus-dead-letter-queues.md) até que haja espaço no destino (ou a entidade seja reativada). Essas mensagens continuam a viver na fila das letras mortas, por isso deves recebê-las explicitamente e processá-las da fila das letras mortas.

Ao acorrentar tópicos individuais para obter um tópico composto com muitas subscrições, recomenda-se que tenha um número moderado de subscrições sobre o tópico de primeiro nível e muitas subscrições sobre os tópicos de segundo nível. Por exemplo, um tópico de primeiro nível com 20 subscrições, cada uma delas acorrentada a um tópico de segundo nível com 200 subscrições, permite uma produção superior à de um tópico de primeiro nível com 200 subscrições, cada uma acorrentada a um tópico de segundo nível com 20 subscrições.

Service Bus fatura uma operação para cada mensagem reencaminhada. Por exemplo, o envio de uma mensagem para um tópico com 20 subscrições, cada uma delas configurada para mensagens de via automática para outra fila ou tópico, é faturado como 21 operações se todas as subscrições de primeiro nível receberem uma cópia da mensagem.

Para criar uma subscrição acorrentada a outra fila ou tópico, o criador da subscrição deve ter permissões de **Gestão** tanto na fonte como na entidade de destino. Enviar mensagens para o tópico de origem só requer **Enviar** permissões sobre o tópico de origem.

## <a name="next-steps"></a>Próximos passos

Para obter informações detalhadas sobre a via automática, consulte os seguintes tópicos de referência:

* [ForwardTo][QueueDescription.ForwardTo]
* [Roteiro de Fila][QueueDescription]
* [AssinaturaDescrição][SubscriptionDescription]

Para saber mais sobre as melhorias no desempenho do Service Bus, consulte 

* [Melhores práticas para uma melhoria do desempenho com as Mensagens do Service Bus](service-bus-performance-improvements.md)
* [Entidades de mensagens divididas.][Partitioned messaging entities]

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
