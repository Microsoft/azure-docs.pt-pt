---
title: Subscrições da Azure Event Grid através do portal
description: Este artigo descreve como criar subscrições de Event Grid para as fontes suportadas, como o Azure Blob Storage, utilizando o portal Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2890f57e50e508f85579a2b1198de536fa9a9013
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119995"
---
# <a name="subscribe-to-events-through-portal"></a>Subscreva eventos através do portal

Este artigo descreve como criar subscrições de Grade de Eventos através do portal.

## <a name="create-event-subscriptions"></a>Criar subscrições de eventos

Para criar uma subscrição de Grade de Eventos para qualquer uma das fontes de eventos suportadas, utilize os [seguintes](overview.md#event-sources)passos. Este artigo mostra como criar uma subscrição de Grade de Eventos para uma subscrição do Azure.

1. Selecione **Todos os serviços**.

   ![Selecione todos os serviços](./media/subscribe-through-portal/select-all-services.png)

1. Procure por **Subscrições de Grelha de Eventos** e selecione-as nas opções disponíveis.

   ![Pesquisa](./media/subscribe-through-portal/search.png)

1. Selecione **+ Subscrição de Eventos**.

   ![Adicionar subscrição](./media/subscribe-through-portal/add-subscription.png)

1. Selecione o tipo de subscrição que pretende criar. Por exemplo, para subscrever eventos para a sua subscrição Azure, selecione **Subscrições Azure** e a subscrição-alvo.

   ![Selecione a subscrição do Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Para subscrever todos os tipos de eventos para esta fonte de evento, mantenha a opção **Subscrever a todos os tipos de eventos** verificados. Caso contrário, selecione os tipos de eventos para esta subscrição.

   ![Selecione tipos de eventos](./media/subscribe-through-portal/select-event-types.png)

1. Forneça detalhes adicionais sobre a subscrição do evento, como o ponto final para lidar com eventos e um nome de subscrição.

   ![Fornecer detalhes de subscrição](./media/subscribe-through-portal/provide-subscription-details.png)

1. Para ativar as letras mortas e personalizar as políticas de repetição, selecione **Funcionalidades Adicionais**.

   ![Selecione funcionalidades adicionais](./media/subscribe-through-portal/select-additional-features.png)

1. Selecione um recipiente para usar para armazenar eventos que não são entregues, e deite as recaídas enviadas.

   ![Habilitar letras mortas e redaçar](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Quando terminar, selecione **Criar**.

## <a name="create-subscription-on-resource"></a>Criar subscrição no recurso

Algumas fontes de eventos suportam a criação de uma subscrição de eventos através da interface do portal para esse recurso. Selecione a fonte do evento e procure **eventos** no painel esquerdo.

![Fornecer detalhes de subscrição](./media/subscribe-through-portal/resource-events.png)

O portal apresenta-lhe opções para criar uma subscrição de eventos relevante para essa fonte.

## <a name="next-steps"></a>Próximos passos

* Para obter informações sobre entrega de eventos e retrórias, [entrega e redação de mensagens de Event Grid.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
