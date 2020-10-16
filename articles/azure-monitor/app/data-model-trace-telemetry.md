---
title: Modelo de dados de insights de aplicação Azure - Trace Telemetria
description: Modelo de dados de Insights de Aplicação para telemetria de traços
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320549"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de traços: Modelo de dados de Insights de Aplicação

A telemetria trace (in [Application Insights)](./app-insights-overview.md)representa `printf` declarações de traços de estilo que são pesquisadas por texto. `Log4Net`, `NLog` e outras entradas de ficheiros de registo baseados em texto são traduzidas em instâncias deste tipo. O vestígio não tem medidas como extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de traço.

Comprimento máximo: 32768 caracteres

## <a name="severity-level"></a>Nível de gravidade

O nível de gravidade do traço. O valor pode `Verbose` ser, `Information` , , , . `Warning` `Error` `Critical` .

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- [Explore registos de traços .NET em Insights de Aplicação](./asp-net-trace-logs.md).
- [Explore os registos de vestígios de Java em Application Insights](./java-trace-logs.md).
- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- [Escreva telemetria de traços personalizados](./api-custom-events-metrics.md#tracktrace)
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

