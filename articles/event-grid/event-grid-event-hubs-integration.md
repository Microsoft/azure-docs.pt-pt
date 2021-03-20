---
title: 'Tutorial: Enviar dados do Event Hubs para o armazém de dados - Grade de Eventos'
description: Descreve como armazenar os Centros de Eventos capturados em Azure Synapse Analytics através de funções Azure e gatilhos de Grade de Eventos.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854721"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Transmitir big data para um armazém de dados
A Azure [Event Grid](overview.md) é um serviço inteligente de encaminhamento de eventos que lhe permite reagir a notificações ou eventos de apps e serviços. Por exemplo, pode desencadear uma Função Azure para processar dados do Event Hubs que são capturados para um armazenamento blob ou armazenamento de data lake. Esta [amostra](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra-lhe como usar o Event Grid e as Funções Azure para migrar dados de Centros de Eventos capturados do armazenamento de blob para a Azure Synapse Analytics, especificamente uma piscina de SQL dedicada.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as diferenças dos serviços de mensagens do Azure, veja [Escolher entre os serviços do Azure que entregam mensagens](compare-messaging-services.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para obter uma introdução à Recolha dos Hubs de Eventos, veja [Ativar Recolha dos Hubs de Eventos com o portal do Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para obter mais informações sobre como configurar e executar o exemplo, veja [Exemplo do Event Grid e da Recolha dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
