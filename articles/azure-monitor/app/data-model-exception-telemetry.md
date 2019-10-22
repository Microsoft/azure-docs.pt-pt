---
title: Modelo de dados de telemetria do Aplicativo Azure insights – telemetria de exceção | Microsoft Docs
description: Modelo de dados Application Insights para telemetria de exceção
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0ba1c94ee8dc78b937d650cff32e1518a7ca5a12
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677424"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria de exceção: modelo de dados de Application Insights

No [Application insights](../../azure-monitor/app/app-insights-overview.md), uma instância de exceção representa uma exceção manipulada ou sem tratamento que ocorreu durante a execução do aplicativo monitorado.

## <a name="problem-id"></a>ID do problema

Identificador de onde a exceção foi lançada no código. Usado para Agrupamento de exceções. Normalmente, uma combinação de tipo de exceção e uma função da pilha de chamadas.

Comprimento máximo: 1024 caracteres

## <a name="severity-level"></a>Nível de gravidade

Nível de severidade do rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` `Critical`.

## <a name="exception-details"></a>Detalhes da exceção

(A ser estendido)

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- Saiba como [diagnosticar exceções em seus aplicativos Web com Application insights](../../azure-monitor/app/asp-net-exceptions.md).
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
