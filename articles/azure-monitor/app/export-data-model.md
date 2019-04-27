---
title: Modelo de dados do Azure Application Insights | Documentos da Microsoft
description: Descreve propriedades exportado da exportação contínua em JSON e usados como filtros.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: mbullwin
ms.openlocfilehash: 12025dfb93bbcfc86ae301f8fb63e7ac74697cf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898921"
---
# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação do Application Insights
Esta tabela lista as propriedades de telemetria enviada pela [Application Insights](../../azure-monitor/app/app-insights-overview.md) SDKs para o portal.
Verá essas propriedades na saída de dados do [exportação contínua](export-telemetry.md).
Eles também aparecem em filtros de propriedade no [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) e [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md).

Pontos a serem observados:

* `[0]` nestas tabelas denota um ponto no caminho em que precisa inserir um índice; mas nem sempre é 0.
* Horas de duração estão em décimas das microssegundo, então, 10000000 = = 1 segundo.
* Datas e horas são UTC e são fornecidas no formato ISO `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Exemplo
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Contexto
Todos os tipos de telemetria são acompanhados por uma seção de contexto. Nem todos estes campos são transmitidos com todos os pontos de dados.

| Caminho | Type | Notas |
| --- | --- | --- |
| Context.Custom.dimensions [0] |objeto [] |Pares de cadeia de caracteres de chave-valor definido pelo parâmetro de propriedades personalizadas. Os valores de comprimento de chave máximo 100, comprimento máximo de 1024. Mais de 100 valores exclusivos, a propriedade pode ser pesquisada, mas não pode ser utilizada para segmentação. Chaves de 200 máx. por ikey. |
| Context.Custom.Metrics [0] |objeto [] |Pares chave-valor definido pelo parâmetro de medições personalizadas e por TrackMetrics. Comprimento máximo 100 da chave, valores podem ser um valor numéricos. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |boolean |Pedido parece ser proveniente de um teste web ou de bot. |
| context.data.samplingRate |número |Percentagem de telemetria gerada pelo SDK do que é enviado para o portal. Intervalo de 0,0 100,0. |
| context.device |objeto |Dispositivo de cliente |
| context.device.browser |string |O IE, Chrome,... |
| context.device.browserVersion |string |Chrome 48.0,... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| context.device.locale |string |en-GB, de-DE... |
| context.device.network |string | |
| context.device.oemName |string | |
| context.device.os |string | |
| context.device.osVersion |string |SO do Host |
| context.device.roleInstance |string |ID de anfitrião do servidor |
| context.device.roleName |string | |
| context.device.screenResolution |string | |
| context.device.type |string |PC, o navegador,... |
| context.location |objeto |Derivado dos clientip. |
| context.location.city |string |Derivado dos clientip, se conhecidos |
| context.location.clientip |string |Um último octógono é anónimos como 0. |
| context.location.continent |string | |
| context.location.country |string | |
| context.location.province |string |Distrito |
| context.operation.id |string |Itens que tenham o mesmo id de operação são apresentados como itens relacionados no portal. Normalmente, o id do pedido. |
| context.operation.name |string |nome de URL ou a pedido |
| context.operation.parentId |string |Permite que os itens relacionados aninhados. |
| context.session.id |string |ID de um grupo de operações da mesma origem. Um período de 30 minutos sem uma operação sinaliza o término de uma sessão. |
| context.session.isFirst |boolean | |
| context.user.accountAcquisitionDate |string | |
| context.user.accountId |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Utilizador autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |string | |
| context.user.isAuthenticated |boolean | |
| context.user.storeRegion |string | |
| internal.data.documentVersion |string | |
| internal.data.id |string | Id exclusivo atribuído quando um item é ingerido para o Application Insights |

## <a name="events"></a>Eventos
Eventos personalizados gerados pelo [trackevent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Caminho | Type | Notas |
| --- | --- | --- |
| Contagem de eventos [0] |inteiro |100 / ([amostragem](../../azure-monitor/app/sampling.md) taxa). Por exemplo, 4 =&gt; 25%. |
| nome do evento [0] |string |Nome do evento.  Comprimento máximo de 250. |
| url de eventos [0] |string | |
| o evento [0] urlData.base |string | |
| o evento [0] urlData.host |string | |

## <a name="exceptions"></a>Exceções
Relatórios [exceções](../../azure-monitor/app/asp-net-exceptions.md) no servidor e no browser.

| Caminho | Type | Notas |
| --- | --- | --- |
| assembly de basicException [0] |string | |
| Contagem de basicException [0] |inteiro |100 / ([amostragem](../../azure-monitor/app/sampling.md) taxa). Por exemplo, 4 =&gt; 25%. |
| exceptionGroup basicException [0] |string | |
| exceptionType basicException [0] |string | |
| failedUserCodeMethod basicException [0] |string | |
| failedUserCodeAssembly basicException [0] |string | |
| handledAt basicException [0] |string | |
| hasFullStack basicException [0] |boolean | |
| id de basicException [0] |string | |
| método basicException [0] |string | |
| mensagem de basicException [0] |string |Mensagem de exceção. Comprimento máximo de 10 mil. |
| outerExceptionMessage basicException [0] |string | |
| outerExceptionThrownAtAssembly basicException [0] |string | |
| outerExceptionThrownAtMethod basicException [0] |string | |
| outerExceptionType basicException [0] |string | |
| outerId basicException [0] |string | |
| assembly de parsedStack [0] basicException [0] |string | |
| o nome de ficheiro do basicException [0] parsedStack [0] |string | |
| nível de parsedStack [0] basicException [0] |inteiro | |
| linha de parsedStack [0] basicException [0] |inteiro | |
| método de parsedStack [0] basicException [0] |string | |
| pilha de basicException [0] |string |Comprimento máximo de 10 mil |
| typeName basicException [0] |string | |

## <a name="trace-messages"></a>Mensagens de Rastreio
Enviado pela [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)e pela [adaptadores de Registro em log](../../azure-monitor/app/asp-net-trace-logs.md).

| Caminho | Type | Notas |
| --- | --- | --- |
| a mensagem [0] loggerName |string | |
| parâmetros de mensagem [0] |string | |
| mensagem [0] não processada |string |A mensagem de registo, comprimento máximo de 10 mil. |
| a mensagem [0] severityLevel |string | |

## <a name="remote-dependency"></a>Dependência remota
Enviado pela TrackDependency. Utilizado para o desempenho do relatório e a utilização da [chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md) no servidor e chamadas AJAX no navegador.

| Caminho | Type | Notas |
| --- | --- | --- |
| async remoteDependency [0] |boolean | |
| Nome_de_base remoteDependency [0] |string | |
| commandName remoteDependency [0] |string |Por exemplo "home/index" |
| Contagem de remoteDependency [0] |inteiro |100 / ([amostragem](../../azure-monitor/app/sampling.md) taxa). Por exemplo, 4 =&gt; 25%. |
| dependencyTypeName remoteDependency [0] |string |HTTP, SQL, ... |
| durationMetric.value remoteDependency [0] |número |Tempo da chamada para a conclusão da resposta pela dependência |
| id de remoteDependency [0] |string | |
| nome de remoteDependency [0] |string |URL. Comprimento máximo de 250. |
| resultCode remoteDependency [0] |string |de dependência HTTP |
| sucesso de remoteDependency [0] |boolean | |
| tipo de remoteDependency [0] |string |Http, Sql,... |
| url de remoteDependency [0] |string |Comprimento máximo de 2000 |
| urlData.base remoteDependency [0] |string |Comprimento máximo de 2000 |
| remoteDependency [0] urlData.hashTag |string | |
| urlData.host remoteDependency [0] |string |Comprimento máximo de 200 |

## <a name="requests"></a>Pedidos
Enviado pela [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Os módulos padrão usado para tempo de resposta do servidor de relatórios, medido no servidor.

| Caminho | Type | Notas |
| --- | --- | --- |
| Contagem de pedidos [0] |inteiro |100 / ([amostragem](../../azure-monitor/app/sampling.md) taxa). Por exemplo: 4 =&gt; 25%. |
| o pedido [0] durationMetric.value |número |Tempo do pedido que chegam à resposta. 1e7 == 1s |
| id do pedido [0] |string |ID da operação |
| nome do pedido [0] |string |GET/pós + url base.  Comprimento máximo de 250 |
| o pedido [0] responseCode |inteiro |Resposta HTTP enviada ao cliente |
| pedido com êxito [0] |boolean |Predefinição = = (responseCode &lt; 400) |
| url do pedido [0] |string |Não incluindo o anfitrião |
| o pedido [0] urlData.base |string | |
| request [0] urlData.hashTag |string | |
| o pedido [0] urlData.host |string | |

## <a name="page-view-performance"></a>Desempenho de vista de página
Enviado pelo navegador. Mede o tempo a processar uma página, do utilizador a iniciar o pedido para apresentar completa (excluindo as chamadas AJAX assíncronas).

Valores de contexto mostram o SO de cliente e a versão do browser.

| Caminho | Type | Notas |
| --- | --- | --- |
| clientProcess.value clientPerformance [0] |inteiro |Hora de fim de receber o HTML para apresentar a página. |
| nome de clientPerformance [0] |string | |
| networkConnection.value clientPerformance [0] |inteiro |Tempo decorrido para estabelecer uma ligação de rede. |
| receiveRequest.value clientPerformance [0] |inteiro |Hora de fim de enviar o pedido para receber o HTML na resposta. |
| sendRequest.value clientPerformance [0] |inteiro |Tempo de direcionado para enviar o pedido HTTP. |
| total.value clientPerformance [0] |inteiro |Hora de começar a enviar o pedido para apresentar a página. |
| url de clientPerformance [0] |string |URL deste pedido |
| clientPerformance [0] urlData.base |string | |
| clientPerformance [0] urlData.hashTag |string | |
| clientPerformance [0] urlData.host |string | |
| urlData.protocol clientPerformance [0] |string | |

## <a name="page-views"></a>Visualizações da Página
Enviado pelo trackPageView() ou [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Caminho | Type | Notas |
| --- | --- | --- |
| Contagem de visualizações [0] |inteiro |100 / ([amostragem](../../azure-monitor/app/sampling.md) taxa). Por exemplo, 4 =&gt; 25%. |
| Ver durationMetric.value [0] |inteiro |Valor se desejar, defina no trackPageView() ou ao startTrackPage() - stopTrackPage(). Não são as mesmas como clientPerformance valores. |
| nome da vista [0] |string |Título da página.  Comprimento máximo de 250 |
| Ver url [0] |string | |
| Ver urlData.base [0] |string | |
| Ver urlData.hashTag [0] |string | |
| Ver urlData.host [0] |string | |

## <a name="availability"></a>Disponibilidade
Relatórios [testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).

| Caminho | Type | Notas |
| --- | --- | --- |
| availabilityMetric.name disponibilidade [0] |string |disponibilidade |
| availabilityMetric.value disponibilidade [0] |número |1.0 ou 0,0 |
| Contagem de disponibilidade [0] |inteiro |100 / ([amostragem](../../azure-monitor/app/sampling.md) taxa). Por exemplo, 4 =&gt; 25%. |
| dataSizeMetric.name disponibilidade [0] |string | |
| dataSizeMetric.value disponibilidade [0] |inteiro | |
| durationMetric.name disponibilidade [0] |string | |
| durationMetric.value disponibilidade [0] |número |Duração do teste. 1e7==1s |
| mensagem de disponibilidade [0] |string |Diagnóstico de falha |
| resultado de disponibilidade [0] |string |Aprovação/reprovação |
| runLocation disponibilidade [0] |string |Origem da geo de pedidos de http |
| testName disponibilidade [0] |string | |
| testRunId disponibilidade [0] |string | |
| testTimestamp disponibilidade [0] |string | |

## <a name="metrics"></a>Métricas
Gerado por trackmetric ().

O valor da métrica é encontrado em context.custom.metrics[0]

Por exemplo:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Sobre os valores de métrica
Valores de métrica, tanto em relatórios de métrica e noutro local, são apresentadas com uma estrutura de objeto padrão. Por exemplo:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Atualmente - embora isso podem ser alteradas no futuro, em todos os valores reportada a partir de módulos SDK padrão, `count==1` apenas e o `name` e `value` campos são úteis. O único caso em que deve ser diferentes seria se escrever suas próprias chamadas TrackMetric no qual é definir os outros parâmetros.

A finalidade dos outros campos é permitir que as métricas para ser agregados no SDK, para reduzir o tráfego para o portal. Por exemplo, poderia média várias leituras sucessivas antes de enviar cada relatório de métrica. Em seguida, seria calcular o min, max, desvio padrão e valor de agregação (sum ou average) e defina a contagem de para o número de leituras representado pelo relatório.

Nas tabelas acima, omitimos a contagem de campos raramente utilizada, min, max, stdDev e sampledValue.

Em vez de pré-agregar métricas, pode usar [amostragem](../../azure-monitor/app/sampling.md) se precisar de reduzir o volume de telemetria.

### <a name="durations"></a>Durações
Exceto onde indicado caso contrário, durações são representadas no décimas de um microssegundo, para que 10000000.0 significa 1 segundo.

## <a name="see-also"></a>Consulte também
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Exportação contínua](export-telemetry.md)
* [Exemplos de código](export-telemetry.md#code-samples)
