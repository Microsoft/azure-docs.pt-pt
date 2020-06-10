---
title: Integração com outros serviços
titleSuffix: Azure Digital Twins
description: Compreenda os requisitos de entrada e saída ao implementar as Gémeas Digitais Azure.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ba4188445f9bdf791ef760936bd27ac0f507fb4c
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613237"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integre a Azure Digital Twins com outros serviços

Azure Digital Twins é normalmente usado juntamente com outros serviços. Utilizando as rotas dos [**eventos,**](concepts-route-events.md)a Azure Digital Twins recebe dados de serviços a montante como [o IoT Hub,](../iot-hub/about-iot-hub.md)que é utilizado para fornecer telemetria e notificações. O Azure Digital Twins também pode encaminhar dados para serviços a jusante para armazenamento, integração de fluxos de trabalho, análise e outros usos. 

## <a name="data-ingress"></a>Entrada de dados

As Gémeas Digitais Azure podem ser conduzidas com dados do IoT Hub. Isto permite-lhe recolher telemetria a partir de dispositivos físicos no seu ambiente, e processar estes dados usando o gráfico Azure Digital Twins na nuvem.

A Azure Digital Twins não tem um Hub IoT incorporado. Você pode usar um Hub IoT existente que você tem atualmente em produção, ou implementar um novo. Isto dá-lhe acesso total a todas as capacidades de gestão de dispositivos do IoT Hub.

Para ingerir dados do IoT Hub em Azure Digital Twins, utilize uma [função Azure](../azure-functions/functions-overview.md). Saiba mais sobre este padrão em [Como-a-: Ingerir telemetria a partir do IoT Hub,](how-to-ingest-iot-hub-data.md)ou experimente-o no [tutorial Azure Digital Twins: Conecte uma solução de ponta a ponta](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Serviços de saída de dados

A Azure Digital Twins pode enviar dados para **pontos finais conectados**. Os pontos finais suportados podem ser:
* [Hub de Eventos](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Os pontos finais são anexados a Azure Digital Twins utilizando APIs de gestão ou o portal Azure. Saiba mais sobre como anexar um ponto final às Gémeas Digitais Azure em [Como-a: Gerir pontos finais e rotas](how-to-manage-routes.md).

Existem muitos outros serviços onde poderá querer, em última análise, direcionar os seus dados, tais como [Azure Storage](../storage/common/storage-introduction.md) ou [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Para enviar os seus dados para serviços como estes, anexe o serviço de destino a um ponto final.

Por exemplo, se também estiver a utilizar [o Azure Maps](../azure-maps/about-azure-maps.md) e quiser correlacionar a [localização](concepts-twins-graph.md)com o seu gráfico gémeo Azure Digital Twins, pode utilizar funções Azure com a Grade de Eventos para estabelecer comunicação entre todos os serviços da sua implementação.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre pontos finais e eventos de encaminhamento para serviços externos:
* [Conceitos: Eventos de Roting Azure Digital Twins](concepts-route-events.md)

Veja como configurar a Azure Digital Twins para ingerir dados do IoT Hub:
* [Como fazer: Ingerir telemetria a partir do IoT Hub](how-to-ingest-iot-hub-data.md)
