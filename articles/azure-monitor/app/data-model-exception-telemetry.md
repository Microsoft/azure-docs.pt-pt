---
title: Modelo de dados de telemetria de insights de aplicação Azure
description: Modelo de dados de Insights de Aplicação para telemetria de exceção
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671941"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria de exceção: Modelo de dados de Insights de Aplicação

No [Application Insights](../../azure-monitor/app/app-insights-overview.md), um exemplo de Exceção representa uma exceção manipulada ou não tratada que ocorreu durante a execução da aplicação monitorizada.

## <a name="problem-id"></a>Id problema

Identificador de onde a exceção foi lançada em código. Usado para agrupar exceções. Tipicamente uma combinação de tipo de exceção e uma função da pilha de chamadas.

Comprimento máximo: 1024 caracteres

## <a name="severity-level"></a>Nível de gravidade

Nível de gravidade do traço. O valor `Verbose` `Information`pode `Warning` `Error`ser, . . . `Critical`

## <a name="exception-details"></a>Detalhes da exceção

(A ser alargado)

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- Consulte o modelo de [dados](data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- Saiba como [diagnosticar exceções nas suas aplicações web com Insights](../../azure-monitor/app/asp-net-exceptions.md)de Aplicação .
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
