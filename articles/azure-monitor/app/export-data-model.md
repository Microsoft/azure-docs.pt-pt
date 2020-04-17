---
title: Modelo de dados da Aplicação Azure Insights [ Modelo de Dados ] Microsoft Docs
description: Descreve propriedades exportadas de exportação contínua em JSON, e usadas como filtros.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 9891bea1d52c61197fa32fa5c0764df5450b563c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536850"
---
# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação de insights de aplicação
Esta tabela lista as propriedades da telemetria enviadas dos SDKs de Insights de [Aplicação](../../azure-monitor/app/app-insights-overview.md) para o portal.
Você verá estas propriedades na saída de dados da [Exportação Contínua](export-telemetry.md).
Também aparecem em filtros de propriedade em [Metric Explorer](../../azure-monitor/platform/metrics-charts.md) e Pesquisa [de Diagnóstico.](../../azure-monitor/app/diagnostic-search.md)

Pontos a notar:

* `[0]`nestas tabelas denota um ponto no caminho em que tem de inserir um índice; mas nem sempre é 0.
* As durações do tempo são em décimos de um microsegundo, por isso 10000000 == 1 segundo.
* Datas e horários são UTC, e são dadas no formato ISO`yyyy-MM-DDThh:mm:ss.sssZ`


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
Todos os tipos de telemetria são acompanhados por uma secção de contexto. Nem todos estes campos são transmitidos com todos os pontos de dados.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| contexto.custom.dimensions [0] |objeto [ ] |Pares de cordas de valor chave definidos por parâmetro de propriedades personalizadas. Comprimento máximo chave 100, valores comprimento máximo 1024. Mais de 100 valores únicos, a propriedade pode ser pesquisada mas não pode ser usada para segmentação. Max 200 chaves por chave. |
| contexto.custom.metrics [0] |objeto [ ] |Pares de valor-chave definidos por parâmetro de medição personalizada e por TrackMetrics. Comprimento máximo chave 100, os valores podem ser numéricos. |
| contexto.data.eventTime |string |UTC |
| contexto.data.isSynthetic |boolean |O pedido parece vir de um teste de bot ou web. |
| contexto.data.samplingRate |número |Percentagem de telemetria gerada pelo SDK que é enviada para o portal. Intervalo 0.0-100.0. |
| contexto.dispositivo |objeto |Dispositivo cliente |
| contexto.dispositivo.browser |string |Ou é, Cromo, ... |
| contexto.device.browserVersion |string |Cromado 48.0, ... |
| contexto.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| contexto.dispositivo.local |string |en-GB, de-DE, ... |
| contexto.device.network |string | |
| contexto.device.oemName |string | |
| contexto.dispositivo.os |string | |
| contexto.dispositivo.osVersão |string |SO Anfitrião |
| contexto.device.roleInstance |string |ID do anfitrião do servidor |
| context.device.roleName |string | |
| contexto.device.screenResolution |string | |
| contexto.dispositivo.type |string |PC, Browser, ... |
| contexto.localização |objeto |Derivado de `clientip`. |
| contexto.location.city |string |Derivado de, `clientip`se conhecido |
| contexto.location.clientip |string |O último octogono é anoonizado a 0. |
| contexto.location.continente |string | |
| contexto.location.country |string | |
| contexto.location.province |string |Estado ou província |
| context.operation.id |string |Itens que têm o `operation id` mesmo são mostrados como Itens Relacionados no portal. Normalmente `request id`o. |
| context.operation.name |string |url ou nome de pedido |
| contexto.operação.parentId |string |Permite itens aninhadas. |
| context.session.id |string |`Id`de um grupo de operações da mesma fonte. Um período de 30 minutos sem operação assinala o fim de uma sessão. |
| contexto.session.isFirst |boolean | |
| contexto.user.accountAcquisitionDate |string | |
| contexto.user.accountId |string | |
| contexto.user.anonAcquisitionDate |string | |
| contexto.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Utilizador Autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| contexto.user.authId |string | |
| contexto.user.isAuthenticated |boolean | |
| contexto.user.storeRegion |string | |
| internal.data.documentVersion |string | |
| internal.data.id |string | `Unique id`que é atribuído quando um item é ingerido para Insights de Aplicação |

## <a name="events"></a>Eventos
Eventos personalizados gerados pelo [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| evento [0] contagem |número inteiro |100/(taxa[de amostragem).](../../azure-monitor/app/sampling.md) Por exemplo&gt; 4 = 25%. |
| evento [0] nome |string |Nome do evento.  Comprimento máximo 250. |
| evento [0] url |string | |
| evento [0] urlData.base |string | |
| evento [0] urlData.host |string | |

## <a name="exceptions"></a>Exceções
Reporta [exceções](../../azure-monitor/app/asp-net-exceptions.md) no servidor e no navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| básicoExcepção [0] montagem |string | |
| básicoExcepção [0] contagem |número inteiro |100/(taxa[de amostragem).](../../azure-monitor/app/sampling.md) Por exemplo&gt; 4 = 25%. |
| basesExcepção [0] excepçãoGrupo |string | |
| basicException [0] exceptionType |string | |
| basicException [0] failedUserCodeMethod |string | |
| básicoExcepção [0] falhouUserCodeAssembly |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |boolean | |
| básicoExcepção [0]`id` |string | |
| método básicoDe exceção [0] |string | |
| basicException [0] mensagem |string |Mensagem de exceção. Comprimento máximo 10k. |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| básicoExcepção [0] exteriorExceptionType |string | |
| basicException [0] outerId |string | |
| básicoExcepção [0] parsedStack [0] montagem |string | |
| basicException [0] parsedStack [0] fileName |string | |
| básicoExcepção [0] parsedStack [0] nível |número inteiro | |
| basicException [0] linha parsedStack [0] |número inteiro | |
| básicoExcepção [0] método parsedStack [0] |string | |
| basicException [0] stack |string |Comprimento máximo 10k |
| básicoExcepção [0] tipoNome |string | |

## <a name="trace-messages"></a>Rastrear mensagens
Enviado por [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace), e pelos adaptadores de [exploração madeireira](../../azure-monitor/app/asp-net-trace-logs.md).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| mensagem [0] loggerName |string | |
| parâmetros de mensagem [0] |string | |
| mensagem [0] cru |string |A mensagem de registo, comprimento máximo 10k. |
| mensagem [0] gravidadeN |string | |

## <a name="remote-dependency"></a>Dependência remota
Enviado pela TrackDependency. Usado para relatar desempenho e uso de [chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md) no servidor, e chamadas AJAX no navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| remotaDependência [0] asincronização |boolean | |
| remotaDependência [0] nome base |string | |
| remotaDependência [0] nome de comando |string |Por exemplo, "casa/índice" |
| remotaDependência [0] contagem |número inteiro |100/(taxa[de amostragem).](../../azure-monitor/app/sampling.md) Por exemplo&gt; 4 = 25%. |
| remotaDependência [0] dependênciaTypeName |string |HTTP, SQL, ... |
| remotaDependência [0] duraçãoMetric.value |número |Tempo da chamada à conclusão da resposta por dependência |
| dependência remota [0]`id` |string | |
| nome remotaDependência [0] |string |Url. Comprimento máximo 250. |
| remotaDependência [0] resultadoCódigo |string |da dependência do HTTP |
| remotoDependência [0] sucesso |boolean | |
| tipo de dependência remota [0] |string |Http, Sql,... |
| remotaDependência [0] url |string |Comprimento máximo 2000 |
| remotaDependência [0] urlData.base |string |Comprimento máximo 2000 |
| remotaDependência [0] urlData.hashTag |string | |
| remotaDependência [0] urlData.host |string |Comprimento máximo 200 |

## <a name="requests"></a>Pedidos
Enviado por [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Os módulos padrão usam isto para reportar o tempo de resposta do servidor, medido no servidor.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| pedido [0] contagem |número inteiro |100/(taxa[de amostragem).](../../azure-monitor/app/sampling.md) Por exemplo:&gt; 4 = 25%. |
| pedido [0] duraçãoMetric.value |número |Tempo do pedido chegar à resposta. 1e7 == 1s |
| pedido [0]`id` |string |`Operation id` |
| solicitar [0] nome |string |GET/POST + url base.  Comprimento máximo 250 |
| solicitar [0] respostaCódigo |número inteiro |Resposta HTTP enviada ao cliente |
| solicitar [0] sucesso |boolean |Padrão == (código de resposta &lt; 400) |
| pedido [0] url |string |Sem incluir o hospedeiro |
| pedido [0] urlData.base |string | |
| pedido [0] urlData.hashTag |string | |
| pedido [0] urlData.host |string | |

## <a name="page-view-performance"></a>Desempenho da visualização da página
Enviado pelo navegador. Mede o tempo para processar uma página, desde o utilizador que dá início ao pedido de exibição completo (excluindo chamadas ajax asincronizadas).

Os valores de contexto mostram o CLIENTE OS e a versão do navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| clientePerformance [0] clienteProcess.value |número inteiro |Tempo desde o fim da receção do HTML até exibir a página. |
| clientePerformance [0] nome |string | |
| clientePerformance [0] networkConnection.value |número inteiro |Tempo demorou a estabelecer uma ligação de rede. |
| clientePerformance [0] receberRequest.value |número inteiro |Tempo a partir do final do envio do pedido para receber o HTML em resposta. |
| clientePerformance [0] enviarRequest.value |número inteiro |Tempo de tomada para enviar o pedido HTTP. |
| clientePerformance [0] total.valor |número inteiro |Hora de começar a enviar o pedido para exibir a página. |
| clientePerformance [0] url |string |URL deste pedido |
| clientePerformance [0] urlData.base |string | |
| clientePerformance [0] urlData.hashTag |string | |
| clientePerformance [0] urlData.host |string | |
| clientePerformance [0] urlData.protocol |string | |

## <a name="page-views"></a>Visualizações da Página
Enviado por trackPageView() ou [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Caminho | Tipo | Notas |
| --- | --- | --- |
| ver [0] contagem |número inteiro |100/(taxa[de amostragem).](../../azure-monitor/app/sampling.md) Por exemplo&gt; 4 = 25%. |
| ver [0] duraçãoMetric.value |número inteiro |Valor opcionalmente definido na trackPageView() ou por startTrackPage() - stopTrackPage(). Não é o mesmo que os valores de desempenho do cliente. |
| ver [0] nome |string |Título da página.  Comprimento máximo 250 |
| ver [0] url |string | |
| ver [0] urlData.base |string | |
| ver [0] urlData.hashTag |string | |
| ver [0] urlData.host |string | |

## <a name="availability"></a>Disponibilidade
Relatórios de disponibilidade de [testes web](../../azure-monitor/app/monitor-web-app-availability.md).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| disponibilidade [0] availabilityMetric.name |string |disponibilidade |
| disponibilidade [0] disponibilidadeMetric.value |número |1.0 ou 0.0 |
| disponibilidade [0] contagem |número inteiro |100/(taxa[de amostragem).](../../azure-monitor/app/sampling.md) Por exemplo&gt; 4 = 25%. |
| disponibilidade [0] dataSizeMetric.name |string | |
| disponibilidade [0] dadosTamanhoMétrico.valor |número inteiro | |
| disponibilidade [0] durationMetric.name |string | |
| disponibilidade [0] duraçãoMetric.value |número |Duração do teste. 1e7==1s |
| mensagem de disponibilidade [0] |string |Diagnóstico de falha |
| resultado da disponibilidade [0] |string |Aprovado/Reprovado |
| disponibilidade [0] runLocation |string |Geo fonte de http req |
| disponibilidade [0] testName |string | |
| disponibilidade [0] testeRunId |string | |
| disponibilidade [0] testTimestamp |string | |

## <a name="metrics"></a>Métricas
Gerado por TrackMetric().

O valor métrico encontra-se no contexto.custom.metrics[0]

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

## <a name="about-metric-values"></a>Sobre valores métricos
Os valores métricos, tanto em relatórios métricos como noutros locais, são relatados com uma estrutura padrão de objetos. Por exemplo:

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

Atualmente - embora isso possa mudar no futuro - em todos os `count==1` valores `name` `value` reportados a partir dos módulos SDK padrão, e apenas os campos e campos são úteis. O único caso em que seriam diferentes seria se escrevesses as tuas próprias chamadas TrackMetric em que definiste os outros parâmetros.

O objetivo dos outros campos é permitir que as métricas sejam agregadas no SDK, para reduzir o tráfego para o portal. Por exemplo, pode fazer uma média de várias leituras sucessivas antes de enviar cada relatório métrico. Em seguida, calcularia o min, máximo, desvio padrão e valor agregado (soma ou média) e definiria a contagem para o número de leituras representadas pelo relatório.

Nas tabelas acima, omitimos a contagem de campos raramente utilizados, min, max, stdDev e sampledValue.

Em vez de métricas pré-agregasantes, pode utilizar [amostras](../../azure-monitor/app/sampling.md) se precisar de reduzir o volume de telemetria.

### <a name="durations"></a>Durações
Salvo indicação em contrário, as durações são representadas em décimos de microsegundo, de modo que 100000000.0 significa 1 segundo.

## <a name="see-also"></a>Consulte também
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Exportação Contínua](export-telemetry.md)
* [Amostras de código](export-telemetry.md#code-samples)
