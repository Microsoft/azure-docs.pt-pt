---
title: Subscrições do Event Grid do Azure através do portal
description: Descreve como criar subscrições do Event Grid através do portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: spelluru
ms.openlocfilehash: b54bc52a2feaf4646d801265ddb273c2c86158ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823560"
---
# <a name="subscribe-to-events-through-portal"></a>Subscrever eventos através do portal

Este artigo descreve como criar subscrições do Event Grid através do portal.

## <a name="create-event-subscriptions"></a>Criar subscrições de eventos

Para criar uma subscrição do Event Grid para qualquer suportadas [origens de eventos](event-sources.md), utilize os seguintes passos. Este artigo mostra como criar uma subscrição do Event Grid para uma subscrição do Azure.

1. Selecione **Todos os serviços**.

   ![Selecione todos os serviços](./media/subscribe-through-portal/select-all-services.png)

1. Procure **subscrições do Event Grid** e selecione-o nas opções disponíveis.

   ![Pesquisa](./media/subscribe-through-portal/search.png)

1. Selecione **+ Subscrição de Eventos**.

   ![Adicionar subscrição](./media/subscribe-through-portal/add-subscription.png)

1. Selecione o tipo de subscrição que pretende criar. Por exemplo, para subscrever eventos para a sua subscrição do Azure, selecione **subscrições do Azure** e a subscrição de destino.

   ![Selecione a subscrição do Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Para subscrever todos os tipos de eventos para esta origem de evento, manter a **subscrever todos os tipos de evento** opção marcada. Caso contrário, selecione os tipos de evento para esta subscrição.

   ![Selecione os tipos de eventos](./media/subscribe-through-portal/select-event-types.png)

1. Fornece detalhes adicionais sobre a subscrição de evento, como o ponto final para manipulação de eventos e um nome de subscrição.

   ![Fornecer detalhes da subscrição](./media/subscribe-through-portal/provide-subscription-details.png)

1. Para ativar mensagens mortas e personalizar as políticas de repetição, selecione **funcionalidades adicionais**.

   ![Selecionar funcionalidades adicionais](./media/subscribe-through-portal/select-additional-features.png)

1. Selecione um contentor a utilizar para armazenar os eventos que não são fornecidos e definir como as repetições são enviadas.

   ![Ativar mensagens não entregues e tente novamente](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Quando terminar, selecione **Criar**.

## <a name="create-subscription-on-resource"></a>Criar subscrição no recurso

Algumas origens de evento suportam a criação de uma subscrição de evento por meio da interface do portal para esse recurso. Selecione a origem do evento e procure **eventos** no painel esquerdo.

![Fornecer detalhes da subscrição](./media/subscribe-through-portal/resource-events.png)

O portal apresenta as opções para criar uma subscrição de evento que são relevante para essa origem.

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
