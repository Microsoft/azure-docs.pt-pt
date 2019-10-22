---
title: Modelo de dados de telemetria do Aplicativo Azure insights – telemetria de rastreamento | Microsoft Docs
description: Modelo de dados Application Insights para a telemetria de rastreamento
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 855a93d8a6350c625fe0820fae83644aec3459ed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678122"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreamento: modelo de dados de Application Insights

A telemetria de rastreamento (em [Application insights](../../azure-monitor/app/app-insights-overview.md)) representa `printf` instruções de rastreamento de estilo que são pesquisadas em texto. `Log4Net`, `NLog` e outras entradas de arquivo de log baseadas em texto são traduzidas em instâncias desse tipo. O rastreamento não tem medidas como uma extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de rastreamento.

Comprimento máximo: 32768 caracteres

## <a name="severity-level"></a>Nível de gravidade

Nível de severidade do rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` `Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- [Explore os logs de rastreamento do .net em Application insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Explore os logs de rastreamento do Java em Application insights](../../azure-monitor/app/java-trace-logs.md).
- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- [Gravar telemetria de rastreamento personalizada](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
