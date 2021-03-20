---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: d3d33c87dc1adf65a53b71cc4c833e7f4a191670
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184499"
---
## <a name="what-are-service-bus-queues"></a>O que são filas do Service Bus?
As filas do Service Bus suportam um modelo de comunicação de **mensagens mediadas**. Ao utilizar filas, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de uma fila, que funciona como um intermediário (mediador). Um produtor de mensagens (remetente) entrega uma mensagem à fila e, em seguida, prossegue com o processamento. De modo assíncrono, um consumidor de mensagens (recetor) obtém a mensagem da fila e processa-a. O produtor não tem de aguardar uma resposta do consumidor para poder continuar a processar e a enviar mais mensagens. As filas oferecem uma entrega de mensagens **First In, First Out (FIFO)** para um ou mais consumidores concorrentes. Ou seja, as mensagens são normalmente recebidas e processadas pelos recetores pela ordem em que foram adicionadas à fila e cada mensagem é recebida e processada por apenas um consumidor de mensagens.

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

As filas do Service Bus são uma tecnologia para fins gerais que pode ser utilizada para uma vasta gama de cenários:

* Comunicação entre funções da Web e de trabalho numa aplicação Azure multicamadas.
* Comunicação entre aplicações no local e aplicações alojadas no Azure numa solução híbrida.
* Comunicação entre componentes de uma aplicação distribuída em execução no local em diferentes organizações ou departamentos de uma organização.

A utilização de filas permite ao utilizador dimensionar mais facilmente as aplicações e permite mais resiliência na arquitetura.


