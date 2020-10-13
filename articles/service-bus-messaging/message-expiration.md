---
title: Azure Service Bus - expiração da mensagem
description: Este artigo explica sobre a expiração e tempo para viver das mensagens do Azure Service Bus. Após tal prazo, a mensagem já não é entregue.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 47f8bdb4440adfeb5197f90cdad5358a442ce6a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569909"
---
# <a name="message-expiration-time-to-live"></a>Expiração de mensagem (TTL)

A carga útil numa mensagem, ou um comando ou inquérito que uma mensagem transmite a um recetor, está quase sempre sujeita a algum tipo de prazo de validade de nível de aplicação. Após esse prazo, o conteúdo deixou de ser entregue ou a operação solicitada deixou de ser executada.

Para ambientes de desenvolvimento e teste em que as filas e tópicos são frequentemente utilizados no contexto de execuções parciais de aplicações ou peças de aplicação, também é desejável que as mensagens de teste encalhadas sejam automaticamente recolhidas para que o próximo teste possa começar a ser limpo.

A expiração de qualquer mensagem individual pode ser controlada definindo a propriedade do sistema [TimeToLive,](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) que especifica uma duração relativa. A expiração torna-se um instante absoluto quando a mensagem é encosa na entidade. Nessa altura, a propriedade [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) assume o valor [**(EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc)  +  [**TimeToLive)**](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). A definição de tempo-a-viver (TTL) numa mensagem intermediada não é aplicada quando não há clientes a ouvir ativamente.

Depois do instante **ExpirasAtUtc,** as mensagens tornam-se inelegíveis para a recuperação. A expiração não afeta as mensagens que estão atualmente bloqueadas para entrega; essas mensagens ainda são tratadas normalmente. Se o bloqueio expirar ou a mensagem for abandonada, a expiração produz efeitos imediatos.

Enquanto a mensagem está bloqueada, a aplicação pode estar na posse de uma mensagem que expirou. Se a aplicação está disposta a avançar com o processamento ou opta por abandonar a mensagem cabe ao implementador.

## <a name="entity-level-expiration"></a>Caducidade ao nível da entidade

Todas as mensagens enviadas para uma fila ou tópico estão sujeitas a uma expiração padrão que é definida ao nível da entidade com a propriedade [padrãoMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) e que também pode ser definida no portal durante a criação e ajustada mais tarde. A expiração padrão é utilizada para todas as mensagens enviadas à entidade onde o [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) não está explicitamente definido. A expiração predefinida também funciona como um teto para o valor **TimeToLive.** As mensagens que têm uma expiração **timeToLive** mais longa do que o valor padrão são ajustadas silenciosamente ao valor **padrãoMessageTimeToLive** antes de serem encadeadas.

> [!NOTE]
> O valor padrão [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) para uma mensagem intermediada é [TimeSpan.Max](/dotnet/api/system.timespan.maxvalue) se não for especificado de outra forma.
>
> Para as entidades de mensagens (filas e tópicos), o tempo de validade padrão também é [TimeSpan.Max](/dotnet/api/system.timespan.maxvalue) para padrão de Service Bus e níveis premium. Para o nível **básico,** o prazo de validade (também máximo) é **de 14 dias**.

As mensagens expiradas podem opcionalmente ser transferidas para uma [fila de letras mortas,](service-bus-dead-letter-queues.md) definindo a propriedade [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) ou verificando a respetiva caixa no portal. Se a opção for deixada desativada, as mensagens caducadas são retiradas. As mensagens caducadas transferidas para a fila de cartas mortas podem ser distinguidas de outras mensagens com letras mortas, avaliando a propriedade [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que o corretor armazena na secção de propriedades do utilizador; o valor é [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) neste caso.

No caso acima referido, em que a mensagem está protegida de expiração enquanto está trancada e se a bandeira estiver colocada na entidade, a mensagem é transferida para a fila da letra morta à medida que o cadeado é abandonado ou expira. No entanto, não se move se a mensagem for resolvida com sucesso, o que pressupõe então que a aplicação a tratou com sucesso, apesar da expiração nominal.

A combinação de [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) e letras automáticas (e transacionais) no termo são uma ferramenta valiosa para estabelecer a confiança em se um trabalho dado a um manipulador ou a um grupo de manipuladores ao abrigo de um prazo é recuperado para processamento à medida que o prazo é atingido.

Por exemplo, considere um site que precisa de executar empregos de forma fiável num backend limitado à escala, e que ocasionalmente experimenta picos de tráfego ou quer ser isolado contra episódios de disponibilidade desse backend. No caso regular, o manipulador do lado do servidor para os dados do utilizador submetidos empurra a informação para uma fila e, posteriormente, recebe uma resposta confirmando o manuseamento bem sucedido da transação numa fila de resposta. Se houver um pico de tráfego e o manipulador de backend não puder processar os seus itens de atraso a tempo, os empregos caducados são devolvidos na fila da carta morta. O utilizador interativo pode ser notificado de que a operação solicitada demorará um pouco mais do que o habitual, podendo então o pedido ser colocado numa fila diferente para uma trajetória de processamento onde o resultado do processamento eventual é enviado ao utilizador por e-mail. 


## <a name="temporary-entities"></a>Entidades temporárias

As filas de autocarros de serviço, tópicos e subscrições podem ser criadas como entidades temporárias, que são automaticamente removidas quando não foram utilizadas por um determinado período de tempo.
 
A limpeza automática é útil em cenários de desenvolvimento e teste em que as entidades são criadas de forma dinâmica e não são limpas após a utilização, devido a alguma interrupção do teste ou depuramento. Também é útil quando uma aplicação cria entidades dinâmicas, como uma fila de respostas, para receber respostas de volta a um processo de servidor web, ou em outro objeto de vida relativamente curta onde é difícil limpar essas entidades de forma fiável quando a instância do objeto desaparece.

A funcionalidade está ativada utilizando a propriedade [autoDeleteOnIdle.](/azure/templates/microsoft.servicebus/namespaces/queues) Esta propriedade é definida para a duração para a qual uma entidade deve estar inativa (não ser usada) antes de ser automaticamente eliminada. O valor mínimo para esta propriedade é 5.
 
A propriedade **autoDeleteOnIdle** deve ser definida através de uma operação Azure Resource Manager ou através do cliente .NET [Framework NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs. Não pode colocar no portal.

## <a name="idleness"></a>Ociosidade

Eis o que considerou o ociosidade das entidades (filas, tópicos e assinaturas):

- Filas
    - Sem envios  
    - Sem receber  
    - Sem atualizações para a fila  
    - Sem mensagens agendadas  
    - Sem navegação/espreitar 
- Tópicos  
    - Sem envios  
    - Sem atualizações para o tópico  
    - Sem mensagens agendadas 
- Subscrições
    - Sem receber  
    - Sem atualizações para a subscrição  
    - Nenhuma nova regra adicionada à subscrição  
    - Sem navegação/espreitar  
 


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)