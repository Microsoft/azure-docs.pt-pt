---
title: Modelo de dados de telemetria do Aplicativo Azure insights – telemetria de eventos | Microsoft Docs
description: Modelo de dados Application Insights para telemetria de eventos
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 620983fb2174fe7f32a61503b0b87e8e7ce0c330
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678154"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria de eventos: modelo de dados de Application Insights

Você pode criar itens de telemetria de eventos (em [Application insights](../../azure-monitor/app/app-insights-overview.md)) para representar um evento que ocorreu em seu aplicativo. Normalmente, é uma interação do usuário, como o clique do botão ou o check-out do pedido. Ele também pode ser um evento de ciclo de vida do aplicativo, como inicialização ou atualização de configuração. 

Semanticamente, os eventos podem ou não ser correlacionados a solicitações. No entanto, se usado corretamente, a telemetria de eventos é mais importante do que solicitações ou rastreamentos. Os eventos representam a telemetria de negócios e devem ser um assunto para uma [amostragem](../../azure-monitor/app/api-filtering-sampling.md)separada e menos agressiva.

## <a name="name"></a>Nome

Nome do evento. Para permitir o agrupamento adequado e as métricas úteis, restrinja seu aplicativo para que ele gere um pequeno número de nomes de evento separados. Por exemplo, não use um nome separado para cada instância gerada de um evento.

Comprimento máximo: 512 caracteres

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- [Gravar telemetria de evento personalizado](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
