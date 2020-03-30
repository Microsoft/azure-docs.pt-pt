---
title: Modelo de dados de dependência de insights de aplicação do Monitor Azure Monitor
description: Modelo de dados de Insights de Aplicação para telemetria de dependência
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671924"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependência: Modelo de dados de Insights de Aplicação

A Telemetria de Dependência (em Insights de [Aplicação)](../../azure-monitor/app/app-insights-overview.md)representa uma interação do componente monitorizado com um componente remoto como o SQL ou um ponto final HTTP.

## <a name="name"></a>Nome

Nome do comando iniciado com esta chamada de dependência. Baixo valor de cardeal. Exemplos são nome de procedimento armazenado e modelo de caminho URL.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Utilizado para a correlação com o item de telemetria de pedido correspondente a esta chamada de dependência. Para mais informações, consulte a [página de correlação.](../../azure-monitor/app/correlation.md)

## <a name="data"></a>Dados

Comando iniciado por esta chamada de dependência. Exemplos são a declaração SQL e URL HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Tipo

Nome do tipo dependência. Baixo valor de cardeal para agrupamento lógico de dependências e interpretação de outros campos como commandName e resultadoCódigo. Exemplos são SQL, Mesa Azure e HTTP.

## <a name="target"></a>Destino

Local alvo de uma chamada de dependência. Exemplos são o nome do servidor, endereço de anfitrião. Para mais informações, consulte a [página de correlação.](../../azure-monitor/app/correlation.md)

## <a name="duration"></a>Duração

Duração do `DD.HH:MM:SS.MMMMMM`pedido em formato: . Deve ser `1000` menos de dias.

## <a name="result-code"></a>Código do resultado

Código de resultadode uma chamada de dependência. Exemplos são código de erro SQL e código de estado HTTP.

## <a name="success"></a>Êxito

Indicação de chamada bem sucedida ou mal sucedida.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Passos seguintes

- Configurar o rastreio da dependência para [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Cria o rastreio de dependência para [o Java.](../../azure-monitor/app/java-agent.md)
- [Escreva telemetria de dependência personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Consulte o modelo de [dados](data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
