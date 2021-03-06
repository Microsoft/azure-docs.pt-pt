---
title: Integração noutros serviços
titleSuffix: Azure Digital Twins
description: Compreenda os requisitos de entrada e saída ao implementar as Gémeas Digitais Azure.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 118b02ab694d27dbe4e13cbfa1a617a56b052772
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043073"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integre a Azure Digital Twins com outros serviços

A Azure Digital Twins é normalmente utilizada juntamente com outros serviços para criar soluções flexíveis e conectadas que utilizam os seus dados de várias maneiras.

Utilizando rotas de [**eventos,**](concepts-route-events.md)a Azure Digital Twins pode receber dados de serviços a montante como [IoT Hub](../iot-hub/about-iot-hub.md) ou Logic [Apps](../logic-apps/logic-apps-overview.md), que são usados para fornecer telemetria e notificações. 

A Azure Digital Twins também pode encaminhar dados para serviços a jusante, como [Azure Maps](../azure-maps/about-azure-maps.md) e [Time Series Insights,](../time-series-insights/overview-what-is-tsi.md)para armazenamento, integração de fluxos de trabalho, análises e muito mais. 

## <a name="data-ingress"></a>Entrada de dados

A Azure Digital Twins pode ser conduzido com dados e eventos de qualquer serviço,[IoT Hub,](../iot-hub/about-iot-hub.md) [Logic Apps,](../logic-apps/logic-apps-overview.md)seu próprio serviço personalizado, e muito mais. Isto permite-lhe recolher telemetria a partir de dispositivos físicos no seu ambiente, e processar estes dados usando o gráfico Azure Digital Twins na nuvem.

Em vez de ter um IoT Hub incorporado nos bastidores, a Azure Digital Twins permite-lhe "trazer o seu próprio" IoT Hub para usar com o serviço. Você pode usar um Hub IoT existente que você tem atualmente em produção, ou implementar um novo para ser usado para este fim. Isto dá-lhe acesso total a todas as capacidades de gestão de dispositivos do IoT Hub.

Para ingerir dados de qualquer fonte em Azure Digital Twins, utilize uma [**função Azure**](../azure-functions/functions-overview.md). Saiba mais sobre este padrão em [*Como-a-: Ingerir telemetria a partir do IoT Hub,*](how-to-ingest-iot-hub-data.md)ou experimente-o no Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md). 

Também pode aprender a ligar as Gémeas Digitais Azure a um gatilho de Apps Lógicas em [*Como-a-: Integrar-se com apps lógicas.*](how-to-integrate-logic-apps.md)

## <a name="data-egress-services"></a>Serviços de saída de dados

A Azure Digital Twins pode enviar dados para **pontos finais conectados**. Os pontos finais suportados podem ser:
* [Hub de Eventos](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Os pontos finais são anexados ao Azure Digital Twins através de APIs de gestão ou do portal do Azure. Saiba mais sobre como anexar um ponto final às Gémeas Digitais Azure em [*Como-a: Gerir pontos finais e rotas*](how-to-manage-routes-apis-cli.md).

Há muitos outros serviços para os quais poderá ter como objetivo direcionar os dados, como o [Armazenamento do Azure](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md)ou [Time Series insights](../time-series-insights/overview-what-is-tsi.md). Para enviar os seus dados para serviços como estes, anexe o serviço de destino a um ponto final.

Por exemplo, se também estiver a utilizar o Azure Maps e quiser correlacionar a [localização](concepts-twins-graph.md)com o seu gráfico gémeo Azure Digital Twins, pode utilizar funções Azure com a Grade de Eventos para estabelecer comunicação entre todos os serviços da sua implementação. Saiba mais sobre isso em [ *How-to: Use Azure Digital Twins para atualizar um mapa interior do Azure Maps*](how-to-integrate-maps.md)

Também pode aprender a encaminhar dados de forma semelhante à Time Series Insights, em [*Como-a-: Integrar-se com insights de séries de tempo.*](how-to-integrate-time-series-insights.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre pontos finais e eventos de encaminhamento para serviços externos:
* [*Conceitos: Eventos de Roting Azure Digital Twins*](concepts-route-events.md)

Veja como configurar a Azure Digital Twins para ingerir dados do IoT Hub:
* [*Como fazer: Ingerir telemetria a partir do IoT Hub*](how-to-ingest-iot-hub-data.md)