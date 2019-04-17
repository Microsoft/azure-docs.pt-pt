---
title: Início rápido - utilizar o portal do Azure para criar subscrições e tópicos do Service Bus | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar um tópico do Service Bus e as subscrições para esse tópico com o portal do Azure.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 2af346b2c9fa5c46593aa9421c3a762bda78dc2f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610309"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Início rápido: Utilizar o portal do Azure para criar um tópico do Service Bus e subscrições para o tópico
Neste início rápido, utilize o portal do Azure para criar um tópico do Service Bus e, em seguida, criar subscrições para esse tópico. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as subscrições do Service Bus?
Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com filas do Service Bus, em que cada mensagem é processada por um único consumidor, tópicos e subscrições fornecem uma forma de um-para-muitos de comunicação, com um padrão de publicação/subscrição. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente. Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, pode registar regras de filtro para um tópico numa base por subscrição, que permite-lhe filtrar ou restringir que mensagens para um tópico são recebidas por que subscrições desse tópico.

Tópicos do Service Bus e as subscrições permitem-lhe dimensionar para processar um grande número de mensagens num grande número de utilizadores e aplicações.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="next-steps"></a>Passos Seguintes
Para saber como enviar mensagens para um tópico e reiceve essas mensagens por meio de uma subscrição, veja o seguinte artigo: selecione a linguagem de programação no índice. 

> [!div class="nextstepaction"]
> [Publicar e subscrever as mensagens](service-bus-dotnet-how-to-use-topics-subscriptions.md)
