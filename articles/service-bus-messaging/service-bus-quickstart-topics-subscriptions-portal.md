---
title: Utilize o portal Azure para criar tópicos e subscrições do Service Bus
description: 'Quickstart: Neste arranque rápido, aprende-se a criar um tópico de Service Bus e subscrições para esse tópico utilizando o portal Azure.'
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: d3dc63106e1ca5d9db2b14392ca1fb97e3a68289
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88191532"
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
Para aprender a enviar mensagens para um tópico e receber essas mensagens através de uma subscrição, consulte o seguinte artigo: selecione a linguagem de programação no TOC. 

> [!div class="nextstepaction"]
> [Publicar e subscrever para mensagens](service-bus-dotnet-how-to-use-topics-subscriptions.md)
