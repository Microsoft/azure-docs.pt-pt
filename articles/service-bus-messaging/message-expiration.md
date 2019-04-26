---
title: Expiração de mensagens do Service Bus do Azure | Documentos da Microsoft
description: Expiração e TTL de mensagens do Service bus do Azure
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 1ea645ee53f91a62bd49fb1da0d44e2962708b88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402773"
---
# <a name="message-expiration-time-to-live"></a>Expiração de mensagem (TTL)

O payload de uma mensagem, ou um comando ou consulta que transmita de uma mensagem para um recetor, é quase sempre sujeitas a alguma forma de prazo de expiração de nível de aplicativo. Após um prazo, o conteúdo já não é entregue ou já não é possível executar a operação pedida.

Para desenvolvimento e ambientes de teste no qual as filas e tópicos, muitas vezes, são utilizados no contexto das execuções parciais de aplicações ou partes do aplicativo, também é aconselhável para mensagens de teste fique preso a ser automaticamente lixo coletado, para que execute o seguinte teste pode Inicie limpa.

A expiração de qualquer mensagem individual pode ser controlada, definindo a [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) propriedade de sistema, que especifica um período de tempo relativo. A expiração torna-se instante absoluto quando a mensagem é colocado em fila para a entidade. Nessa altura, o [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) propriedade assume o valor [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). Não é imposta a definição para time-to-live (TTL) numa mensagem mediada quando nenhum cliente não está ouvindo ativamente.

Nos últimos a **ExpiresAtUtc** instantâneas, mensagens de se tornar elegíveis para obtenção. A expiração não afeta as mensagens que estão bloqueadas atualmente para entrega; essas mensagens ainda são processadas normalmente. Se o bloqueio expira ou a mensagem é abandonada, a expiração entrar em vigor imediato.

Enquanto a mensagem está abaixo do bloqueio, o aplicativo poderá estar em posse de uma mensagem que expirou. Se o aplicativo está disposto a vá em frente com o processamento ou optar por abandonar a mensagem cabe ao implementador.

## <a name="entity-level-expiration"></a>Expiração de nível de entidade

Todas as mensagens enviadas para uma fila ou tópico estão sujeitos a expiração um padrão que estiver definida para a entidade de nível com o [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) propriedade e o que também pode ser definido no portal durante a criação e ajustado mais tarde. A expiração padrão é utilizada para todas as mensagens enviadas para a entidade em que [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) não for explicitamente definida. A expiração padrão também funciona como um limite para o **TimeToLive** valor. As mensagens com uma maior **TimeToLive** expiração que o valor predefinido são automaticamente ajustadas para o **defaultMessageTimeToLive** valor antes de ser colocados em fila.

Mensagens expiradas, opcionalmente, podem ser movidas para uma [fila de mensagens não entregues](service-bus-dead-letter-queues.md) ao definir o [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) propriedade ou verificar a respetiva caixa no portal do. Se a opção for deixada desativado, mensagens expiradas são removidas. Mensagens expiradas movidas para a fila de mensagens não entregues podem distinguir de outras mensagens lettered em papel já era avaliando os [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) propriedade que o mediador armazena na secção de propriedades de utilizador; o valor é [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) neste caso.

No caso mencionado anteriormente na qual a mensagem está protegida contra expiração ao mesmo tempo em bloqueio e se o sinalizador é definido na entidade, a mensagem é movida para a fila de mensagens não entregues à medida que o bloqueio é abandonado ou expira. No entanto, não é movido se a mensagem será com êxito liquidada, que, em seguida, parte do princípio de que o aplicativo foi processado com sucesso, apesar da expiração nominal.

A combinação de [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) e automática (e transacional) mensagens não entregues no expiração são uma ferramenta valiosa para o estabelecimento de confiança na se uma tarefa para um manipulador ou um grupo de processadores num prazo é recuperada para processamento como o prazo for atingido.

Por exemplo, considere um web site que precisa de forma fiável, executar tarefas num back-end de restrição de dimensionamento, e que, ocasionalmente, o tráfego de experiências picos ou quer ser isolado em relação a episódios de disponibilidade desse back-end. No caso do regular, o manipulador do lado do servidor para os dados de utilizador submetido envia as informações numa fila e, em seguida, recebe uma resposta confirmar manipulação bem-sucedida da transação numa fila de resposta. Se existir um pico de tráfego e o manipulador de back-end não é possível processar seus itens de lista de pendências no tempo, as tarefas expiradas são devolvidas na fila de mensagens não entregues. O usuário interativo pode ser notificado de que a operação pedida irá demorar um pouco mais do que o habitual, e o pedido pode, em seguida, ser colocado numa fila diferente para um caminho de processamento em que o resultado do processamento eventual é enviado para o utilizador por e-mail. 


## <a name="temporary-entities"></a>Entidades temporárias

Filas do Service Bus, tópicos e subscrições podem ser criadas como entidades temporárias, que são automaticamente removidas quando elas não têm sido usadas para um período de tempo especificado.
 
A limpeza automática é útil em cenários de desenvolvimento e teste no qual as entidades são criadas dinamicamente e não são limpos após o uso, devido a alguma interrupção do teste ou executar depuração. Também é útil quando um aplicativo criar entidades dinâmicas, como uma fila de resposta, para receber respostas de volta para um processo de servidor web ou a outro objeto relativamente curta duração em que é difícil de forma fiável limpar essas entidades quando o objeto instância desaparece.

A funcionalidade é habilitada com o [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) propriedade. Esta propriedade é definida como a duração para o qual uma entidade tem de ser de inatividade (não utilizado) antes de ser eliminado automaticamente. O valor mínimo para essa propriedade é 5.
 
O **autoDeleteOnIdle** propriedade deve ser definida através de uma operação do Azure Resource Manager ou através do cliente do .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs. Não é possível defini-lo no portal.

## <a name="idleness"></a>Inatividade

Eis o que considerado inatividade de entidades (filas, tópicos e subscrições):

- Filas
    - Não envia  
    - Não recebe  
    - Não existem atualizações para a fila  
    - Não existem mensagens agendadas  
    - Não procurar/pré-visualização 
- Tópicos  
    - Não envia  
    - Não existem atualizações para o tópico  
    - Não existem mensagens agendadas 
- Subscriptions
    - Não recebe  
    - Não existem atualizações para a subscrição  
    - Não existem novas regras incluídas na assinatura  
    - Não procurar/pré-visualização  
 


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)