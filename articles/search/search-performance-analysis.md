---
title: Analisar o desempenho
titleSuffix: Azure Cognitive Search
description: TBD
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582382"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Analisar desempenho em Azure Cognitive Search

Este artigo descreve as ferramentas, comportamentos e abordagens para analisar a consulta e indexar o desempenho na Pesquisa Cognitiva.

## <a name="develop-baseline-numbers"></a>Desenvolver números de base

Em qualquer grande implementação, é fundamental fazer um teste de benchmarking de desempenho do seu serviço de Pesquisa Cognitiva antes de o lançar na produção. Deve testar tanto a carga de consulta de pesquisa que espera, mas também as cargas de trabalho de ingestão de dados esperadas (se possível, executá-las simultaneamente). Ter números de referência ajuda a validar o [nível](search-sku-tier.md)de pesquisa adequado, [a configuração do serviço](search-capacity-planning.md)e a [latência](search-performance-analysis.md#average-query-latency)de consulta esperada .

Para desenvolver referências, recomendamos a ferramenta [azure-search-performance-test (GitHub).](https://github.com/Azure-Samples/azure-search-performance-testing)

Para isolar os efeitos de uma arquitetura de serviço distribuída, tente testar as configurações de serviço de uma réplica e uma divisória.

> [!NOTE]
> Para os níveis otimizados de armazenamento (L1 e L2), deverá esperar uma produção de consulta mais baixa e uma latência mais elevada do que os níveis Standard.
>

## <a name="use-diagnostic-logging"></a>Utilize registos de diagnóstico

A ferramenta mais importante que um administrador tem no diagnóstico de potenciais problemas de desempenho é [o registo de diagnósticos](search-monitor-logs.md) que recolhe dados operacionais e métricas sobre o seu serviço de pesquisa. A sessão de diagnóstico é ativada através do [Azure Monitor](../azure-monitor/overview.md). Existem custos associados à utilização do Azure Monitor e ao armazenamento de dados, mas se o ativar para o seu serviço, pode ser fundamental na investigação de problemas de desempenho.

Existem múltiplas oportunidades para a latência ocorrer, seja durante uma transferência de rede, ou durante o processamento de conteúdos na camada de serviços de aplicações, ou em um serviço de pesquisa. Um dos principais benefícios do registo de diagnóstico é que as atividades são registadas na perspetiva do serviço de pesquisa, o que significa que o registo pode ajudá-lo a determinar se os problemas de desempenho se devem a problemas com a consulta ou indexação, ou algum outro ponto de falha.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Cadeia de eventos registados" border="true":::

O registo de diagnósticos dá-lhe opções para armazenar informações registadas. Recomendamos a utilização [do Log Analytics](../azure-monitor/logs/log-analytics-overview.md) para que possa executar consultas avançadas de Kusto contra os dados para responder a muitas perguntas sobre o uso e desempenho. 

Nas páginas do portal do seu serviço de pesquisa, pode ativar o registo através das **definições de Diagnóstico** e, em seguida, emitir consultas de Kusto contra Log Analytics, escolhendo **Logs**. Para obter mais informações sobre a configuração, consulte [recolher e analisar dados de registo.](search-monitor-logs.md)

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Opções de menu de registo" border="true":::

## <a name="throttling-behaviors"></a>Comportamentos de estrangulamento

O estrangulamento ocorre quando o serviço de pesquisa atingiu o limite do que pode lidar numa perspetiva de recurso. O estrangulamento pode ocorrer durante consultas ou indexação. Do lado do cliente, uma chamada da API resulta numa resposta HTTP 503 quando foi estrangulada. Durante a indexação, existe também a possibilidade de receber uma resposta HTTP 207, o que indica que um ou mais itens não se indexaram. Este erro é um indicador de que o serviço de pesquisa está a aproximar-se da capacidade. 

Como regra geral, é melhor quantificar a quantidade de estrangulamento que se vê. Por exemplo, se uma consulta de pesquisa de 500.000 é estrangulada, pode não ser um problema tão grande. No entanto, se uma grande percentagem de consultas for estrangulada durante um período, isso seria uma preocupação maior. Ao olhar para o estrangulamento ao longo de um período, também ajuda a identificar os prazos onde o estrangulamento pode ocorrer mais provavelmente e ajudá-lo a decidir como melhor acomodar isso.

Uma solução simples para a maioria dos problemas de estrangulamento é lançar mais recursos no serviço de pesquisa (normalmente réplicas para estrangulamento baseado em consultas, ou divisórias para estrangulamento baseado em indexação). No entanto, o aumento de réplicas ou divisórias adiciona custo, e é por isso que é importante saber a razão pela qual o estrangulamento está a ocorrer. Investigar as condições que causam estrangulamento será explicado nas próximas secções. 

Abaixo está um exemplo de uma consulta kusto que pode identificar a desagregação de respostas HTTP do serviço de pesquisa que tem sido carregado. Ao consultar um período de 7 dias, o gráfico de barras renderizado mostra que uma percentagem relativamente grande das consultas de pesquisa foi acelerada, em comparação com o número de respostas bem sucedidas (200).

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Gráfico de barras de contagens de erros http" border="true":::

Examinar o estrangulamento durante um período de tempo específico pode ajudá-lo a identificar os tempos em que o estrangulamento pode ocorrer com mais frequência. No exemplo abaixo, um gráfico de séries de tempo é usado para mostrar o número de consultas estranguladas que ocorreram ao longo de um período de tempo especificado. Neste caso, foram realizadas as consultas de estrangulamento correlacionadas com os tempos em que o benchmarking de desempenho foi realizado.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="Gráfico de linha de consultas estranguladas" border="true":::

## <a name="measure-individual-queries"></a>Medir consultas individuais

Em alguns casos, pode ser útil testar consultas individuais para ver como estão a funcionar. Para isso, é importante ser capaz de ver quanto tempo o serviço de pesquisa leva para completar o trabalho, bem como quanto tempo demora a fazer o pedido de ida e volta do cliente e de volta ao cliente. Os registos de diagnóstico podem ser usados para procurar operações individuais, mas pode ser mais fácil fazer isto a partir de uma ferramenta de cliente, como o Carteiro.

No exemplo abaixo, foi executada uma consulta de pesquisa baseada em REST. A Pesquisa Cognitiva inclui em todas as respostas o número de milissegundos necessários para completar a consulta, visível no separador Cabeçalhos, em "tempo decorrido". Ao lado do Status no topo da resposta, você encontrará a duração da viagem de ida e volta. neste caso, 418 milissegundos. Na secção de resultados, foi escolhido o separador "Cabeçalhos". Utilizando estes dois valores destacados com uma caixa vermelha na imagem abaixo, vemos que o serviço de pesquisa demorou 21 ms para completar a consulta de pesquisa e todo o pedido de ida e volta do cliente levou 125 ms. Subtraindo estes dois números podemos determinar que demorou 104 ms mais tempo para transmitir a consulta de pesquisa para o serviço de pesquisa e transferir os resultados de pesquisa de volta para o cliente.

Isto pode ser extremamente útil para determinar se pode haver latências de rede ou outros fatores que impactam o desempenho da consulta.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Métricas de duração da consulta" border="true":::

## <a name="query-rates"></a>Taxas de consulta

Uma das razões potenciais para o seu serviço de pesquisa para acelerar os pedidos deve-se ao número absoluto de consultas que estão a ser realizadas onde o volume é capturado como consultas por segundo (QPS) ou consultas por minuto (QPM). À medida que o seu serviço de pesquisa recebe mais QPS, normalmente demorará mais tempo a responder a essas consultas até que não possa continuar a funcionar, como o qual irá enviar de volta uma resposta de estrangulamento de 503 HTTP. 

A consulta kusto seguinte mostra o volume de consulta medido em QPM, juntamente com a duração média de uma consulta em milissegundos (AvgDurationMS) e o número médio de documentos (AvgDocCountReturned) devolvidos em cada um.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="Gráfico mostrando consultas por minuto" border="true":::

> [!TIP]
> Para revelar os dados por trás deste gráfico, remova a linha `| render timechart` e, em seguida, reexecute a consulta.

## <a name="impact-of-indexing-on-queries"></a>Impacto da indexação nas consultas

Um fator importante a ter em conta quando se olha para o desempenho é que a indexação utiliza os mesmos recursos que as consultas de pesquisa. Se estiver a indexar uma grande quantidade de conteúdos, pode esperar ver a latência crescer à medida que o serviço tenta acomodar ambas as cargas de trabalho.

Se as consultas estão a abrandar, veja o tempo da atividade de indexação para ver se coincide com a degradação da consulta. Por exemplo, talvez um indexante esteja a executar um trabalho diário ou de hora que se correlaciona com o desempenho diminuído das consultas de pesquisa. 

Esta secção fornece um conjunto de consultas que podem ajudá-lo a visualizar as taxas de pesquisa e indexação. Para estes exemplos, o intervalo de tempo é definido na consulta. Certifique-se de que indica **set em consulta** ao executar as consultas no portal Azure.

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Definir intervalos de tempo na ferramenta de consulta" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Latência média da consulta

Na consulta abaixo, um intervalo de 1 minuto é usado para mostrar a latência média das consultas de pesquisa. Do gráfico podemos ver que a latência média foi baixa até às 17h45 e durou até às 17h53.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="Gráfico mostrando latência média de consulta" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Consultas médias por minuto (QPM)

A seguinte consulta permite-nos olhar para o número médio de consultas por minuto para garantir que não houve algum tipo de pico nos pedidos de pesquisa que possam ter afetado a latência. Do gráfico podemos ver que há alguma variação, mas nada indica um pico na contagem de pedidos.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Gráfico mostrando consultas médias por minuto" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Operações de Indexação por Minuto (OPM)

Aqui vamos olhar para o número de operações de indexação por minuto. Do gráfico podemos ver que uma grande quantidade de dados foi indexado às 17h42 e terminou às 17h50. Esta indexação começou 3 minutos antes das consultas de pesquisa começarem a ficar latentes e terminou 3 minutos antes das consultas de pesquisa deixarem de ser latentes.

Desta forma podemos ver que o serviço de pesquisa demorou cerca de 3 minutos a estar ocupado o suficiente a partir da indexação para começar a afetar a latência da consulta de pesquisa. Também podemos ver que após a indexação concluída, demorou mais 3 minutos para o serviço de pesquisa completar todo o trabalho a partir do conteúdo recentemente indexado, até que as consultas de pesquisa começam a deixar de ser latentes.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Gráfico mostrando operações de indexação por minuto" border="true":::

## <a name="background-service-processing"></a>Processamento de serviço de fundo

Não é incomum ver picos periódicos na consulta ou indexação da latência. Os picos podem ocorrer em resposta à indexação ou altas taxas de consulta, mas também podem ocorrer durante as operações de fusão. Os índices de pesquisa são armazenados em pedaços ou fragmentos. Periodicamente, o sistema funde fragmentos menores em grandes fragmentos, o que pode ajudar a otimizar o desempenho do serviço. Este processo de fusão também limpa documentos que foram previamente marcados para a eliminação do índice, resultando na recuperação do espaço de armazenamento. 

A fusão de fragmentos é rápida, mas também intensiva em recursos e, portanto, tem o potencial de degradar o desempenho do serviço. Por esta razão, se vires pequenas explosões de latência de consulta, e essas explosões coincidem com as recentes alterações ao conteúdo indexado, é provável que atribua essa latência a operações de fusão de fragmentos. 

## <a name="next-steps"></a>Passos seguintes

Reveja estes artigos adicionais relacionados com a análise do desempenho do serviço.

+ [Sugestões de desempenho](search-performance-tips.md)
+ [Escolha um nível de serviço](search-sku-tier.md)
+ [Gerir capacidade](search-capacity-planning.md)
