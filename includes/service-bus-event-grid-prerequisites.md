---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509404"
---
## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma [subscrição do Azure,](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus
Siga as instruções neste tutorial: [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tópico](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) para fazer as seguintes tarefas:

- Crie um espaço de nome de ônibus de serviço **premium.** 
- Pegue a corda de ligação. 
- Crie um tópico de ônibus de serviço.
- Crie uma subscrição do tópico. Você precisa apenas de uma subscrição neste tutorial, por isso não precisa de criar subscrições S2 e S3. 

## <a name="send-messages-to-the-service-bus-topic"></a>Envie mensagens para o tema do Service Bus
Neste passo, utilize uma aplicação de amostra para enviar mensagens para o tópico Service Bus que criou no passo anterior. 

1. Clone o [repositório gitHub-service-bus](https://github.com/Azure/azure-service-bus/).
2. No Estúdio Visual, vá à pasta *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* e, em seguida, abra o ficheiro *SBEventGridIntegration.sln.*
3. Na janela Solution Explorer, expanda o projeto **MessageSender** e selecione **Program.cs**.
4. `<SERVICE BUS NAMESPACE - CONNECTION STRING>`Substitua-a pelo fio de ligação ao seu espaço de nomes service bus e `<TOPIC NAME>` pelo nome do tópico. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Construa e execute o programa para enviar 5 mensagens de teste `const int numberOfMessages = 5;` () para o tópico do Service Bus. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Saída de aplicativo de consola":::
