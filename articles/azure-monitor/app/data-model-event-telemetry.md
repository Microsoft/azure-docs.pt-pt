---
title: Modelo de dados de telemetria da aplicação Azure Insights - Telemetria de Eventos Microsoft Docs
description: Modelo de dados de Insights de Aplicação para telemetria de eventos
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671889"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria de eventos: Modelo de dados de Insights de Aplicação

Pode criar itens de telemetria de eventos (em Insights de [Aplicação)](../../azure-monitor/app/app-insights-overview.md)para representar um evento que ocorreu na sua aplicação. Normalmente é uma interação do utilizador, como clicar no botão ou encomendar check-out. Também pode ser um evento de ciclo de vida de aplicação como a inicialização ou a atualização de configuração. 

Semanticamente, os acontecimentos podem ou não estar correlacionados com pedidos. No entanto, se for utilizada corretamente, a telemetria do evento é mais importante do que pedidos ou vestígios. Os eventos representam telemetria empresarial e devem ser sujeitos a [amostragem](../../azure-monitor/app/api-filtering-sampling.md)separada e menos agressiva.

## <a name="name"></a>Nome

Nome do evento. Para permitir um agrupamento adequado e métricas úteis, restrinja a sua aplicação de modo a gerar um pequeno número de nomes de eventos separados. Por exemplo, não use um nome separado para cada instância gerada de um evento.

Comprimento máximo: 512 caracteres

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- Consulte o modelo de [dados](data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- [Escreva telemetria de eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
