---
title: Descrição geral das Funções do Azure
description: Saiba como as Funções Azure podem ajudar a construir aplicações robustas sem servidor.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 41b627259d84539c868f95eb3cf33db5dbdab52c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97862398"
---
# <a name="introduction-to-azure-functions"></a>Introdução às Funções do Azure

A azure Functions é uma solução sem servidor que lhe permite escrever menos código, manter menos infraestruturas e economizar em custos. Em vez de se preocupar em implementar e manter servidores, a infraestrutura de nuvem fornece todos os recursos atualizados necessários para manter as suas aplicações em funcionamento.

Concentre-se nas partes de código mais importantes para si e as Funções do Azure tratam do resto.<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

Muitas vezes construímos sistemas para reagir a uma série de eventos críticos. Quer esteja a construir uma API web, a responder a alterações na base de dados, a processar fluxos de dados IoT ou até mesmo a gerir filas de mensagens - cada aplicação precisa de uma forma de executar algum código à medida que estes eventos ocorrem.

Para responder a esta necessidade, a Azure Functions fornece "computação a pedido" de duas formas significativas.

Em primeiro lugar, o Azure Functions permite-lhe implementar a lógica do seu sistema em blocos de código facilmente disponíveis. Estes blocos de código são chamados de "funções". Diferentes funções podem ser executadas sempre que precisar para responder a eventos críticos.

Em segundo lugar, à medida que os pedidos aumentam, as Funções Azure satisfazem a procura com o maior número de recursos e instâncias de função que for necessário - mas apenas quando necessário. À medida que os pedidos caem, quaisquer recursos extras e instâncias de aplicação caem automaticamente.

De onde vêm todos os recursos compute computacional? A Azure [Functions fornece o máximo ou quanto poucos recursos de cálculo necessários](./functions-scale.md) para satisfazer a procura da sua aplicação.

Fornecer recursos de computação a pedido é a essência da [computação sem servidor](https://azure.microsoft.com/solutions/serverless/) em Funções Azure.

## <a name="scenarios"></a>Cenários

Em muitos casos, uma função [integra-se com uma série de serviços em nuvem](./functions-triggers-bindings.md) para fornecer implementações ricas em recursos.

Os seguintes são um conjunto comum, _mas de forma alguma exaustivo,_ de cenários para funções azure.

| Se quiser... | então... |
| --- | --- |
| **Construir uma API web** | Implementar um ponto final para as suas aplicações web usando o [gatilho HTTP](./functions-bindings-http-webhook.md) |
| **Uploads de ficheiros de processo** | Executar código quando um ficheiro é carregado ou alterado no [armazenamento de bolhas](./functions-bindings-storage-blob.md) |
| **Construir um fluxo de trabalho sem servidor** | Acorr um conjunto de funções utilizando [funções duráveis](./durable/durable-functions-overview.md) |
| **Responder às alterações da base de dados** | Executar lógica personalizada quando um documento é criado ou atualizado em [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Executar tarefas programadas** | Execute o código em [horários definidos](./functions-bindings-timer.md) |
| **Criar sistemas de fila de mensagens fiáveis** | Processar filas de mensagens usando [armazenamento de fila,](./functions-bindings-storage-queue.md) [ônibus de serviço](./functions-bindings-service-bus.md)ou centros de [eventos](./functions-bindings-event-hubs.md) |
| **Analisar fluxos de dados IoT** | Recolher e processar [dados de dispositivos IoT](./functions-bindings-event-iot.md) |
| **Processar dados em tempo real** | Utilizar [Funções e SignalR](./functions-bindings-signalr-service.md) para responder aos dados no momento |

À medida que constrói as suas funções, tem as seguintes opções e recursos disponíveis:

- **Utilize o seu idioma preferido**: Escreva funções em [C,Java, JavaScript, PowerShell ou Python](./supported-languages.md), ou use um manipulador [personalizado](./functions-custom-handlers.md) para utilizar praticamente qualquer outra língua.

- **Automatizar a implementação**: De uma abordagem baseada em ferramentas para usar gasodutos externos, há uma [miríade de opções de implementação](./functions-deployment-technologies.md) disponíveis.

- **Resolução de problemas uma função**: Use [ferramentas de monitorização](./functions-monitoring.md) e [estratégias de teste](./functions-test-a-function.md) para obter informações sobre as suas aplicações.

- **Opções de preços flexíveis**: Com o plano [de Consumo,](./pricing.md) você só paga enquanto as suas funções estão em execução, enquanto os planos [Premium](./pricing.md) e [App Service](./pricing.md) oferecem funcionalidades para necessidades especializadas.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Começar através de aulas, amostras e tutoriais interativos](./functions-get-started.md)
