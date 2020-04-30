---
title: Utilize o portal Azure para criar tópicos e subscrições de ônibus de serviço
description: 'Quickstart: Neste arranque rápido, aprende-se a criar um tópico de Ônibus de serviço e subscrições a esse tema utilizando o portal Azure.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: a1e330a62a58daaa3fb2a2e8758d14791a3208c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76260825"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Quickstart: Use o portal Azure para criar um tópico de ônibus de serviço e subscrições para o tópico
Neste arranque rápido, você usa o portal Azure para criar um tópico de ônibus de serviço e, em seguida, criar subscrições para esse tópico. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as subscrições do Service Bus?
Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com as filas de ônibus de serviço, em que cada mensagem é processada por um único consumidor, tópicos e subscrições fornecem uma forma de comunicação de um a muitos, usando um padrão de publicação/subscrição. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente. Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Pode registar opcionalmente regras de filtro para um tópico por subscrição, o que lhe permite filtrar ou restringir quais as mensagens para um tópico que são recebidas através do qual são recebidas as subscrições de tópicos.

Os tópicos e subscrições do Service Bus permitem-lhe escalar para processar um grande número de mensagens em um grande número de utilizadores e aplicações.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para aprender a enviar mensagens para um tópico e receber essas mensagens através de uma subscrição, consulte o seguinte artigo: selecione o idioma de programação no TOC. 

> [!div class="nextstepaction"]
> [Publicar e subscrever para mensagens](service-bus-dotnet-how-to-use-topics-subscriptions.md)
