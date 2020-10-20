---
title: Analisar telemetria de funções Azure em Insights de Aplicação
description: Saiba como visualizar e consultar dados de telemetria Azure Functions recolhidos e armazenados em Azure Application Insights.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperfq2
ms.openlocfilehash: 9091d59cbc3a4cf841e28505531c89c83e705c46
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217308"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Analisar telemetria de funções Azure em Insights de Aplicação 

As Funções Azure integram-se com o Application Insights para melhor monitorizar as suas aplicações de função. O Application Insights recolhe dados de telemetria gerados pela sua aplicação de função, incluindo informações que a sua aplicação escreve para registos. A integração do Application Insights é normalmente ativada quando a sua aplicação de função é criada. Se a sua aplicação de função não tiver o conjunto de teclas de instrumentação, primeiro deve [ativar a integração do Application Insights](configure-monitoring.md#enable-application-insights-integration). 

Por predefinição, os dados recolhidos da sua aplicação de função são armazenados no Application Insights. No [portal Azure,](https://portal.azure.com)o Application Insights fornece um vasto conjunto de visualizações dos seus dados de telemetria. Pode perfurar registos de erros e eventos de consulta e métricas. Este artigo fornece exemplos básicos de como visualizar e consultar os seus dados recolhidos. Para saber mais sobre como explorar os dados da sua aplicação de funções em Application Insights, consulte [o que é Insights de Aplicação?](../azure-monitor/app/app-insights-overview.md) 

Para saber mais sobre a retenção de dados e os potenciais custos de armazenamento, consulte [a recolha, retenção e armazenamento de Dados em Application Insights](../azure-monitor/app/data-retention-privacy.md).   

## <a name="viewing-telemetry-in-monitor-tab"></a>Visualização da telemetria no separador Monitor

Com [a integração de Application Insights ativada,](configure-monitoring.md#enable-application-insights-integration)pode ver os dados da telemetria no **separador Monitor.**

1. Na página da aplicação de funções, selecione uma função que tenha sido executada pelo menos uma vez após a configuração do Application Insights. Em seguida, **selecione Monitor** a partir do painel esquerdo. Selecione **Refresh** periodicamente, até aparecer a lista de invocações de funções.

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Pode levar até cinco minutos para a lista aparecer enquanto o cliente da telemetria ementa dados para transmissão para o servidor. O atraso não se aplica ao [Live Metrics Stream](../azure-monitor/app/live-stream.md). Este serviço liga-se ao anfitrião Funções quando carrega a página, para que os registos sejam transmitidos diretamente para a página.

1. Para ver os registos para uma invocação de função específica, selecione a ligação da coluna **Data (UTC)** para essa invocação. A saída de registo para essa invocação aparece numa nova página.

   ![Detalhes da invocação](media/functions-monitoring/invocation-details-ai.png)

1. Escolha Executar em Insights de **Aplicação** para visualizar a origem da consulta que recupera os dados de registo do Azure Monitor no Registo Azure. Se esta for a sua primeira utilização do Azure Log Analytics na sua subscrição, é-lhe pedido que o ative.

1. Depois de ativar o Log Analytics, é apresentada a seguinte consulta. Pode ver que os resultados da consulta estão limitados aos últimos 30 dias `where timestamp > ago(30d)` (), e os resultados não mostram mais de 20 linhas `take 20` ( ). Em contraste, a lista de detalhes de invocação para a sua função é para os últimos 30 dias sem limite.

   ![Lista de invocação de Application Insights Analytics](media/functions-monitoring/ai-analytics-invocation-list.png)

Para mais informações, consulte os dados de [telemetria da Consulta](#query-telemetry-data) mais tarde neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Ver telemetria em Insights de Aplicação

Para abrir insights de aplicação a partir de uma aplicação de função no [portal Azure:](https://portal.azure.com)

1. Navegue pela aplicação de função no portal.

1. Selecione **Insights de Aplicação** em **Definições** na página esquerda. 

1. Se esta for a sua primeira utilização de Application Insights com a sua subscrição, será solicitado para o ativar. Para isso, selecione **Ligue os Insights de Aplicação**e, em seguida, selecione **Aplicar** na página seguinte.

![Abrir informações sobre aplicações a partir da página geral da aplicação de funções](media/functions-monitoring/ai-link.png)

Para obter informações sobre como utilizar o Application Insights, consulte a [documentação do Application Insights](/azure/application-insights/). Esta secção mostra alguns exemplos de como visualizar dados em Insights de Aplicação. Se já está familiarizado com o Application Insights, pode ir diretamente às [secções sobre como configurar e personalizar os dados de telemetria](configure-monitoring.md#configure-log-levels).

![Separador de visão geral de insights de aplicação](media/functions-monitoring/metrics-explorer.png)

As seguintes áreas de Insights de Aplicação podem ser úteis na avaliação do comportamento, desempenho e erros nas suas funções:

| Investigar | Descrição |
| ---- | ----------- |
| **[Falhas](../azure-monitor/app/asp-net-exceptions.md)** |  Crie gráficos e alertas com base em falhas de função e exceções ao servidor. O **Nome da Operação** é o nome da função. Falhas nas dependências não são mostradas a menos que implemente telemetria personalizada para dependências. |
| **[Desempenho](../azure-monitor/app/performance-counters.md)** | Analise os problemas de desempenho visualizando a utilização de recursos e a produção por **instâncias de função cloud**. Estes dados de desempenho podem ser úteis para depurar cenários onde as funções estão a atrapalhar os seus recursos subjacentes. |
| **[Métricas](../azure-monitor/platform/metrics-charts.md)** | Crie gráficos e alertas baseados em métricas. As métricas incluem o número de invocações de funções, tempo de execução e taxas de sucesso. |
| **[Métricas Vivas    ](../azure-monitor/app/live-stream.md)** | Veja os dados das métricas como são criados em tempo real. |

## <a name="query-telemetry-data"></a>Dados de telemetria de consulta

[Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md) dá-lhe acesso a todos os dados de telemetria sob a forma de tabelas numa base de dados. A Analytics fornece uma linguagem de consulta para extrair, manipular e visualizar os dados. 

Escolha **Registos** para explorar ou consultar eventos registados.

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Aqui está um exemplo de consulta que mostra a distribuição de pedidos por trabalhador nos últimos 30 minutos.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

As tabelas disponíveis são mostradas no **separador Schema** à esquerda. Pode encontrar dados gerados por invocações de função nas seguintes tabelas:

| Tabela | Descrição |
| ----- | ----------- |
| **vestígios** | Registos criados pelo tempo de execução e vestígios do seu código de função. |
| **pedidos** | Um pedido para cada invocação de função. |
| **exceções** | Quaisquer exceções lançadas pelo tempo de execução. |
| **costumesMetricos** | A contagem de invocações bem sucedidas e falhadas, taxa de sucesso e duração. |
| **costumesEvents** | Eventos rastreados pelo tempo de execução, por exemplo: PEDIDOS HTTP que desencadeiam uma função. |
| **performanceCounters** | Informações sobre o desempenho dos servidores em que as funções estão a funcionar. |

As outras tabelas são para testes de disponibilidade, e telemetria de cliente e navegador. Pode implementar telemetria personalizada para adicionar dados a eles.

Dentro de cada tabela, alguns dos dados específicos das Funções estão num `customDimensions` campo.  Por exemplo, a seguinte consulta recupera todos os vestígios que têm nível de registo `Error` .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

O tempo de execução fornece os `customDimensions.LogLevel` `customDimensions.Category` campos e campos. Pode fornecer campos adicionais em registos que escreve no seu código de função. Para um exemplo em C#, consulte [o registo estruturado](functions-dotnet-class-library.md#structured-logging) no guia de desenvolvimento da biblioteca da classe .NET.

## <a name="consumption-plan-specific-metrics"></a>Métricas específicas do plano de consumo

Ao executar um [plano de consumo,](functions-scale.md#consumption-plan)o *custo* de execução de uma única função é medido em *GB-segundos*. O custo de execução é calculado combinando o seu uso de memória com o seu tempo de execução. Para saber mais, consulte [os custos do plano de consumo estimado.](functions-consumption-costs.md)

As seguintes consultas de telemetria são específicas para métricas que impactam o custo de funcionamento das funções no plano de Consumo.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a monitorização das Funções Azure:

+ [Monitorizar as Funções do Azure](functions-monitoring.md)
+ [Como configurar a monitorização para as funções do Azure](configure-monitoring.md)

