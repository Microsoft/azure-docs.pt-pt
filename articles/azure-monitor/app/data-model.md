---
title: Modelo de dados de telemetria do Aplicativo Azure insights | Microsoft Docs
description: Visão geral do modelo de dados do Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.subservice: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: b14ce5cc83dcbbeef2379d21027d4bca337fd9fd
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376029"
---
# <a name="application-insights-telemetry-data-model"></a>Modelo de dados de telemetria Application Insights

[Aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md) envia telemetria do seu aplicativo Web para o portal do Azure, para que você possa analisar o desempenho e o uso do seu aplicativo. O modelo de telemetria é padronizado para que seja possível criar a plataforma e o monitoramento independente de linguagem. 

Os dados coletados por Application Insights modelam este padrão de execução de aplicativo típico:

![Modelo de aplicativo Application Insights](./media/data-model/application-insights-data-model.png)

Os seguintes tipos de telemetria são usados para monitorar a execução do seu aplicativo. Os três tipos a seguir geralmente são coletados automaticamente pelo SDK do Application Insights da estrutura do aplicativo Web:

* [**Solicitação**](data-model-request-telemetry.md) -gerada para registrar uma solicitação recebida pelo seu aplicativo. Por exemplo, o SDK da Web do Application Insights gera automaticamente um item de telemetria de solicitação para cada solicitação HTTP que seu aplicativo Web recebe. 

    Uma **operação** é o threads de execução que processa uma solicitação. Você também pode [escrever código](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) para monitorar outros tipos de operação, como uma "ativação" em um trabalho da Web ou função que processa dados periodicamente.  Cada operação tem uma ID. Essa ID que pode ser usada para [Agrupar](../../azure-monitor/app/correlation.md) toda a telemetria gerada enquanto o aplicativo está processando a solicitação. Cada operação é bem-sucedida ou falha e tem uma duração de tempo.
* [**Exceção**](data-model-exception-telemetry.md) – normalmente representa uma exceção que causa a falha de uma operação.
* [**Dependência**](data-model-dependency-telemetry.md) – representa uma chamada de seu aplicativo para um serviço externo ou armazenamento, como uma API REST ou SQL. No ASP.NET, as chamadas de dependência para SQL são definidas por `System.Data`. Chamadas para pontos de extremidade HTTP são definidas por `System.Net`. 

Application Insights fornece três tipos de dados adicionais para telemetria personalizada:

* [Trace](data-model-trace-telemetry.md) – usado diretamente ou por meio de um adaptador para implementar o log de diagnóstico usando uma estrutura de instrumentação familiar para você, como `Log4Net` ou `System.Diagnostics`.
* [Evento](data-model-event-telemetry.md) – normalmente usado para capturar a interação do usuário com seu serviço, para analisar os padrões de uso.
* [Métrica](data-model-metric-telemetry.md) -usada para relatar medidas escalares periódicas.

Cada item de telemetria pode definir as [informações de contexto](data-model-context.md) , como a versão do aplicativo ou a ID de sessão do usuário. O contexto é um conjunto de campos fortemente tipados que desbloqueia determinados cenários. Quando a versão do aplicativo é inicializada corretamente, Application Insights pode detectar novos padrões no comportamento do aplicativo correlacionados com a reimplantação. A ID de sessão pode ser usada para calcular a interrupção ou um impacto de problema nos usuários. Calcular a contagem distinta de valores de ID de sessão para determinada dependência com falha, rastreamento de erro ou exceção crítica fornece uma boa compreensão de um impacto.

Application Insights modelo de telemetria define uma maneira de [correlacionar](../../azure-monitor/app/correlation.md) a telemetria com a operação da qual ela faz parte. Por exemplo, uma solicitação pode fazer uma chamada de banco de dados SQL e informações de diagnóstico gravadas. Você pode definir o contexto de correlação para os itens de telemetria que o vinculam de volta à telemetria de solicitação.

## <a name="schema-improvements"></a>Aprimoramentos de esquema

Application Insights modelo de dados é uma maneira simples e básica, mas eficiente, de modelar a telemetria do aplicativo. Nós nos empenhamos em manter o modelo simples e fino para dar suporte a cenários essenciais e permitir que o estenda o esquema para uso avançado.

Para relatar problemas de modelo de dados ou de esquema e sugestões, use o repositório do GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/issues) .

## <a name="next-steps"></a>Passos seguintes

- [Gravar telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md)
- Saiba como [estender e filtrar a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Use [amostragem](../../azure-monitor/app/sampling.md) para minimizar a quantidade de telemetria com base no modelo de dados.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
