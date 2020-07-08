---
title: Manipuladores de eventos Azure Event Grid
description: Descreve os manipuladores de eventos suportados para a Azure Event Grid. Azure Automation, Functions, Event Hubs, Hybrid Connections, Logic Apps, Service Bus, Queue Storage, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83592462"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos em Azure Event Grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais algumas medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos. Também pode usar qualquer webhook para lidar com eventos. O webhook não precisa de ser hospedado em Azure para lidar com eventos. O Event Grid suporta apenas pontos finais https webhook.

## <a name="supported-event-handlers"></a>Manipuladores de eventos apoiados
Aqui estão os manipuladores de eventos suportados: 

- [Webhooks.](handler-webhooks.md) Os runbooks e as aplicações lógicas da Azure Automation são suportados através de webhooks. 
- [Funções Azure](handler-functions.md)
- [Hubs de eventos](handler-event-hubs.md)
- [Ligações híbridas de reencaminhamento](handler-relay-hybrid-connections.md)
- [Filas e tópicos de autocarro de serviço](handler-service-bus.md)
- [Filas de armazenamento](handler-storage-queues.md)

## <a name="next-steps"></a>Próximos passos
- Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
