---
title: Modelo de dados do Aplicativo Azure insights | Microsoft Docs
description: Descreve as propriedades exportadas da exportação contínua em JSON e usadas como filtros.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/08/2019
ms.openlocfilehash: 8f84e3179a6f949e4a322a2218736fc9ebe60442
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677916"
---
# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação Application Insights
Esta tabela lista as propriedades de telemetria enviadas dos SDKs de [Application insights](../../azure-monitor/app/app-insights-overview.md) para o Portal.
Você verá essas propriedades na saída de dados da [exportação contínua](export-telemetry.md).
Eles também aparecem em filtros de propriedade no [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md) e na [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md).

Pontos a serem observados:

* `[0]` nessas tabelas denota um ponto no caminho onde você precisa inserir um índice; Mas nem sempre é 0.
* As durações de tempo são em décimos de um microssegundo, portanto, 10 milhões = = 1 segundo.
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
Todos os tipos de telemetria são acompanhados por uma seção de contexto. Nem todos esses campos são transmitidos com todos os pontos de dados.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| Context. Custom. Dimensions [0] |objeto [] |Pares de cadeia de caracteres de chave-valor definidos pelo parâmetro Propriedades personalizadas. Comprimento máximo da chave 100, tamanho máximo de valores 1024. Mais de 100 valores exclusivos, a propriedade pode ser pesquisada, mas não pode ser usada para segmentação. Máximo de 200 chaves por iKey. |
| Context. Custom. métricas [0] |objeto [] |Pares chave-valor definidos pelo parâmetro de medidas personalizadas e por TrackMetrics. Comprimento máximo da chave 100, os valores podem ser numéricos. |
| Context. Data. EventTime |string |HORÁRIO |
| Context. Data. issintética |boolean |A solicitação parece vir de um teste na Web ou bot. |
| Context. Data. samplingRate |número |Porcentagem de telemetria gerada pelo SDK que é enviado ao Portal. Intervalo 0,0-100.0. |
| contexto. dispositivo |objeto |Dispositivo do cliente |
| contexto. Device. browser |string |IE, Chrome,... |
| Context. Device. browserVersion |string |Chrome 48,0,... |
| Context. Device. deviceModel |string | |
| Context. Device. DeviceName |string | |
| context.device.id |string | |
| contexto. Device. Locale |string |en-GB, de-DE,... |
| contexto. Device. Network |string | |
| Context. Device. oemName |string | |
| contexto. Device. os |string | |
| Context. Device. osVersion |string |Sistema operacional do host |
| Context. Device. roleInstance |string |ID do host do servidor |
| Context. Device. roleName |string | |
| Context. Device. screenResolution |string | |
| Context. Device. Type |string |PC, navegador,... |
| contexto. local |objeto |Derivado de clientip. |
| Context. Location. City |string |Derivado de clientip, se conhecido |
| Context. Location. clientip |string |O último octógono é anônimo para 0. |
| Context. Location. continente |string | |
| contexto. local. Country |string | |
| Context. Location. província |string |Estado ou província |
| context.operation.id |string |Os itens que têm a mesma ID de operação são mostrados como itens relacionados no Portal. Geralmente a ID da solicitação. |
| context.operation.name |string |URL ou nome da solicitação |
| Context. Operation. parentId |string |Permite itens relacionados aninhados. |
| context.session.id |string |ID de um grupo de operações da mesma origem. Um período de 30 minutos sem uma operação sinaliza o final de uma sessão. |
| Context. Session. isprimeiro |boolean | |
| Context. User. accountAcquisitionDate |string | |
| Context. User. AccountId |string | |
| Context. User. anonAcquisitionDate |string | |
| Context. User. anonid |string | |
| Context. User. authAcquisitionDate |string |[Usuário autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| Context. User. authid |string | |
| Context. User. IsAuthenticated |boolean | |
| Context. User. storeRegion |string | |
| Internal. Data. documentVersion |string | |
| internal.data.id |string | ID exclusiva que é atribuída quando um item é ingerido para Application Insights |

## <a name="events"></a>Eventos
Eventos personalizados gerados por [TrackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| contagem do evento [0] |número inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| nome do evento [0] |string |Nome do evento.  Comprimento máximo de 250. |
| URL do evento [0] |string | |
| evento [0] urlData. base |string | |
| evento [0] urlData. host |string | |

## <a name="exceptions"></a>Exceções
Relata [exceções](../../azure-monitor/app/asp-net-exceptions.md) no servidor e no navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| assembly basicexception [0] |string | |
| contagem de basicexception [0] |número inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| camada de exceção basicexception [0] |string | |
| ExceptionType de basicexception [0] |string | |
| basicexception [0] failedUserCodeMethod |string | |
| basicexception [0] failedUserCodeAssembly |string | |
| basicexception [0] handledAt |string | |
| basicexception [0] hasFullStack |boolean | |
| ID de basicexception [0] |string | |
| método basicexception [0] |string | |
| basicexception [0] mensagem |string |Mensagem de exceção. Comprimento máximo 10K. |
| basicexception [0] outerExceptionMessage |string | |
| basicexception [0] outerExceptionThrownAtAssembly |string | |
| basicexception [0] outerExceptionThrownAtMethod |string | |
| basicexception [0] outerExceptionType |string | |
| basicexception [0] externaid |string | |
| assembly basicexception [0] parsedStack [0] |string | |
| basicexception [0] parsedStack [0] nome do arquivo |string | |
| nível basicexception [0] parsedStack [0] |número inteiro | |
| basicexception [0] parsedStack [0] linha |número inteiro | |
| método basicexception [0] parsedStack [0] |string | |
| basicexception [0] pilha |string |Comprimento máximo 10K |
| typeName de basicexception [0] |string | |

## <a name="trace-messages"></a>Mensagens de rastreamento
Enviado por [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)e pelos [adaptadores de registro em log](../../azure-monitor/app/asp-net-trace-logs.md).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| mensagem [0] loggername |string | |
| parâmetros da mensagem [0] |string | |
| mensagem [0] RAW |string |A mensagem de log, comprimento máximo 10K. |
| mensagem [0] nível |string | |

## <a name="remote-dependency"></a>Dependência remota
Enviado por TrackDependency. Usado para relatar o desempenho e o uso de [chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md) no servidor e chamadas AJAX no navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| remoteDependency [0] Async |boolean | |
| remoteDependency [0] baseName |string | |
| remoteDependency [0] commandName |string |Por exemplo, "home/index" |
| contagem de remoteDependency [0] |número inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |número |Tempo desde a chamada até a conclusão da resposta por dependência |
| remoteDependency [0] ID |string | |
| remoteDependency [0] nome |string |URL. Comprimento máximo de 250. |
| remoteDependency [0] resultCode |string |da dependência HTTP |
| remoteDependency [0] êxito |boolean | |
| remoteDependency [0] tipo |string |Http, SQL,... |
| URL do remoteDependency [0] |string |Comprimento máximo de 2000 |
| remoteDependency [0] urlData. base |string |Comprimento máximo de 2000 |
| remoteDependency [0] urlData. hashTag |string | |
| remoteDependency [0] urlData. host |string |Comprimento máximo de 200 |

## <a name="requests"></a>Pedidos
Enviado por [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Os módulos padrão usam isso para relatar o tempo de resposta do servidor, medido no servidor.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| contagem [0] da solicitação |número inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo: 4 = &gt; 25%. |
| solicitação [0] durationMetric. Value |número |Tempo da solicitação que chega à resposta. 1e7 = = 1s |
| ID da solicitação [0] |string |ID da operação |
| nome da solicitação [0] |string |GET/POST + URL base.  Comprimento máximo de 250 |
| solicitar [0] responseCode |número inteiro |Resposta HTTP enviada ao cliente |
| solicitação [0] com êxito |boolean |Padrão = = (responseCode &lt; 400) |
| URL da solicitação [0] |string |Não incluindo host |
| solicitação [0] urlData. base |string | |
| solicitação [0] urlData. hashTag |string | |
| solicitação [0] urlData. host |string | |

## <a name="page-view-performance"></a>Desempenho de exibição de página
Enviado pelo navegador. Mede o tempo para processar uma página, desde o usuário iniciar a solicitação para exibir a conclusão (excluindo chamadas assíncronas AJAX).

Os valores de contexto mostram o so do cliente e a versão do navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |número inteiro |Tempo desde o fim do recebimento do HTML até a exibição da página. |
| clientPerformance [0] nome |string | |
| clientPerformance [0] networkConnection. Value |número inteiro |Tempo necessário para estabelecer uma conexão de rede. |
| clientPerformance [0] receiveRequest. Value |número inteiro |Tempo desde o fim do envio da solicitação até o recebimento do HTML em resposta. |
| clientPerformance [0] sendRequest. Value |número inteiro |Tempo desde que foi levado para enviar a solicitação HTTP. |
| clientPerformance [0] total. valor |número inteiro |Tempo desde o início até o envio da solicitação para exibir a página. |
| URL do clientPerformance [0] |string |URL desta solicitação |
| clientPerformance [0] urlData. base |string | |
| clientPerformance [0] urlData. hashTag |string | |
| clientPerformance [0] urlData. host |string | |
| clientPerformance [0] urlData. Protocol |string | |

## <a name="page-views"></a>Exibições de página
Enviado por trackPageView () ou [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Caminho | Tipo | Notas |
| --- | --- | --- |
| Exibir [0] contagem |número inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| Exibir [0] durationMetric. Value |número inteiro |Valor opcionalmente definido em trackPageView () ou por startTrackPage ()-stopTrackPage (). Não é o mesmo que os valores de clientPerformance. |
| Exibir [0] nome |string |Título da página.  Comprimento máximo de 250 |
| Exibir [0] URL |string | |
| Exibir [0] urlData. base |string | |
| Exibir [0] urlData. hashTag |string | |
| Exibir [0] urlData. host |string | |

## <a name="availability"></a>Disponibilidade
Relata [testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| disponibilidade [0] availabilityMetric.name |string |availability |
| disponibilidade [0] availabilityMetric. Value |número |1,0 ou 0,0 |
| contagem de disponibilidade [0] |número inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo 4 = &gt; 25%. |
| disponibilidade [0] dataSizeMetric.name |string | |
| disponibilidade [0] dataSizeMetric. Value |número inteiro | |
| disponibilidade [0] durationMetric.name |string | |
| disponibilidade [0] durationMetric. Value |número |Duração do teste. 1e7 = = 1s |
| mensagem de disponibilidade [0] |string |Diagnóstico de falha |
| resultado da disponibilidade [0] |string |Aprovado/reprovado |
| disponibilidade [0] runLocation |string |Fonte geográfica de req. http |
| disponibilidade [0] TestName |string | |
| disponibilidade [0] testRunId |string | |
| disponibilidade [0] testTimestamp |string | |

## <a name="metrics"></a>Métricas
Gerado por TrackMetric ().

O valor da métrica é encontrado em Context. Custom. métricas [0]

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

## <a name="about-metric-values"></a>Sobre valores de métrica
Os valores de métrica, tanto em relatórios de métricas quanto em outro lugar, são relatados com uma estrutura de objeto padrão. Por exemplo:

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

Atualmente, embora isso possa mudar no futuro, em todos os valores relatados dos módulos padrão do SDK, `count==1` e somente os campos `name` e `value` são úteis. O único caso em que seria diferente seria se você escrevesse suas próprias chamadas TrackMetric, nas quais você definiu os outros parâmetros.

A finalidade dos outros campos é permitir que as métricas sejam agregadas no SDK, para reduzir o tráfego para o Portal. Por exemplo, você pode calcular várias leituras sucessivas antes de enviar cada relatório de métrica. Em seguida, você calcularia o desvio mínimo, máximo, padrão e o valor agregado (Sum ou Average) e definirá Count como o número de leituras representadas pelo relatório.

Nas tabelas acima, omitimos a contagem de campos raramente utilizados, min, Max, stdDev e sampledValue.

Em vez de agregar previamente as métricas, você pode usar a [amostragem](../../azure-monitor/app/sampling.md) se precisar reduzir o volume de telemetria.

### <a name="durations"></a>Durações
Exceto quando indicado de outra forma, as durações são representadas em décimos de um microssegundo, de modo que 10000000,0 significa 1 segundo.

## <a name="see-also"></a>Ver também
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Exportação contínua](export-telemetry.md)
* [Exemplos de código](export-telemetry.md#code-samples)
