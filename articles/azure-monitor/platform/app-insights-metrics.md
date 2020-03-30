---
title: Azure Application Insights Métricas baseadas em log Microsoft Docs
description: Este artigo lista as métricas de Insights de Aplicação Azure com agregados e dimensões suportadas. Os detalhes sobre métricas baseadas em log incluem as declarações de consulta de Kusto subjacentes.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664920"
---
# <a name="application-insights-log-based-metrics"></a>Aplicações Insights métricas baseadas em log

Aplicações Insights métricas baseadas em log permitem analisar a saúde das suas aplicações monitorizadas, criar dashboards poderosos e configurar alertas. Há dois tipos de métricas:

* [As métricas baseadas em log](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) sem registo são traduzidas em [consultas de Kusto](https://docs.microsoft.com/azure/kusto/query/) de eventos armazenados.
* [As métricas padrão](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) são armazenadas como séries temporais pré-agregadas.

Uma vez que as *métricas padrão* são pré-agregadas durante a recolha, têm um melhor desempenho no momento da consulta. Isto torna-os uma escolha melhor para o dashboard e em alerta em tempo real. As *métricas baseadas em log* têm mais dimensões, o que as torna a opção superior para a análise de dados e diagnósticos ad-hoc. Utilize o [seletor](metrics-getting-started.md#create-your-first-metric-chart) espaço de nome para alternar entre métricas baseadas em registo e métricas padrão no explorador de [métricas](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretar e usar consultas deste artigo

Este artigo enumera métricas com agregações e dimensões suportadas. Os detalhes sobre métricas baseadas em log incluem as declarações de consulta de Kusto subjacentes. Para conveniência, cada consulta utiliza predefinições para granularidade de tempo, tipo de gráfico e, por vezes, dimensão de divisão que simplifica usando a consulta no Log Analytics sem qualquer necessidade de modificação.

Quando traça a mesma métrica no explorador de [métricas](metrics-getting-started.md), não existem incumprimentos - a consulta é ajustada dinamicamente com base nas definições do seu gráfico:

- A **gama** de tempo selecionada é traduzida para um adicional onde o *timestamp...* cláusula para apenas escolher os eventos a partir do intervalo de tempo selecionado. Por exemplo, um gráfico que mostra os dados das últimas 24 horas, a consulta inclui *onde o carimbo temporal >(24 h)*.

- A **granularidade do tempo** selecionada é colocada na *sinuosidade final... por cláusula bin (timestamp, [time grain]).*

- Quaisquer dimensões de **filtro** selecionadas são traduzidas para um adicional *onde* as cláusulas.

- A dimensão do **gráfico Split** selecionada é traduzida para uma propriedade extra resumo. Por exemplo, se dividir o seu gráfico por *localização*, e traçar usando uma granularidade de 5 minutos, a cláusula de *resumo* é resumida... * por caixote do lixo (carimbo de tempo, 5 m), localização*.

> [!NOTE]
> Se você é novo na linguagem de consulta Kusto, você começa por copiar e colar declarações de Kusto no painel de consulta log Analytics sem fazer quaisquer modificações. Clique em **Correr** para ver gráfico básico. À medida que começa a entender a sintaxe da linguagem de consulta, pode começar a fazer pequenas modificações e ver o impacto da sua mudança. Explorar os seus próprios dados é uma ótima maneira de começar a perceber toda a potência do [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) e do [Monitor Azure.](../../azure-monitor/overview.md)

## <a name="availability-metrics"></a>Métricas de disponibilidade

As métricas na categoria Disponibilidade permitem-lhe ver a saúde da sua aplicação web como observada a partir de pontos de todo o mundo. [Configure os testes](../../azure-monitor/app/monitor-web-app-availability.md) de disponibilidade para começar a utilizar quaisquer métricas desta categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilidade (disponibilidadeResultados/disponibilidadePercentage)
A métrica de *disponibilidade* mostra a percentagem dos testes web que não detetaram nenhum problema. O valor mais baixo possível é 0, o que indica que todos os testes da web falharam. O valor de 100 significa que todos os testes web passam pelos critérios de validação.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Percentagem|Média|Localização de execução, nome do teste|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Duração do teste de disponibilidade (disponibilidadeResultados/duração)

A métrica de *duração* do teste de disponibilidade mostra quanto tempo demorou para o teste web ser executado. Para os [testes web em várias etapas,](../../azure-monitor/app/availability-multistep.md)a métrica reflete o tempo total de execução de todos os passos.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Milissegundos|Média, Min, Max|Localização de execução, nome do teste, resultado do teste

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testes de disponibilidade (disponibilidadeResultados/contagem)

A métrica de testes de *disponibilidade* reflete a contagem dos testes web realizados pelo Azure Monitor.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Contagem|Contagem|Localização de execução, nome do teste, resultado do teste|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Métricas do navegador

As métricas do navegador são recolhidas pelo Formulário de Aplicação JavaScript SDK a partir de navegadores de utilizador final reais. Eles fornecem excelentes informações sobre a experiência dos seus utilizadores com a sua aplicação web. As métricas do navegador não são tipicamente amostradas, o que significa que fornecem uma maior precisão dos números de utilização em comparação com as métricas do lado do servidor que podem ser distorcidas por amostragem.

> [!NOTE]
> Para recolher as métricas do navegador, a sua aplicação deve ser instrumentada com os Insights de [Aplicação JavaScript SDK](../../azure-monitor/app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo de carregamento da página do navegador (browserTimings/totalDuração)

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhuma|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo de processamento do cliente (browserTiming/processingDura)

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhuma|

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

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhuma|

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

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhuma|

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

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhuma|

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

As métricas em **Falhas** mostram problemas com pedidos de processamento, chamadas de dependência e exceções lançadas.

### <a name="browser-exceptions-exceptionsbrowser"></a>Exceções ao navegador (exceções/navegador)

Esta métrica reflete o número de exceções lançadas do seu código de aplicação em execução no navegador. Apenas as exceções que ```trackException()``` são rastreadas com uma chamada de API de Insights de Aplicação estão incluídas na métrica.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|Contagem|Contagem|Nenhuma|Versão baseada em log usa agregação **de soma**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Falhas de chamada de dependência (dependências/falhadas)

O número de chamadas de dependência falhadas.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|Contagem|Contagem|Nenhuma|Versão baseada em log usa agregação **de soma**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Exceções (exceções/contagem)

Cada vez que regista uma exceção aos Insights de Aplicação, há uma chamada para o [método trackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) do SDK. A métrica das exceções mostra o número de exceções registadas.

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|Contagem|Contagem|Nome de papel de nuvem, exemplo de papel cloud, tipo de dispositivo|Versão baseada em log usa agregação **de soma**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Pedidos falhados (pedidos/falhados)

A contagem de pedidos de servidores rastreados que foram marcados como *falhados*. Por predefinição, o SDK de Insights de Aplicação marca automaticamente cada pedido de servidor que devolveu o código de resposta HTTP 5xx ou 4xx como um pedido falhado. Você pode personalizar esta lógica modificando a propriedade de *sucesso* de artigo de telemetria de pedido em um [inicializador de telemetria personalizado.](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

|Unidade de medida|Agregações apoiadas|Dimensões pré-agregadas|Notas|
|---|---|---|---|
|Contagem|Contagem|Caso de função em nuvem, nome de papel em nuvem, tráfego real ou sintético, desempenho de pedido, código de resposta|Versão baseada em log usa agregação **de soma**|

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
|Contagem|Contagem|Nome de papel de nuvem, exemplo de papel cloud|Versão baseada em log usa agregação **de soma**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Contadores de desempenho

Utilize métricas na categoria **de contadores** de desempenho para aceder aos contadores de desempenho do [sistema recolhidos pela Application Insights](../../azure-monitor/app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Memória disponível (performanceCounters/memória disponível)

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

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>PEDIDOS HTTP na fila de aplicações (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU de processo (performanceCounters/processCpuPercentage)

A métrica mostra quanto da capacidade total do processador é consumida pelo processo que está a acolher a sua app monitorizada.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Percentagem|Média, Min, Max|Instância de papel de nuvem

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
|Bytes por segundo|Média, Min, Max|Instância de papel de nuvem

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
|Bytes|Média, Min, Max|Instância de papel de nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo do processador (desempenhoCounters/processadorCpuPercentage)

Consumo de CPU por *todos os* processos em execução na instância do servidor monitorizado.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Percentagem|Média, Min, Max|Instância de papel de nuvem

>[!NOTE]
> A métrica de tempo do processador não está disponível para as aplicações alojadas nos Serviços de Aplicações Azure. Utilize a métrica [do Processo CPU](#process-cpu-performancecountersprocesscpupercentage) para acompanhar a utilização do CPU das aplicações web alojadas nos Serviços de Aplicações.

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

### <a name="dependency-duration-dependenciesduration"></a>Duração da dependência (dependência/duração)

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

Esta métrica reflete o número de pedidos de servidor estiveram recebidos pela sua aplicação web.

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

### <a name="page-view-load-time-pageviewsduration"></a>Tempo de carga da vista da página (páginaVistas/duração)

Esta métrica refere-se ao tempo que os eventos do PageView demoraram a carregar.

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

### <a name="page-views-pageviewscount"></a>Visualizações da página (pageViews/count)

A contagem de eventos pageView registrado com a API de Insights de Aplicação TrackPageView()

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessões (sessões/contagem)

Esta métrica refere-se à contagem de iDs de sessão distintas.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Vestígios (vestígios/contagem)

A contagem de declarações de vestígios registadas com a chamada TrackTrace() Application Insights API.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Utilizadores (utilizadores/contagem)

O número de utilizadores distintos que acederam à sua aplicação. A precisão desta métrica pode ser significativamente impactada através da utilização de amostragem e filtragem de telemetria.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Utilizadores, Autenticados (utilizadores/autenticados)

O número de utilizadores distintos que autenticaram na sua aplicação.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
