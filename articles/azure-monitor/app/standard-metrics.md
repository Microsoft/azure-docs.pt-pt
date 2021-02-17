---
title: Azure Application Insights métricas padrão | Microsoft Docs
description: Este artigo lista métricas de Azure Application Insights com agregações e dimensões suportadas.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572352"
---
# <a name="application-insights-standard-metrics"></a>Métricas padrão de Insights de Aplicação

As métricas padrão são pré-agregadas durante a recolha, têm um melhor desempenho no tempo de consulta. Isto faz deles a melhor escolha para dashboards e alertas em tempo real.

## <a name="availability-metrics"></a>Métricas de disponibilidade

As métricas na categoria Disponibilidade permitem-lhe ver a saúde da sua aplicação web como observado em pontos de todo o mundo. [Configure os testes de disponibilidade](../app/monitor-web-app-availability.md) para começar a utilizar quaisquer métricas desta categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilidade (disponibilidadeResults/disponibilidadepercentes)
A métrica *disponibilidade* mostra a percentagem dos testes web que não detetou quaisquer problemas. O valor mais baixo possível é 0, o que indica que todos os testes web falharam. O valor de 100 significa que todos os testes web passaram os critérios de validação.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Percentagem|Média|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Duração do teste de disponibilidade (disponibilidadeResults/duração)

A métrica *da duração do teste de Disponibilidade* mostra quanto tempo levou para o teste web ser executado. Para os [testes web em várias etapas,](../app/availability-multistep.md)a métrica reflete o tempo total de execução de todas as etapas.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Milissegundos|Média, Min, Max|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Testes de disponibilidade (disponibilidadeResults/contagem)

A métrica *dos testes de Disponibilidade* reflete a contagem dos testes web realizados pelo Azure Monitor.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|---|---|---|
|de palavras|de palavras|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Métricas do navegador

As métricas do navegador são recolhidas pela Aplicação Insights JavaScript SDK a partir de navegadores reais de utilizadores finais. Eles fornecem excelentes informações sobre a experiência dos seus utilizadores com a sua aplicação web. As métricas do navegador normalmente não são amostradas, o que significa que fornecem uma maior precisão dos números de utilização em comparação com métricas do lado do servidor que podem ser distorcidas por amostragem.

> [!NOTE]
> Para recolher as métricas do navegador, a sua aplicação deve ser instrumentada com o [App Insights JavaScript SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo de carregamento da página do navegador (browserTimings/totalDuration)

|Unidade de medida|Agregações apoiadas| Dimensões suportadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo de processamento do cliente (browserTiming/processingDuration)

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo de ligação da rede de carga de página (browserTimings/networkDuration)

|Unidade de medida|Agregações apoiadas| Dimensões suportadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Tempo de resposta de receção (browserTimings/receiveDuration)

|Unidade de medida|Agregações apoiadas| Dimensões suportadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

### <a name="send-request-time-browsertimingssendduration"></a>Enviar tempo de pedido (browserTimings/sendDuration)

|Unidade de medida|Agregações apoiadas| Dimensões suportadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

## <a name="failure-metrics"></a>Métricas de falha

As métricas em Falhas mostram **problemas** com pedidos de processamento, chamadas de dependência e exceções lançadas.

### <a name="browser-exceptions-exceptionsbrowser"></a>Exceções do navegador (exceções/navegador)

Esta métrica reflete o número de exceções lançadas do seu código de aplicação em execução no navegador. Apenas as exceções que são rastreadas com uma ```trackException()``` chamada API de Insights de Aplicação estão incluídas na métrica.

|Unidade de medida|Agregações apoiadas | Dimensões suportadas|
|---|---|---|---|
| de palavras | de palavras | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Falhas nas chamadas de dependência (dependências/falhas)

O número de chamadas de dependência falhadas.

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
|de palavras|de palavras| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Exceções (exceções/contagem)

Sempre que regista uma exceção ao Application Insights, há uma chamada para o [método trackException do](../app/api-custom-events-metrics.md#trackexception) SDK. A métrica das Exceções mostra o número de exceções registadas.

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
|de palavras|de palavras|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Pedidos falhados (pedidos/falhados)

A contagem de pedidos de servidor rastreados que foram marcados como *falhados*. Por predefinição, o Application Insights SDK marca automaticamente cada pedido de servidor que devolveu o código de resposta HTTP 5xx ou 4xx como um pedido falhado. Pode personalizar esta lógica modificando a propriedade de *sucesso* do item de telemetria de pedido num [inicializador de telemetria personalizado.](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)


|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
|de palavras|de palavras|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Exceções do servidor (exceções/servidor)

Esta métrica mostra o número de exceções do servidor.

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
|de palavras|de palavras|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Contadores de desempenho

Utilize métricas na categoria **de contadores de desempenho** para aceder aos [balcões de desempenho do sistema recolhidos pela Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Memória disponível (performanceCounters/availableMemory)

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
|Dados dependentes: Megabytes, Gigabytes|Média, Max, Min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Taxa de exceção (performanceCounters/exceptionRate)

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
| de palavras | Média, Max, Min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP solicitar tempo de execução (performanceCounters/requestExecutionTime)

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
| Milissegundos | Média, Max, Min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Taxa de pedido HTTP (performanceCounters/requestsPerSecond)

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
| Pedidos por segundo | Média, Max, Min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Pedidos HTTP na fila de aplicações (performanceCounters/requestsInQueue)

|Unidade de medida|Agregações apoiadas | Dimensões suportadas |
|---|---|---|---|
| de palavras | Média, Max, Min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CpU de processo (performanceCounters/processCpuPercentage)

A métrica mostra quanto da capacidade total do processador é consumida pelo processo que está a hospedar a sua app monitorizada.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Percentagem|Média, Max, Min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Taxa IO do processo (performanceCounters/processIOBytesPerSecond)

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Bytes por segundo|Média, Min, Max|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Processo bytes privados (performanceCounters/processPrivateBytes)

Quantidade de memória não partilhada que o processo monitorizado atribuiu aos seus dados.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Bytes|Média, Min, Max|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo de processador (performanceCounters/processadorEsPercentage)

Consumo de CPU por *todos os* processos em execução na instância do servidor monitorizado.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
|Percentagem|Média, Min, Max|`Cloud role instance` |

>[!NOTE]
> A métrica do tempo do processador não está disponível para as aplicações hospedadas nos Serviços de Aplicações Azure. Utilize a métrica  [do CPU do processo](#process-cpu-performancecountersprocesscpupercentage) para acompanhar a utilização do CPU das aplicações web hospedadas nos Serviços de Aplicações.

## <a name="server-metrics"></a>Métricas do servidor

### <a name="dependency-calls-dependenciescount"></a>Chamadas de dependência (dependências/contagem)

Esta métrica está relacionada com o número de chamadas de dependência.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| de palavras | de palavras | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Duração da dependência (dependências/duração)

Esta métrica refere-se à duração das chamadas de dependência.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| Hora | Média, Min, Max | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Taxa de pedido do servidor (pedidos/contagem)

Esta métrica reflete o número de pedidos de servidor de entrada que foram recebidos pela sua aplicação web.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| de palavras | Média | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Pedidos de servidor (pedidos/contagem)

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| de palavras | de palavras | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Tempo de resposta do servidor (pedidos/duração)

Esta métrica reflete o tempo que os servidores demoraram a processar os pedidos de entrada.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| Hora | Média, Min, Max | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Métricas de utilização

### <a name="page-view-load-time-pageviewsduration"></a>Tempo de carga da visualização da página (páginaVerses/duração)

Esta métrica refere-se ao tempo que os eventos pageView demoraram a carregar.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| Hora | Média, Min, Max | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Visualizações da página (páginaVersas/contagem)

A contagem de eventos PageView registadas com a API de Insights de Aplicação TrackPageView()

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| de palavras | de palavras | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Vestígios (vestígios/contagem)

A contagem de registos de vestígios registados com a chamada API de Insights de Aplicação TrackTrace() Application Insights.

|Unidade de medida|Agregações apoiadas|Dimensões suportadas|
|---|---|---|
| de palavras | de palavras | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [métricas baseadas em log e pré-agregadas.](./pre-aggregated-metrics-log-metrics.md)
* [Consultas e definições de métricas baseadas em registos.](../essentials/app-insights-metrics.md)