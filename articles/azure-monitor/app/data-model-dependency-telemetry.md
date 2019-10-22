---
title: Modelo de dados de telemetria do Aplicativo Azure insights – telemetria de dependência | Microsoft Docs
description: Modelo de dados Application Insights para telemetria de dependência
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: e0923c20b11aa02f380af1faa6766d2346ad1fb2
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677438"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependência: modelo de dados de Application Insights

A telemetria de dependência (em [Application insights](../../azure-monitor/app/app-insights-overview.md)) representa uma interação do componente monitorado com um componente remoto, como SQL ou um ponto de extremidade http.

## <a name="name"></a>Nome

Nome do comando iniciado com esta chamada de dependência. Valor de baixa cardinalidade. Os exemplos são o nome do procedimento armazenado e o modelo do caminho da URL.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Usado para correlação com o item de telemetria de solicitação correspondente a essa chamada de dependência. Para obter mais informações, consulte a página [correlação](../../azure-monitor/app/correlation.md) .

## <a name="data"></a>Dados

Comando iniciado por esta chamada de dependência. Os exemplos são a instrução SQL e a URL HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Tipo

Nome do tipo de dependência. Valor de baixa cardinalidade para agrupamento lógico de dependências e interpretação de outros campos, como commandName e resultCode. Os exemplos são SQL, Azure Table e HTTP.

## <a name="target"></a>Visar

Site de destino de uma chamada de dependência. Os exemplos são nome do servidor, endereço do host. Para obter mais informações, consulte a página [correlação](../../azure-monitor/app/correlation.md) .

## <a name="duration"></a>Duração

Duração da solicitação no formato: `DD.HH:MM:SS.MMMMMM`. Deve ser menor que `1000` dias.

## <a name="result-code"></a>Código de resultado

Código de resultado de uma chamada de dependência. Os exemplos são código de erro SQL e código de status HTTP.

## <a name="success"></a>Êxito

Indicação de chamada bem-sucedida ou malsucedida.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Passos seguintes

- Configure o acompanhamento de dependência para [.net](../../azure-monitor/app/asp-net-dependencies.md).
- Configure o acompanhamento de dependência para [Java](../../azure-monitor/app/java-agent.md).
- [Gravar telemetria de dependência personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
