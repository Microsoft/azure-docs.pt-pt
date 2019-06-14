---
title: Sequência de mensagens do Service Bus do Azure e carimbos de data / | Documentos da Microsoft
description: Preservar a sequência de mensagem do Service Bus e ordem carimbos de data
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
ms.openlocfilehash: 8665d0a1fccecf5521a553a894e2a55e52384ec3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402723"
---
# <a name="message-sequencing-and-timestamps"></a>Sequência de mensagens e carimbos de data/hora

Sequenciamento e carimbo são dois recursos que são sempre ativados em todas as entidades do Service Bus e de superfície através da [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) e [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) propriedades de recebidos ou pesquisado mensagens.

Para os casos em que ordem absoluto das mensagens é significativo e/ou em que um consumidor tem um identificador exclusivo confiável para mensagens, as mensagens de carimbos de Mediador com uma lacuna e sem aumentar o número de sequência em relação a fila ou tópico. Para entidades particionadas, o número de sequência é emitido em relação a partição.

O **SequenceNumber** valor é um inteiro de 64 bits exclusivo atribuído a uma mensagem à medida que ele é aceite e armazenado pela broker e funções como seu identificador interno. Para entidades particionadas, os mais alto 16 bits refletem o identificador de partição. Números de sequência de rollover para zero quando o intervalo de 48/64 bits se esgota.

O número de sequência de mensagens em fila pode ser fidedigno como um identificador exclusivo, uma vez que é atribuído por uma autoridade central e neutra e não pelos clientes. Ele também representa a ordem de chegada de verdadeira e é mais preciso que um carimbo de data / hora como um critério de ordem, porque os carimbos de data / hora não pode ter uma resolução alta o suficiente às taxas de mensagem extreme e poderá estar sujeito a (no entanto mínima) distorção em situações em que o Mediador propriedade faz a transição entre os nós.

A ordem de chegada absoluto é importante, por exemplo, nos negócios cenários em que um número limitado de fornecido bens são fornecidos numa base servidos de primeiro vêm primeiro enquanto estiver disponível pela última vez; vendas de bilhetes do conjunto são um exemplo.

O recurso de registro de tempo atua como uma autoridade de neutra e confiável que captura com precisão a hora UTC de chegada de uma mensagem, refletida na **EnqueuedTimeUtc** propriedade. O valor é útil se um cenário de negócio depende de prazos, por exemplo, se um item de trabalho foi submetido numa determinada data antes de meia-noite, mas o processamento é coisa do passado o registo de segurança da fila.

## <a name="scheduled-messages"></a>Mensagens agendadas

Pode enviar mensagens para uma fila ou tópico para processamento atrasado; Por exemplo, para agendar um trabalho fiquem disponíveis para processamento por um sistema em determinado momento. Esta capacidade apercebe-se de um agendador baseados no tempo de distribuídas fiável.

Mensagens agendadas não materializar na fila até a hora de colocar em fila definidos. Antes dessa data, podem ser canceladas mensagens agendadas. Cancelamento elimina a mensagem.

Pode agendar mensagens de qualquer um, definindo a [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) propriedade ao enviar uma mensagem por meio do caminho de envio normal ou explicitamente com o [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. O segundo devolve imediatamente a mensagem agendada **SequenceNumber**, que pode utilizar mais tarde para cancelar a mensagem agendada, se necessário. Mensagens agendadas e seus números de sequência podem também ser detetados usando [navegação de mensagens](message-browsing.md).

O **SequenceNumber** para uma mensagem agendada só é válida durante a mensagem está neste estado. Como as transições de mensagem para o estado do Active Directory, a mensagem é acrescentada à fila como se tivessem sido colocados em fila no instante atual, que inclui a atribuir um novo **SequenceNumber**.

Uma vez que a funcionalidade é ancorada nas mensagens individuais e as mensagens podem apenas ser colocados em fila uma vez, do Service Bus não suporta agendamentos periódicos para mensagens.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)