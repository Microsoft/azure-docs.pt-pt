---
title: Manipuladores de eventos azure Event Grid
description: Descreve os manipuladores de eventos suportados para a Grelha de Eventos Azure. Automação Azure, Funções, Hubs de Eventos, Conexões Híbridas, Aplicações Lógicas, Ônibus de Serviço, Armazenamento de Filas, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592462"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos na Grelha de Eventos Azure
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos. Também pode utilizar qualquer webhook para lidar com eventos. O webhook não precisa de ser hospedado em Azure para lidar com eventos. A Grelha de Eventos suporta apenas pontos finais https webhook.

## <a name="supported-event-handlers"></a>Manipuladores de eventos suportados
Aqui estão os manipuladores de eventos suportados: 

- [Webhooks](handler-webhooks.md). Os livros de execução da Azure Automation e as Aplicações Lógicas são suportados através de webhooks. 
- [Funções azure](handler-functions.md)
- [Centros de eventos](handler-event-hubs.md)
- [Ligações híbridas de retransmissão](handler-relay-hybrid-connections.md)
- [Filas e tópicos de ônibus de serviço](handler-service-bus.md)
- [Filas de armazenamento](handler-storage-queues.md)

## <a name="next-steps"></a>Passos seguintes
- Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
