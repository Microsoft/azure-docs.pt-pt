---
title: Azure Application Insights métricas baseadas em registos | Microsoft Docs
description: Este artigo lista métricas de Azure Application Insights com agregações e dimensões suportadas. Os detalhes sobre métricas baseadas em log incluem as declarações de consulta subjacentes de Kusto.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.openlocfilehash: bae7980137c1da5e1755450863af4f9029d6ed62
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583467"
---
# <a name="application-insights-log-based-metrics"></a>Métricas baseadas em registos de insights de aplicação

As métricas baseadas em registos do Application Insights permitem analisar a saúde das suas aplicações monitorizadas, criar painéis de instrumentos poderosos e configurar alertas. Existem dois tipos de métricas:

* [As métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) por detrás da cena são traduzidas em [consultas de Kusto](/azure/kusto/query/) de eventos armazenados.
* [As métricas padrão](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) são armazenadas como séries temporizadas pré-agregadas.

Uma vez que *as métricas padrão* são pré-agregadas durante a recolha, têm um melhor desempenho no tempo de consulta. Isto faz deles uma escolha melhor para o dashboarding e em alerta em tempo real. As *métricas baseadas em log* têm mais dimensões, o que os torna a opção superior para análise de dados e diagnósticos ad-hoc. Utilize o [seletor de espaço](./metrics-getting-started.md#create-your-first-metric-chart) de nome para alternar entre métricas baseadas em registos e métricas padrão no explorador de [métricas](./metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretar e usar consultas deste artigo

Este artigo lista métricas com agregações e dimensões suportadas. Os detalhes sobre métricas baseadas em log incluem as declarações de consulta subjacentes de Kusto. Por conveniência, cada consulta utiliza predefinições para granularidade temporal, tipo de gráfico e, por vezes, dimensão de divisão que simplifica usando a consulta em Log Analytics sem qualquer necessidade de modificação.

Quando traça a mesma métrica no [explorador de métricas](./metrics-getting-started.md), não existem predefinições - a consulta é ajustada dinamicamente com base nas definições do seu gráfico:

- O **intervalo de tempo** selecionado é traduzido para um adicional *onde a hora marcada...* cláusula para escolher apenas os eventos a partir do intervalo de tempo selecionado. Por exemplo, um gráfico que mostra dados para as últimas 24 horas, a consulta inclui | onde o *relógio > (24 h)*.

- A **granularidade do tempo** selecionada é colocada no *resumo final... por bin (timetamp, [time grain])* cláusula.

- Quaisquer dimensões **de filtro** selecionadas são traduzidas em *cláusulas* adicionais.

- A dimensão do **gráfico split** selecionado é traduzida para uma propriedade de resumo extra. Por exemplo, se dividir o seu gráfico por *localização*, e traçar usando uma granularidade de 5 minutos, a cláusula *resumida* é resumida... *por bin (timetamp, 5 m), localização*.

> [!NOTE]
> Se é novo no idioma de consulta kusto, começa por copiar e colar declarações de Kusto no painel de consultas log analytics sem fazer modificações. Clique em **Executar** para ver o gráfico básico. À medida que começa a entender a sintaxe da linguagem de consulta, pode começar a fazer pequenas modificações e ver o impacto da sua mudança. Explorar os seus próprios dados é uma ótima maneira de começar a perceber a potência total do [Log Analytics](../logs/log-analytics-tutorial.md) e [do Azure Monitor.](../overview.md)

## <a name="availability-metrics"></a>Métricas de disponibilidade

As métricas na categoria Disponibilidade permitem-lhe ver a saúde da sua aplicação web como observado em pontos de todo o mundo. [Configure os testes de disponibilidade](../app/monitor-web-app-availability.md) para começar a utilizar quaisquer métricas desta categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilidade (disponibilidadeResults/disponibilidadepercentes)
A métrica *disponibilidade* mostra a percentagem dos testes web que não detetou quaisquer problemas. O valor mais baixo possível é 0, o que indica que todos os testes web falharam. O valor de 100 significa que todos os testes web passaram os critérios de validação.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Percentagem|Média|Localização de execução, nome do teste|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Duração do teste de disponibilidade (disponibilidadeResults/duração)

A métrica *da duração do teste de Disponibilidade* mostra quanto tempo levou para o teste web ser executado. Para os [testes web em várias etapas,](../app/availability-multistep.md)a métrica reflete o tempo total de execução de todas as etapas.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Milissegundos|Média, Min, Max|Localização de execução, nome de teste, resultado do teste

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testes de disponibilidade (disponibilidadeResults/contagem)

A métrica *dos testes de Disponibilidade* reflete a contagem dos testes web realizados pelo Azure Monitor.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|de palavras|de palavras|Localização de execução, nome de teste, resultado do teste|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Métricas do navegador

As métricas do navegador são recolhidas pela Aplicação Insights JavaScript SDK a partir de navegadores reais de utilizadores finais. Eles fornecem excelentes informações sobre a experiência dos seus utilizadores com a sua aplicação web. As métricas do navegador normalmente não são amostradas, o que significa que fornecem uma maior precisão dos números de utilização em comparação com métricas do lado do servidor que podem ser distorcidas por amostragem.

> [!NOTE]
> Para recolher as métricas do navegador, a sua aplicação deve ser instrumentada com o [App Insights JavaScript SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo de carregamento da página do navegador (browserTimings/totalDuration)

Tempo do pedido do utilizador até dom, folhas de estilo, scripts e imagens são carregados.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo de processamento do cliente (browserTiming/processingDuration)

Tempo entre receber o último byte de um documento até que o DOM seja carregado. Os pedidos de async ainda podem estar a ser processados.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo de ligação da rede de carga de página (browserTimings/networkDuration)

Tempo entre o pedido do utilizador e a ligação à rede. Inclui a procura de DNS e a ligação de transporte.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Tempo de resposta de receção (browserTimings/receiveDuration)

Tempo entre o primeiro e o último bytes, ou até a desconexão.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Enviar tempo de pedido (browserTimings/sendDuration)

Tempo entre a ligação de rede e receber o primeiro byte.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Métricas de falha

As métricas em Falhas mostram **problemas** com pedidos de processamento, chamadas de dependência e exceções lançadas.

### <a name="browser-exceptions-exceptionsbrowser"></a>Exceções do navegador (exceções/navegador)

Esta métrica reflete o número de exceções lançadas do seu código de aplicação em execução no navegador. Apenas as exceções que são rastreadas com uma ```trackException()``` chamada API de Insights de Aplicação estão incluídas na métrica.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|de palavras|de palavras|Nenhum|A versão baseada em log utiliza a agregação **de Soma**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Falhas nas chamadas de dependência (dependências/falhas)

O número de chamadas de dependência falhadas.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|de palavras|de palavras|Nenhum|A versão baseada em log utiliza a agregação **de Soma**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Exceções (exceções/contagem)

Sempre que regista uma exceção ao Application Insights, há uma chamada para o [método trackException do](../app/api-custom-events-metrics.md#trackexception) SDK. A métrica das Exceções mostra o número de exceções registadas.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|de palavras|de palavras|Nome de função em nuvem, instância de função em nuvem, tipo de dispositivo|A versão baseada em log utiliza a agregação **de Soma**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Pedidos falhados (pedidos/falhados)

A contagem de pedidos de servidor rastreados que foram marcados como *falhados*. Por predefinição, o Application Insights SDK marca automaticamente cada pedido de servidor que devolveu o código de resposta HTTP 5xx ou 4xx como um pedido falhado. Pode personalizar esta lógica modificando a propriedade de *sucesso* do item de telemetria de pedido num [inicializador de telemetria personalizado.](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|de palavras|de palavras|Instância de função em nuvem, nome de função cloud, tráfego real ou sintético, Desempenho do pedido, código de resposta|A versão baseada em log utiliza a agregação **de Soma**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Exceções do servidor (exceções/servidor)

Esta métrica mostra o número de exceções do servidor.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|de palavras|de palavras|Nome de papel em nuvem, instância de papel em nuvem|A versão baseada em log utiliza a agregação **de Soma**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Contadores de desempenho

Utilize métricas na categoria **de contadores de desempenho** para aceder aos [balcões de desempenho do sistema recolhidos pela Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Memória disponível (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Taxa de exceção (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP solicitar tempo de execução (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Taxa de pedido HTTP (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Pedidos HTTP na fila de aplicações (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CpU de processo (performanceCounters/processCpuPercentage)

A métrica mostra quanto da capacidade total do processador é consumida pelo processo que está a hospedar a sua app monitorizada.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Percentagem|Média, Min, Max|Instância de papel em nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Taxa IO do processo (performanceCounters/processIOBytesPerSecond)

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Bytes por segundo|Média, Min, Max|Instância de papel em nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Processo bytes privados (performanceCounters/processPrivateBytes)

Quantidade de memória não partilhada que o processo monitorizado atribuiu aos seus dados.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Bytes|Média, Min, Max|Instância de papel em nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo de processador (performanceCounters/processadorEsPercentage)

Consumo de CPU por *todos os* processos em execução na instância do servidor monitorizado.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Percentagem|Média, Min, Max|Instância de papel em nuvem

>[!NOTE]
> A métrica do tempo do processador não está disponível para as aplicações hospedadas nos Serviços de Aplicações Azure. Utilize a métrica  [do CPU do processo](#process-cpu-performancecountersprocesscpupercentage) para acompanhar a utilização do CPU das aplicações web hospedadas nos Serviços de Aplicações.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Métricas do servidor

### <a name="dependency-calls-dependenciescount"></a>Chamadas de dependência (dependências/contagem)

Esta métrica está relacionada com o número de chamadas de dependência.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Duração da dependência (dependências/duração)

Esta métrica refere-se à duração das chamadas de dependência.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Pedidos de servidor (pedidos/contagem)

Esta métrica reflete o número de pedidos de servidor de entrada que foram recebidos pela sua aplicação web.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Tempo de resposta do servidor (pedidos/duração)

Esta métrica reflete o tempo que os servidores demoraram a processar os pedidos de entrada.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Métricas de utilização

### <a name="page-view-load-time-pageviewsduration"></a>Tempo de carga da visualização da página (páginaVerses/duração)

Esta métrica refere-se ao tempo que os eventos pageView demoraram a carregar.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Visualizações da página (páginaVersas/contagem)

A contagem de eventos PageView registadas com a API de Insights de Aplicação TrackPageView()

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessões (sessões/contagem)

Esta métrica refere-se à contagem de IDs de sessão distintos.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Vestígios (vestígios/contagem)

A contagem de registos de vestígios registados com a chamada API de Insights de Aplicação TrackTrace() Application Insights.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Utilizadores (utilizadores/contagem)

O número de utilizadores distintos que acederam à sua aplicação. A precisão desta métrica pode ser significativamente afetada através da utilização de amostragem e filtragem de telemetria.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Utilizadores, Autenticados (utilizadores/autenticados)

O número de utilizadores distintos que autenticaram a sua aplicação.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```