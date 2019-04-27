---
title: Modelo de dados de telemetria de informações de aplicação do Azure - telemetria de dependência | Documentos da Microsoft
description: Modelo de dados do Application Insights para a telemetria de dependência
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 3e3d6b8fdc9ac8dd28f73fecd6231e97a5645407
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901030"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependência: Modelo de dados do Application Insights

Telemetria de dependência (no [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representa uma interação do componente monitorizado com um componente remoto, como SQL ou um ponto final HTTP.

## <a name="name"></a>Name

Nome do comando iniciado com chamada de dependência. Valor de cardinalidade baixa. Os exemplos são o nome do procedimento armazenado e o modelo de caminho de URL.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Utilizar correlação com o item de telemetria de pedido correspondente a esta chamada de dependência. Para obter mais informações, consulte [correlação](../../azure-monitor/app/correlation.md) página.

## <a name="data"></a>Dados

Comandos iniciados por essa chamada de dependência. Os exemplos são a instrução SQL e o URL de HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Type

Nome do tipo de dependência. Valor de cardinalidade baixa para um agrupamento lógico de dependências e a interpretação dos outros campos, como commandName e resultCode. Os exemplos são SQL, a tabela do Azure e o HTTP.

## <a name="target"></a>Destino

Site de destino de uma chamada de dependência. Os exemplos são o nome de servidor, o endereço do anfitrião. Para obter mais informações, consulte [correlação](../../azure-monitor/app/correlation.md) página.

## <a name="duration"></a>Duração

Duração no formato do pedido: `DD.HH:MM:SS.MMMMMM`. Tem de ser inferior a `1000` dias.

## <a name="result-code"></a>Código de resultado

Código de resultado de uma chamada de dependência. Os exemplos são o código de erro SQL e o código de estado HTTP.

## <a name="success"></a>Êxito

Indicação de chamada com êxito ou sem êxito.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Passos Seguintes

- Configurar dependência de controlo para [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Configurar dependência de controlo para [Java](../../azure-monitor/app/java-agent.md).
- [Escrever telemetria de dependência personalizadas](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Ver [modelo de dados](data-model.md) para o modelo de tipos e dados do Application Insights.
- Confira [plataformas](../../azure-monitor/app/platforms.md) suportada pelo Application Insights.
