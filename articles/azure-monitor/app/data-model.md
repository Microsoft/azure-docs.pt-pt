---
title: Azure Application Insights Telemetria Data Model | Microsoft Docs
description: Visão geral do modelo de dados do Application Insights
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: fd5848eb44ecd32612943662b5a4fd0a5091cd9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91461296"
---
# <a name="application-insights-telemetry-data-model"></a>Modelo de dados de telemetria de Insights de Aplicação

[O Azure Application Insights](./app-insights-overview.md) envia telemetria da sua aplicação web para o portal Azure, para que possa analisar o desempenho e utilização da sua aplicação. O modelo de telemetria é padronizado de modo a que seja possível criar plataforma e monitorização independente da linguagem. 

Dados recolhidos pela Application Insights modelam este padrão típico de execução de aplicações:

![Modelo de aplicação insights](./media/data-model/application-insights-data-model.png)

Os seguintes tipos de telemetria são utilizados para monitorizar a execução da sua aplicação. Os três tipos seguintes são normalmente recolhidos automaticamente pela Application Insights SDK a partir do quadro de aplicações web:

* [**Pedido**](data-model-request-telemetry.md) - Gerado para registar um pedido recebido pela sua app. Por exemplo, a Web SDK do Application Insights gera automaticamente um item de telemetria Request para cada pedido HTTP que a sua aplicação web recebe. 

    Uma **Operação** são os fios da execução que processa um pedido. Também pode [escrever código](./api-custom-events-metrics.md#trackrequest) para monitorizar outros tipos de operação, como um "despertar" numa função web ou uma função que processa periodicamente os dados.  Cada operação tem uma identificação. Este ID que pode ser usado para [agrupar](./correlation.md) toda a telemetria gerada enquanto a sua aplicação está a processar o pedido. Cada operação tem sucesso ou falha, e tem uma duração de tempo.
* [**Exceção**](data-model-exception-telemetry.md) - Normalmente representa uma exceção que faz com que uma operação falhe.
* [**Dependência**](data-model-dependency-telemetry.md) - Representa uma chamada da sua app para um serviço ou armazenamento externo, como um REST API ou SQL. Em ASP.NET, as chamadas de dependência para SQL são definidas por `System.Data` . As chamadas para pontos finais HTTP são definidas por `System.Net` . 

O Application Insights fornece três tipos de dados adicionais para telemetria personalizada:

* [Trace](data-model-trace-telemetry.md) - usado diretamente, ou através de um adaptador para implementar o registo de diagnósticos utilizando uma estrutura de instrumentação que lhe é familiar, como `Log4Net` ou . `System.Diagnostics` .
* [Evento](data-model-event-telemetry.md) - normalmente usado para capturar a interação do utilizador com o seu serviço, para analisar padrões de utilização.
* [Métrica](data-model-metric-telemetry.md) - usada para reportar medições periódicas de escalar.

Cada item de telemetria pode definir as [informações](data-model-context.md) de contexto como versão de aplicação ou id de sessão de utilizador. O contexto é um conjunto de campos fortemente dactilografados que desbloqueiam certos cenários. Quando a versão da aplicação é devidamente inicializada, o Application Insights pode detetar novos padrões no comportamento da aplicação correlacionado com a redistribuição. O id de sessão pode ser usado para calcular a paralisação ou um impacto em questão nos utilizadores. Calcular valores de identificação de sessão distintos para determinada dependência falhada, traço de erro ou exceção crítica dá uma boa compreensão de um impacto.

O modelo de telemetria Application Insights define uma forma de [correlacionar](./correlation.md) a telemetria com o funcionamento do qual faz parte. Por exemplo, um pedido pode fazer uma base de dados SQL e informações de diagnóstico gravadas. Pode definir o contexto de correlação para os itens de telemetria que o ligam à telemetria do pedido.

## <a name="schema-improvements"></a>Melhorias do esquema

O modelo de dados Application Insights é uma forma simples e básica mas poderosa de modelar a telemetria da aplicação. Esforçamo-nos por manter o modelo simples e magro para suportar cenários essenciais e permitir estender o esquema para uso avançado.

[Para reportar problemas e sugestões de modelo de dados ou esquemas, utilize o nosso repositório GitHub.](https://github.com/microsoft/ApplicationInsights-dotnet/issues/new/choose)

## <a name="next-steps"></a>Passos seguintes

- [Escrever telemetria personalizada](./api-custom-events-metrics.md)
- Saiba como [estender e filtrar a telemetria.](./api-filtering-sampling.md)
- Utilize [amostras para](./sampling.md) minimizar a quantidade de telemetria com base no modelo de dados.
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

