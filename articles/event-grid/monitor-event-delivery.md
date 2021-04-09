---
title: Ver métricas da grelha de eventos Azure e definir alertas
description: Este artigo descreve como usar o portal Azure para ver métricas para tópicos e subscrições de Azure Event Grid e criar alertas sobre eles.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598563"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorar a entrega de mensagens da grelha de eventos 
Este artigo descreve como usar o portal para ver métricas para tópicos e subscrições de Event Grid, e criar alertas sobre eles. 

> [!IMPORTANT]
> Para obter uma lista de métricas suportadas Azure Event Grid, consulte [métricas](metrics.md).

## <a name="view-custom-topic-metrics"></a>Ver métricas de tópicos personalizados

Se publicou um tópico personalizado, pode ver as métricas para o mesmo. 

1. Inscreva-se no [portal Azure](https://portal.azure.com/).
2. Na barra de pesquisa do tópico, escreva **Tópicos de Grelha de Eventos** e, em seguida, selecione Tópicos de Grelha de **Eventos** da lista de drop-down. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Procurar e selecionar tópicos de grelha de eventos":::
3. Selecione o seu tópico personalizado na lista de tópicos. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Selecione o seu tópico personalizado":::
4. Veja as métricas para o tema do evento personalizado na página **tópico da grelha de evento.** Na imagem a seguir, a secção **Essentials** que mostra o grupo de recursos, subscrição etc. é minimizada. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Ver métricas do evento":::

    Pode criar gráficos com métricas suportadas utilizando o separador **Métricas** da página **Tópico de Grelha de Evento.**

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Tópico - Página de métricas":::

    Por exemplo, consulte o gráfico de métricas para a **métrica de Eventos Publicados.**

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Métrica de eventos publicados":::


## <a name="view-subscription-metrics"></a>Ver métricas de subscrição
1. Navegue para a página **tópico da grelha de eventos** seguindo os passos da secção anterior. 
2. Selecione a subscrição a partir do painel inferior, como mostrado no exemplo seguinte. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Selecione subscrição de eventos":::    

    Também pode pesquisar **subscrições de Grade de Eventos** na barra de pesquisa no portal Azure, selecione **Topic Type,** **Subscrição** e **Localização** para ver uma subscrição do evento. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Selecione a subscrição do evento a partir da página de subscrições da Grade de Eventos":::        

    Para tópicos personalizados, selecione **Tópicos de Grelha de Eventos** como **Tipo tópico**. Para tópicos do sistema, selecione o tipo de recurso Azure, por exemplo, **Contas de Armazenamento (Blob, GPv2)**. 
3. Consulte as métricas da subscrição na página inicial para a subscrição num gráfico. Pode ver as métricas **gerais,** **error** e **latência** para as últimas 1 hora, 6 horas, 12 horas, 1 dia, 7 dias ou 30 dias. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Métricas na página inicial da subscrição":::    

## <a name="view-system-topic-metrics"></a>Ver métricas de tópicos do sistema

1. Inscreva-se no [portal Azure](https://portal.azure.com/).
2. Na barra de pesquisa do tópico, escreva **Tópicos do Sistema de Grelha de Eventos** e, em seguida, selecione Tópicos do Sistema de Grelha de **Eventos** da lista de drop-down. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Procurar e selecionar tópicos do sistema de grelha de eventos":::
3. Selecione o tópico do seu sistema na lista de tópicos. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Selecione o tópico do seu sistema":::
4. Veja as métricas do tópico do sistema na página **tópico do sistema de grelha de evento.** Na imagem a seguir, a secção **Essentials** que mostra o grupo de recursos, subscrição etc. é minimizada. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Ver métricas de tópico do sistema na página geral":::

    Pode criar gráficos com métricas suportadas utilizando o separador **Métricas** da página **Tópico de Grelha de Evento.**

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Tópico do Sistema - Página de métricas":::

    > [!IMPORTANT]
    > Para obter uma lista de métricas suportadas Azure Event Grid, consulte [métricas](metrics.md).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- Para aprender a criar alertas sobre métricas e operações de registo de [atividades, consulte Alertas definidos](set-alerts.md).
- Para obter informações sobre entrega de eventos e retrórias, [entrega e redação de mensagens de Event Grid.](delivery-and-retry.md)
