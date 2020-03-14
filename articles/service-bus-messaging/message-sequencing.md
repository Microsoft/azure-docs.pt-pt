---
title: Sequenciação de mensagens de ônibus de serviço Azure e selos de tempo Microsoft Docs
description: Este artigo explica como preservar a sequenciação e a encomenda (com selos temporais) das mensagens azure service bus.
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
ms.openlocfilehash: 54d774c00fa650cb9608f46cc07b9d899709eaa5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261661"
---
# <a name="message-sequencing-and-timestamps"></a>Sequência de mensagens e carimbos de data/hora

Sequenciação e marcação de tempo são duas funcionalidades que são sempre ativadas em todas as entidades de Ônibus de serviço e superam através das propriedades [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) e [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) de mensagens recebidas ou navegadas.

Para os casos em que a ordem absoluta das mensagens é significativa e/ou em que o consumidor precisa de um identificador único de confiança para mensagens, o corretor carimba mensagens com um número de sequência sem lacunas, aumentando em relação à fila ou tópico. Para entidades divididas, o número de sequência é emitido em relação à partição.

O valor **SequenceNumber** é um inteiro único de 64 bits atribuído a uma mensagem, uma vez que é aceite e armazenado pelo corretor e funciona como seu identificador interno. Para entidades divididas, os 16 bits mais altos refletem o identificador de partição. Os números da sequência passam para zero quando o intervalo de 48/64 bits está esgotado.

O número da sequência pode ser confiado como um identificador único, uma vez que é atribuído por uma autoridade central e neutra e não por clientes. Também representa a verdadeira ordem de chegada, e é mais precisa do que um carimbo de tempo como critério de encomenda, porque os selos de tempo podem não ter uma resolução suficientemente alta a taxas de mensagem extremas e podem estar sujeitos a (ainda que mínimo) relógio distorcido em situações em que o corretor pode não ter uma resolução suficientemente alta a taxas extremas de mensagem e pode estar sujeito a (ainda que mínimo) relógio distorcido em situações em que o corretor pode não ter uma resolução suficientemente alta a taxas de mensagem extremas e pode estar sujeito a (ainda que mínimo) relógio distorcido em situações em que o corretor pode não ter uma resolução suficientemente alta a taxas de mensagem extremas e pode estar sujeito a (ainda que mínimo) relógio distorcido em situações em que o corretor pode não ter uma resolução suficientemente alta transições de propriedade entre nós.

A ordem de chegada absoluta é, por exemplo, em cenários empresariais em que um número limitado de bens oferecidos são servidos numa base de primeiro a chegar primeiro a ser servido enquanto os fornecimentos duram; venda de bilhetes de concerto são um exemplo.

A capacidade de carimbar o tempo funciona como uma autoridade neutra e de confiança que captura com precisão a hora utc de chegada de uma mensagem, refletida na propriedade **EnqueuedTimeUtc.** O valor é útil se um cenário de negócio depender de prazos, como se um item de trabalho foi submetido numa determinada data antes da meia-noite, mas o processamento está muito aquém do atraso na fila.

## <a name="scheduled-messages"></a>Mensagens programadas

Pode enviar mensagens para uma fila ou tópico para processamento atrasado; por exemplo, agendar um trabalho para se tornar disponível para processamento por um sistema em determinado momento. Esta capacidade realiza um programador de tempo distribuído fiável.

As mensagens programadas não se materializam na fila até ao tempo definido de fila. Antes desse tempo, as mensagens programadas podem ser canceladas. O cancelamento elimina a mensagem.

Pode agendar mensagens, definindo a propriedade [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) ao enviar uma mensagem através do caminho de envio regular, ou explicitamente com a API [ScheduleMessageAsync.](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) Este último devolve imediatamente o **Número de Sequência**da mensagem programada, que poderá utilizar mais tarde para cancelar a mensagem programada, se necessário. As mensagens programadas e os seus números de sequência também podem ser descobertos através da [navegação por mensagens.](message-browsing.md)

O **Número de sequência** para uma mensagem programada só é válido enquanto a mensagem estiver neste estado. À medida que a mensagem transita para o estado ativo, a mensagem é anexada à fila como se tivesse sido enquecida no instante atual, o que inclui a atribuição de um novo **SequenceNumber**.

Como a funcionalidade está ancorada em mensagens individuais e as mensagens só podem ser enquecidas uma vez, o Service Bus não suporta horários recorrentes para mensagens.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)