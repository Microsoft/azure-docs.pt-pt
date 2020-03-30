---
title: Modelo de dados de telemetria da aplicação Azure Insights Microsoft Docs
description: Visão geral do modelo de dados de Insights de Aplicação
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 94013a12e1cf48a8007fce2547c200d82a657b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671882"
---
# <a name="application-insights-telemetry-data-model"></a>Modelo de dados de telemetria Insights de aplicação

[O Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia telemetria da sua aplicação web para o portal Azure, para que possa analisar o desempenho e o uso da sua aplicação. O modelo de telemetria é padronizado para que seja possível criar uma plataforma e uma monitorização independente da linguagem. 

Os dados recolhidos pelos modelos Application Insights este padrão típico de execução de aplicações:

![Modelo de aplicação de insights de aplicação](./media/data-model/application-insights-data-model.png)

Os seguintes tipos de telemetria são utilizados para monitorizar a execução da sua aplicação. Os três tipos seguintes são normalmente recolhidos automaticamente pelo SDK de Insights de Aplicação a partir do quadro de aplicação web:

* [**Pedido**](data-model-request-telemetry.md) - Gerado para registar um pedido recebido pela sua app. Por exemplo, o Web SDK application Insights gera automaticamente um item de telemetria Request para cada pedido HTTP que a sua aplicação web recebe. 

    Uma **Operação** são os fios de execução que processa um pedido. Também pode [escrever código](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) para monitorizar outros tipos de funcionamento, como um "despertar" num trabalho web ou função que processa periodicamente os dados.  Cada operação tem identificação. Este ID que pode ser usado para [agrupar](../../azure-monitor/app/correlation.md) toda a telemetria gerada enquanto a sua aplicação está processando o pedido. Cada operação tem sucesso ou falha, e tem uma duração de tempo.
* [**Exceção**](data-model-exception-telemetry.md) - Normalmente representa uma exceção que faz com que uma operação falhe.
* [**Dependência**](data-model-dependency-telemetry.md) - Representa uma chamada da sua app para um serviço ou armazenamento externo, como uma API REST ou SQL. Em ASP.NET, as chamadas de dependência `System.Data`para sQL são definidas por . As chamadas para os `System.Net`pontos finais HTTP são definidas por . 

Os Insights de Aplicação fornecem três tipos de dados adicionais para telemetria personalizada:

* [Trace](data-model-trace-telemetry.md) - utilizado diretamente, ou através de um adaptador para implementar o registo de `Log4Net` `System.Diagnostics`diagnósticos utilizando uma estrutura de instrumentação que lhe é familiar, como ou .
* [Evento](data-model-event-telemetry.md) - normalmente usado para capturar a interação do utilizador com o seu serviço, para analisar padrões de utilização.
* [Métrica](data-model-metric-telemetry.md) - usada para relatar medições periódicas de escalar.

Cada item de telemetria pode definir as [informações](data-model-context.md) de contexto como versão de aplicação ou id de sessão de utilizador. O contexto é um conjunto de campos fortemente digitados que desbloqueiacertos cenários. Quando a versão da aplicação é devidamente inicializada, os Insights de Aplicação podem detetar novos padrões no comportamento da aplicação correlacionados com a redistribuição. O id da sessão pode ser usado para calcular a paralisação ou um impacto de problema nos utilizadores. Calcular valores distintos de id da sessão para determinada dependência falhada, traço de erro ou exceção crítica dá uma boa compreensão de um impacto.

O modelo de telemetria Application Insights define uma forma de [correlacionar](../../azure-monitor/app/correlation.md) a telemetria com o funcionamento do qual faz parte. Por exemplo, um pedido pode fazer uma base de dados SQL e informações de diagnóstico gravadas. Pode definir o contexto de correlação para os itens de telemetria que o ligam à telemetria do pedido.

## <a name="schema-improvements"></a>Melhorias de Schema

O modelo de dados da Application Insights é uma forma simples e básica, mas poderosa, de modelar a telemetria da sua aplicação. Esforçamo-nos por manter o modelo simples e fino para apoiar cenários essenciais e permitir estender o esquema para uso avançado.

Para relatar problemas de modelo de dados ou esquemas e sugestões usam o repositório GitHub [ApplicationInsights-Home.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="next-steps"></a>Passos seguintes

- [Escreva telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md)
- Aprenda a estender e filtrar a [telemetria.](../../azure-monitor/app/api-filtering-sampling.md)
- Utilize [amostras](../../azure-monitor/app/sampling.md) para minimizar a quantidade de telemetria com base no modelo de dados.
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
