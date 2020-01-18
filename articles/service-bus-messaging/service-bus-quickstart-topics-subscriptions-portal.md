---
title: Use o portal do Azure para criar tópicos e assinaturas do barramento de serviço
description: 'Início rápido: neste guia de início rápido, você aprende a criar um tópico e assinaturas do barramento de serviço para esse tópico usando o portal do Azure.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: a1e330a62a58daaa3fb2a2e8758d14791a3208c1
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260825"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Início rápido: Use a portal do Azure para criar um tópico e assinaturas do barramento de serviço para o tópico
Neste guia de início rápido, você usa o portal do Azure para criar um tópico do barramento de serviço e, em seguida, criar assinaturas para esse tópico. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as subscrições do Service Bus?
Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com as filas do barramento de serviço, em que cada mensagem é processada por um único consumidor, os tópicos e as assinaturas fornecem uma forma de comunicação de um para muitos, usando um padrão de publicação/assinatura. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente. Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, você pode registrar regras de filtro para um tópico por assinatura, o que permite filtrar ou restringir quais mensagens para um tópico são recebidas por quais assinaturas de tópico.

Os tópicos e as assinaturas do barramento de serviço permitem que você dimensione para processar um grande número de mensagens em um grande número de usuários e aplicativos.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber como enviar mensagens para um tópico e receber essas mensagens por meio de uma assinatura, consulte o seguinte artigo: selecionar a linguagem de programação no sumário. 

> [!div class="nextstepaction"]
> [Publicar e assinar mensagens](service-bus-dotnet-how-to-use-topics-subscriptions.md)
