---
title: Tarefas e aplicações de replicação de eventos - Azure Event Hubs | Microsoft Docs
description: Este artigo fornece uma visão geral das tarefas e aplicações de replicação de eventos de construção com Funções Azure
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97663647"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Tarefas e aplicações de replicação de eventos com Funções Azure

> [!TIP]
> Para todas as tarefas de replicação imponentes em que precisa de considerar as cargas dos seus eventos e transformar, agregar, enriquecer ou reduzi-las, utilize [o Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) em vez de Funções Azure.

Como explicado no artigo da [federação de replicação e cross-region,](event-hubs-federation-overview.md) a replicação apátrida de fluxos de eventos entre pares de Centros de Eventos e entre Centros de Eventos e outras fontes e alvos de fluxo de eventos inclina-se sobre as Funções Azure.

[A Azure Functions](../azure-functions/functions-overview.md) é um ambiente de execução escalável e fiável para configurar e executar aplicações sem servidor, incluindo replicação de eventos e tarefas da federação.

Nesta visão geral, você vai aprender sobre as capacidades incorporadas da Azure Functions para tais aplicações, sobre blocos de código que você pode adaptar e modificar para tarefas de transformação, e sobre como configurar uma aplicação Azure Functions de modo que se integra idealmente com Centros de Eventos e outros serviços de Mensagens Azure. Para muitos detalhes, este artigo irá apontar para a documentação do Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









