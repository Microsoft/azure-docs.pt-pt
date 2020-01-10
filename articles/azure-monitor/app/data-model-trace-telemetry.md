---
title: Modelo de dados do Aplicativo Azure insights – telemetria de rastreamento
description: Modelo de dados Application Insights para a telemetria de rastreamento
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6e188039a86f4c655df3098be1d769668dcf3571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407141"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreamento: modelo de dados de Application Insights

A telemetria de rastreamento (em [Application insights](../../azure-monitor/app/app-insights-overview.md)) representa `printf` instruções de rastreamento de estilo que são pesquisadas em texto. `Log4Net`, `NLog`e outras entradas de arquivo de log baseadas em texto são traduzidas em instâncias desse tipo. O rastreamento não tem medidas como uma extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de rastreamento.

Comprimento máximo: 32768 caracteres

## <a name="severity-level"></a>Nível de gravidade

Nível de severidade do rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error``Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- [Explore os logs de rastreamento do .net em Application insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Explore os logs de rastreamento do Java em Application insights](../../azure-monitor/app/java-trace-logs.md).
- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- [Gravar telemetria de rastreamento personalizada](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
