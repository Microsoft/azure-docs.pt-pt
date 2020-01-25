---
title: Assinaturas da Rede de Eventos Azure através do portal
description: Este artigo descreve como criar subscrições da Event Grid para as fontes suportadas, como o Armazenamento De Blob Azure, utilizando o portal Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 3172c92ecae094ab5d978803d2ccac7e6404a5e1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721511"
---
# <a name="subscribe-to-events-through-portal"></a>Subscreva eventos através do portal

Este artigo descreve como criar subscrições da Rede de Eventos através do portal.

## <a name="create-event-subscriptions"></a>Criar subscrições de eventos

Para criar uma subscrição da Grelha de Eventos para qualquer uma das fontes de [eventos](event-sources.md)suportadas, utilize os seguintes passos. Este artigo mostra como criar uma subscrição da Rede de Eventos para uma subscrição do Azure.

1. Selecione **Todos os serviços**.

   ![Selecione todos os serviços](./media/subscribe-through-portal/select-all-services.png)

1. Procure **subscrições** de grelha de eventos e selecione-as a partir das opções disponíveis.

   ![Search](./media/subscribe-through-portal/search.png)

1. Selecione **+ Subscrição de Eventos**.

   ![Adicionar subscrição](./media/subscribe-through-portal/add-subscription.png)

1. Selecione o tipo de subscrição que pretende criar. Por exemplo, para subscrever eventos para a sua subscrição Azure, selecione **Assinaturas Azure** e a subscrição do alvo.

   ![Selecione subscrição do Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Para subscrever todos os tipos de eventos para esta fonte de evento, mantenha o **Subscrito a todos os tipos** de eventos verificados. Caso contrário, selecione os tipos de eventos para esta subscrição.

   ![Selecionar tipos de eventos](./media/subscribe-through-portal/select-event-types.png)

1. Forneça detalhes adicionais sobre a subscrição do evento, como o ponto final para lidar com eventos e um nome de subscrição.

   ![Fornecer detalhes de subscrição](./media/subscribe-through-portal/provide-subscription-details.png)

1. Para permitir a letra morta e personalizar políticas de retry, selecione **Funcionalidades Adicionais**.

   ![Selecione funcionalidades adicionais](./media/subscribe-through-portal/select-additional-features.png)

1. Selecione um recipiente para armazenar eventos que não sejam entregues e detetete como as repetições são enviadas.

   ![Permitir letras mortas e retentar](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Quando terminar, selecione **Criar**.

## <a name="create-subscription-on-resource"></a>Criar subscrição de recursos

Algumas fontes de evento supor a criação de uma subscrição de evento através da interface do portal para esse recurso. Selecione a fonte do evento e procure **eventos** no painel esquerdo.

![Fornecer detalhes de subscrição](./media/subscribe-through-portal/resource-events.png)

O portal apresenta-lhe opções para criar uma subscrição de evento que seja relevante para essa fonte.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
