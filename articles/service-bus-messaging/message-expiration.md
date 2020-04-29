---
title: Ônibus de serviço Azure - expiração da mensagem
description: Este artigo explica sobre a expiração e tempo para viver das mensagens azure Service Bus. Após tal prazo, a mensagem já não é entregue.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e86c92fa1cfb13929d5617502224f479709efdd3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76756339"
---
# <a name="message-expiration-time-to-live"></a>Expiração de mensagem (TTL)

A carga útil numa mensagem, ou um comando ou inquérito que uma mensagem transmite a um recetor, está quase sempre sujeita a algum tipo de prazo de validade ao nível da aplicação. Após esse prazo, o conteúdo já não é entregue ou a operação solicitada já não é executada.

Para ambientes de desenvolvimento e teste em que as filas e tópicos são frequentemente usados no contexto de execuções parciais de aplicações ou peças de aplicação, também é desejável que mensagens de teste encalhadas sejam recolhidas automaticamente para que o próximo teste possa começar limpo.

A expiração de qualquer mensagem individual pode ser controlada definindo a propriedade do sistema [TimeToLive,](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) que especifica uma duração relativa. A expiração torna-se um instante absoluto quando a mensagem é incorporada na entidade. Nessa altura, a propriedade [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) assume o valor [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). A definição de tempo para viver (TTL) numa mensagem intermediada não é aplicada quando não há clientes a ouvir ativamente.

Depois do instante **expiradoAtUtc,** as mensagens tornam-se inelegíveis para recuperação. A expiração não afeta as mensagens que estão atualmente bloqueadas para entrega; essas mensagens ainda são tratadas normalmente. Se o bloqueio expirar ou a mensagem for abandonada, a expiração entra em vigor imediatamente.

Enquanto a mensagem está bloqueada, o pedido pode estar na posse de uma mensagem que expirou. Se a aplicação está disposta a avançar com o processamento ou se opta por abandonar a mensagem depende do implementador.

## <a name="entity-level-expiration"></a>Expiração ao nível da entidade

Todas as mensagens enviadas para uma fila ou tópico estão sujeitas a uma expiração predefinida que é definida ao nível da entidade com a propriedade [padrãoMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) e que também pode ser definida no portal durante a criação e ajustada posteriormente. A expiração por defeito é utilizada para todas as mensagens enviadas à entidade onde o [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) não está explicitamente definido. A expiração por defeito também funciona como um teto para o valor **TimeToLive.** As mensagens que têm uma validade **timeToLive** mais longa do que o valor predefinido são silenciosamente ajustadas ao valor **padrãoMessageTimeToLive** antes de serem enqueuadas.

> [!NOTE]
> O valor padrão [timeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) para uma mensagem intermediada é [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) se não especificado de outra forma.
>
> Para as entidades de mensagens (filas e tópicos), o tempo de validade padrão é também [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) para os níveis padrão e premium do Service Bus.  Para o nível básico, o tempo de validade padrão é de 14 dias.

As mensagens expiradas podem ser movidas opcionalmente para uma [fila de cartas mortas,](service-bus-dead-letter-queues.md) definindo a propriedade [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) ou verificando a respetiva caixa no portal. Se a opção for deixada desativada, as mensagens expiradas são retiradas. As mensagens expiradas transferidas para a fila das letras mortas podem ser distinguidas de outras mensagens com letras mortas, avaliando a propriedade [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que o corretor armazena na secção de propriedades do utilizador; o valor é [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) neste caso.

No caso acima referido em que a mensagem é protegida contra a expiração enquanto está trancada e se a bandeira estiver colocada na entidade, a mensagem é transferida para a fila da letra morta à medida que o cadeado é abandonado ou expira. No entanto, não se move se a mensagem for resolvida com êxito, o que pressupõe então que a aplicação a tenha tratado com sucesso, apesar da caducidade nominal.

A combinação de [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) e letras mortas automáticas (e transacionais) no termo são uma ferramenta valiosa para estabelecer confiança em saber se um trabalho dado a um manipulador ou a um grupo de manipuladores ao abrigo de um prazo é recuperado para processamento à medida que o prazo é alcançado.

Por exemplo, considere um site que precisa executar de forma fiável empregos em um backend limitado à escala, e que ocasionalmente experimenta picos de tráfego ou quer ser isolado contra episódios de disponibilidade desse backend. No caso regular, o manipulador do lado do servidor para os dados do utilizador submetido suprime a informação para uma fila e, posteriormente, recebe uma resposta confirmando o manuseamento bem sucedido da transação numa fila de resposta. Se houver um pico de tráfego e o manipulador de backend não conseguir processar os seus itens de atraso a tempo, os empregos caducados são devolvidos na fila das letras mortas. O utilizador interativo pode ser notificado de que a operação solicitada demorará um pouco mais do que o habitual, podendo então o pedido ser colocado numa fila diferente para um caminho de processamento em que o resultado de processamento eventual seja enviado ao utilizador por e-mail. 


## <a name="temporary-entities"></a>Entidades temporárias

As filas de ônibus de serviço, tópicos e subscrições podem ser criadas como entidades temporárias, que são automaticamente removidas quando não foram utilizadas por um determinado período de tempo.
 
A limpeza automática é útil em cenários de desenvolvimento e teste em que as entidades são criadas dinamicamente e não são limpas após a utilização, devido a alguma interrupção do teste ou depuração. Também é útil quando uma aplicação cria entidades dinâmicas, como uma fila de respostas, para receber respostas de volta a um processo de servidor web, ou em outro objeto de vida relativamente curta onde é difícil limpar de forma fiável essas entidades quando a instância do objeto desaparece.

A funcionalidade está ativada utilizando a propriedade [autoDeleteOnIdle.](/azure/templates/microsoft.servicebus/namespaces/queues) Esta propriedade está definida para a duração pela qual uma entidade deve estar inativa (não utilizada) antes de ser automaticamente eliminada. O valor mínimo para este imóvel é 5.
 
A propriedade **autoDeleteOnIdle** deve ser definida através de uma operação do Gestor de Recursos Azure ou através do .NET-Framework client [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs. Não pode colocar no portal.

## <a name="idleness"></a>Ociosidade

Eis o que considerou a ociosidade das entidades (filas, tópicos e subscrições):

- Filas
    - Não envia  
    - Sem receber  
    - Sem atualizações para a fila  
    - Sem mensagens agendadas  
    - Sem navegar/espreitar 
- Tópicos  
    - Não envia  
    - Sem atualizações para o tópico  
    - Sem mensagens agendadas 
- Subscrições
    - Sem receber  
    - Sem atualizações para a subscrição  
    - Nenhuma nova regra adicionada à subscrição  
    - Sem navegar/espreitar  
 


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
