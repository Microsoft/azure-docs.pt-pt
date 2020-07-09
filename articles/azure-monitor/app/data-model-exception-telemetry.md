---
title: Modelo de dados de telemetria de aplicação Azure
description: Modelo de dados de Insights de Aplicação para telemetria de exceção
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671941"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria de exceção: Modelo de dados de Insights de Aplicação

No [Application Insights](../../azure-monitor/app/app-insights-overview.md), uma instância de Exceção representa uma exceção manuseada ou não manipulada que ocorreu durante a execução da aplicação monitorizada.

## <a name="problem-id"></a>ID de problemas

Identificador de onde a exceção foi lançada em código. Usado para agrupar exceções. Tipicamente uma combinação de tipo de exceção e uma função da pilha de chamadas.

Comprimento máximo: 1024 caracteres

## <a name="severity-level"></a>Nível de gravidade

O nível de gravidade do traço. O valor pode `Verbose` ser, `Information` , , , . `Warning` `Error` `Critical` .

## <a name="exception-details"></a>Detalhes da exceção

(A ser estendido)

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximos passos

- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Saiba como [diagnosticar exceções nas suas aplicações web com Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Confira as [plataformas](../../azure-monitor/app/platforms.md) suportadas pela Application Insights.
