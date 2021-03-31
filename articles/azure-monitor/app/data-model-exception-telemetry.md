---
title: Modelo de dados de telemetria de aplicação Azure
description: Modelo de dados de Insights de Aplicação para telemetria de exceção
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6732fb3dde79abd5c69e2b0a91710bdd2356d6aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320600"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria de exceção: Modelo de dados de Insights de Aplicação

No [Application Insights](./app-insights-overview.md), uma instância de Exceção representa uma exceção manuseada ou não manipulada que ocorreu durante a execução da aplicação monitorizada.

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

## <a name="next-steps"></a>Passos seguintes

- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Saiba como [diagnosticar exceções nas suas aplicações web com Application Insights](./asp-net-exceptions.md).
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

