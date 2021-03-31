---
title: Manipuladores de eventos Azure Event Grid
description: Descreve os manipuladores de eventos suportados para a Azure Event Grid. Azure Automation, Functions, Event Hubs, Hybrid Connections, Logic Apps, Service Bus, Queue Storage, Webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86117020"
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

## <a name="next-steps"></a>Passos seguintes
- Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
