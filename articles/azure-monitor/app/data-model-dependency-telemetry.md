---
title: Modelo de dados de dependência de insights de aplicação do monitor Azure
description: Modelo de dados de Insights de Aplicação para telemetria de dependência
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0f9fc96479569c3411024068ed614d422035ab17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87315976"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria de dependência: Modelo de dados de Insights de Aplicação

A Telemetria de Dependência (in [Application Insights)](./app-insights-overview.md)representa uma interação do componente monitorizado com um componente remoto, como o SQL ou um ponto final HTTP.

## <a name="name"></a>Name

Nome do comando iniciado com esta chamada de dependência. Baixo valor de cardinalidade. Exemplos são o nome do procedimento armazenado e o modelo do caminho url.

## <a name="id"></a>ID

Identificador de uma instância de chamada de dependência. Utilizado para a correlação com o item de telemetria de pedido correspondente a esta chamada de dependência. Para mais informações, consulte a página [de correlação.](./correlation.md)

## <a name="data"></a>Dados

Comando iniciado por esta chamada de dependência. Exemplos são a declaração SQL e URL HTTP com todos os parâmetros de consulta.

## <a name="type"></a>Tipo

Nome do tipo dependência. Baixo valor cardinalício para agrupamento lógico de dependências e interpretação de outros campos como o nome de comando e resultados Código. Exemplos são SQL, Azure table e HTTP.

## <a name="target"></a>Destino

Local alvo de uma chamada de dependência. Exemplos são o nome do servidor, o endereço do anfitrião. Para mais informações, consulte a página [de correlação.](./correlation.md)

## <a name="duration"></a>Duração

Duração do pedido no formato: `DD.HH:MM:SS.MMMMMM` . Deve ser menos que `1000` dias.

## <a name="result-code"></a>Código do resultado

Código de resultado de uma chamada de dependência. Os exemplos são o código de erro SQL e o código de estado HTTP.

## <a name="success"></a>Com êxito

Indicação de chamada bem sucedida ou mal sucedida.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Passos seguintes

- Configurar o rastreio de dependência para [.NET](./asp-net-dependencies.md).
- Configurar o rastreio de dependência para [Java.](./java-agent.md)
- [Escrever telemetria de dependência personalizada](./api-custom-events-metrics.md#trackdependency)
- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

