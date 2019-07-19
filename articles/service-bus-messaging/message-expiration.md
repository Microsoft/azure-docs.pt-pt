---
title: Expiração de mensagem do barramento de serviço do Azure | Microsoft Docs
description: Expiração e vida útil de mensagens do barramento de serviço do Azure
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
ms.openlocfilehash: 109ecc671b43365c433a626ff8d9fe55a5a626b5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310299"
---
# <a name="message-expiration-time-to-live"></a>Expiração de mensagem (TTL)

A carga em uma mensagem, ou um comando ou consulta que uma mensagem transmite a um destinatário, está quase sempre sujeita a alguma forma de prazo de expiração no nível do aplicativo. Após esse prazo, o conteúdo não será mais entregue ou a operação solicitada não será mais executada.

Para ambientes de desenvolvimento e teste em que as filas e os tópicos são frequentemente usados no contexto de execuções parciais de aplicativos ou partes de aplicativos, também é desejável que as mensagens de teste sejam coletadas automaticamente para que a próxima execução de teste possa Iniciar limpeza.

A expiração de qualquer mensagem individual pode ser controlada definindo a propriedade de sistema [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , que especifica uma duração relativa. A expiração se torna um instantâneo absoluto quando a mensagem é enfileirada na entidade. Nesse momento, a propriedade [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) assume o valor [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). A configuração de vida útil (TTL) em uma mensagem orientada não é imposta quando não há clientes ouvindo ativamente.

Após o **ExpiresAtUtc** instantâneo, as mensagens se tornam inqualificadas para recuperação. A expiração não afeta as mensagens atualmente bloqueadas para entrega; essas mensagens ainda são tratadas normalmente. Se o bloqueio expirar ou a mensagem for abandonada, a expiração terá efeito imediato.

Enquanto a mensagem está sob bloqueio, o aplicativo pode estar em posse de uma mensagem que expirou. Se o aplicativo está disposto a prosseguir com o processamento ou opta por abandonar a mensagem é o implementador.

## <a name="entity-level-expiration"></a>Expiração no nível da entidade

Todas as mensagens enviadas para uma fila ou tópico estão sujeitas a uma expiração padrão que é definida no nível de entidade com a propriedade [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) e que também pode ser definida no portal durante a criação e ajustada posteriormente. A expiração padrão é usada para todas as mensagens enviadas para a entidade em que [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) não está definido explicitamente. A expiração padrão também funciona como um teto para o valor **TimeToLive** . As mensagens que têm uma expiração de **TimeToLive** maior do que o valor padrão são silenciosamente ajustadas para o valor **defaultMessageTimeToLive** antes de serem enfileiradas.

> [!NOTE]
> O valor [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) padrão para uma mensagem orientada é [TimeSpan. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) se não for especificado de outra forma.
>
> Para entidades de mensagens (filas e tópicos), o tempo de expiração padrão também é [TimeSpan. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) para as camadas Standard e Premium do barramento de serviço.  Para a camada básica, o tempo de expiração padrão é de 14 dias.

As mensagens expiradas podem, opcionalmente, ser movidas para uma fila de mensagens [mortas](service-bus-dead-letter-queues.md) definindo a propriedade [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) ou marcando a respectiva caixa no Portal. Se a opção for deixada desabilitada, as mensagens expiradas serão descartadas. Mensagens expiradas movidas para a fila de mensagem mortas podem ser diferenciadas de outras mensagens mortas avaliando a propriedade [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que o agente armazena na seção de propriedades do usuário; o valor é [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) nesse caso.

No caso mencionado anteriormente, no qual a mensagem é protegida da expiração enquanto está sob bloqueio e se o sinalizador é definido na entidade, a mensagem é movida para a fila de mensagens mortas enquanto o bloqueio é abandonado ou expira. No entanto, ele não será movido se a mensagem for liquidada com êxito, o que pressupõe que o aplicativo o tratou com êxito, apesar da expiração nominal.

A combinação de [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) e mensagens mortas automáticas (e transacionais) na expiração são uma ferramenta valiosa para estabelecer a confiança em se um trabalho dado a um manipulador ou um grupo de manipuladores em um prazo é recuperado para processamento como o prazo final é atingido.

Por exemplo, considere um site da Web que precisa executar trabalhos de forma confiável em um back-end com restrição de escala e que ocasionalmente enfrenta picos de tráfego ou deseja ser isolado contra episódios de disponibilidade desse back-end. No caso comum, o manipulador do lado do servidor para os dados do usuário enviado envia as informações para uma fila e, posteriormente, recebe uma resposta confirmando o tratamento bem-sucedido da transação em uma fila de resposta. Se houver um pico de tráfego e o manipulador de back-end não puder processar seus itens de lista de pendências no tempo, os trabalhos expirados serão retornados na fila de mensagens mortas. O usuário interativo pode ser notificado de que a operação solicitada levará um pouco mais tempo do que o normal e, em seguida, a solicitação poderá ser colocada em uma fila diferente para um caminho de processamento em que o resultado do processamento eventual seja enviado ao usuário por email. 


## <a name="temporary-entities"></a>Entidades temporárias

As filas, os tópicos e as assinaturas do barramento de serviço podem ser criados como entidades temporárias, que são removidas automaticamente quando não tiverem sido usadas por um período de tempo especificado.
 
A limpeza automática é útil em cenários de desenvolvimento e teste nos quais as entidades são criadas dinamicamente e não são limpas após o uso, devido a alguma interrupção do teste ou da execução da depuração. Também é útil quando um aplicativo cria entidades dinâmicas, como uma fila de resposta, para receber respostas de volta em um processo de servidor Web ou em outro objeto de vida relativamente curta, em que é difícil limpar de forma confiável essas entidades quando o objeto a instância desaparece.

O recurso é habilitado usando a propriedade [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) . Essa propriedade é definida como a duração para a qual uma entidade deve estar ociosa (não usada) antes de ser excluída automaticamente. O valor mínimo para essa propriedade é 5.
 
A propriedade **autoDeleteOnIdle** deve ser definida por meio de uma operação Azure Resource Manager ou por meio das APIs [namespace](/dotnet/api/microsoft.servicebus.namespacemanager) Client .NET Framework. Você não pode defini-lo no Portal.

## <a name="idleness"></a>Ociosidade

Veja o que é considerado ocioso de entidades (filas, tópicos e assinaturas):

- Filas
    - Nenhum envio  
    - Nenhum recebimento  
    - Nenhuma atualização para a fila  
    - Nenhuma mensagem agendada  
    - Sem navegação/Peek 
- Tópicos  
    - Nenhum envio  
    - Não há atualizações para o tópico  
    - Nenhuma mensagem agendada 
- Subscrições
    - Nenhum recebimento  
    - Nenhuma atualização para a assinatura  
    - Nenhuma regra nova adicionada à assinatura  
    - Sem navegação/Peek  
 


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
