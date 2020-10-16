---
title: Azure Application Insights Telemetria Data Model - Telemetria de Eventos Microsoft Docs
description: Modelo de dados de Insights de Aplicação para telemetria de eventos
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 69685afa14352a22b58bccbea342038e4273696e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320617"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria de eventos: Modelo de dados de Insights de Aplicação

Pode criar itens de telemetria de eventos (in [Application Insights)](./app-insights-overview.md)para representar um evento que ocorreu na sua aplicação. Normalmente é uma interação do utilizador, como clique de botão ou check-out de encomenda. Também pode ser um evento de ciclo de vida de aplicação como inicialização ou atualização de configuração. 

Semântica, os acontecimentos podem ou não estar correlacionados com pedidos. No entanto, se for utilizada corretamente, a telemetria de eventos é mais importante do que pedidos ou vestígios. Os eventos representam a telemetria empresarial e devem ser sujeitos a [amostragem](./api-filtering-sampling.md)separada e menos agressiva.

## <a name="name"></a>Nome

Nome do evento. Para permitir o agrupamento adequado e métricas úteis, restringir a sua aplicação de modo a gerar um pequeno número de nomes de eventos separados. Por exemplo, não use um nome separado para cada instância gerada de um evento.

Comprimento máximo: 512 caracteres

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- [Escrever telemetria de evento personalizado](./api-custom-events-metrics.md#trackevent)
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

