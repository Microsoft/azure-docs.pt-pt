---
title: Utilize o portal Azure para criar tópicos e subscrições do Service Bus
description: 'Quickstart: Neste arranque rápido, aprende-se a criar um tópico de Service Bus e subscrições para esse tópico utilizando o portal Azure.'
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 749ba3d2947dfd7defdf2549305f2a61c16ba713
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95803293"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Utilizar o portal do Azure para criar um tópico do Service Bus e subscrições para o tópico
Neste arranque rápido, você usa o portal Azure para criar um tópico de Service Bus e, em seguida, criar subscrições para esse tópico. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as subscrições do Service Bus?
Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com as filas do Service Bus, em que cada mensagem é processada por um único consumidor, os tópicos e as subscrições fornecem uma forma de comunicação de um a muitos, utilizando um padrão de publicação/subscrição. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente. Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Pode registar opcionalmente regras de filtragem para um tópico por subscrição, o que lhe permite filtrar ou restringir quais as mensagens a um tópico que são recebidas através das quais as subscrições de tópicos são recebidas.

Os tópicos e subscrições do Service Bus permitem-lhe dimensionar para processar um grande número de mensagens através de um grande número de utilizadores e aplicações.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um espaço de nomes de Service Bus, um tópico no espaço de nomes e três subscrições do tema. Para aprender a publicar mensagens para o tema e subscrever mensagens de uma subscrição, consulte um dos seguintes quickstarts na secção **Publicar e subscreva para** a secção de mensagens. 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)