---
title: Azure Application Insights Data Model | Microsoft Docs
description: Descreve propriedades exportadas a partir de exportação contínua em JSON, e usadas como filtros.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: b4609d54c1c3c33a654dd58a3bceaca4974fda15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584215"
---
# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação de insights de aplicação
Esta tabela lista as propriedades da telemetria enviadas dos SDKs [application insights](./app-insights-overview.md) para o portal.
Você verá estas propriedades na produção de dados da [Exportação Contínua.](export-telemetry.md)
Também aparecem em filtros de propriedade no [Metric Explorer](../essentials/metrics-charts.md) e na Pesquisa [de Diagnóstico.](./diagnostic-search.md)

Pontos a notar:

* `[0]` nestas tabelas denota um ponto no caminho onde é preciso inserir um índice; mas nem sempre é 0.
* As durações do tempo são em décimos de um microsegundo, então 10000000 == 1 segundo.
* As datas e os horários são UTC, e são dadas no formato ISO `yyyy-MM-DDThh:mm:ss.sssZ`

## <a name="example"></a>Exemplo

```json
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
```

## <a name="context"></a>Contexto
Todos os tipos de telemetria são acompanhados por uma secção de contexto. Nem todos estes campos são transmitidos com cada ponto de dados.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| context.custom.dimensions [0] |objeto [ ] |Pares de cordas de valor chave definidos pelo parâmetro de propriedades personalizadas. Comprimento máximo da chave 100, valores máximos comprimento 1024. Mais de 100 valores únicos, o imóvel pode ser pesquisado mas não pode ser usado para segmentação. Max 200 chaves por ikey. |
| context.custom.metrics [0] |objeto [ ] |Pares de valor-chave definidos pelo parâmetro de medição personalizada e pela TrackMetrics. O comprimento máximo da chave 100, os valores podem ser numéricos. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |boolean |O pedido parece vir de um teste de bot ou web. |
| context.data.samplingRate |número |Percentagem de telemetria gerada pelo SDK que é enviado para o portal. Intervalo 0.0-100.0. |
| contexto.dispositivo |objeto |Dispositivo de cliente |
| context.device.browser |string |IE, Chrome, ... |
| context.device.browserVersion |string |Cromado 48.0, ... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| context.device.locale |string |en-GB, de-DE, ... |
| contexto.device.network |string | |
| context.device.oemName |string | |
| contexto.device.os |string | |
| context.device.osVersion |string |SO Anfitrião |
| context.device.roleInstance |string |ID do anfitrião do servidor |
| context.device.roleName |string | |
| context.device.screenResolution |string | |
| contexto.dispositivo.tipo |string |PC, Browser, ... |
| contexto.localização |objeto |Derivado `clientip` de. |
| contexto.localização.cidade |string |Derivado `clientip` de, se conhecido |
| contexto.location.clientip |string |O último octógono é anonimizado para 0. |
| contexto.localização.continente |string | |
| contexto.localização.país |string | |
| contexto.localização.província |string |Estado ou província |
| context.operation.id |string |Os itens que têm o mesmo `operation id` são mostrados como Itens Relacionados no portal. Normalmente o `request id` . |
| context.operation.name |string |url ou nome de pedido |
| context.operation.parentId |string |Permite itens relacionados com aninhados. |
| context.session.id |string |`Id` de um grupo de operações da mesma fonte. Um período de 30 minutos sem uma operação assinala o fim de uma sessão. |
| context.session.isFirst |boolean | |
| context.user.accountAcquisitionDate |string | |
| context.user.accountD |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Utilizador autenticado](./api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |string | |
| context.user.isAuthenticated |boolean | |
| context.user.storeRegion |string | |
| internal.data.documa vez que a uma vez |string | |
| internal.data.id |string | `Unique id` que é atribuído quando um item é ingerido para Insights de Aplicação |

## <a name="events"></a>Eventos
Eventos personalizados gerados pelo [TrackEvent()](./api-custom-events-metrics.md#trackevent).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| evento [0] contagem |número inteiro |100/(taxa[de amostragem).](./sampling.md) Por exemplo 4 = &gt; 25%. |
| evento [0] nome |string |Nome do evento.  Comprimento máximo 250. |
| evento [0] url |string | |
| evento [0] urlData.base |string | |
| evento [0] urlData.host |string | |

## <a name="exceptions"></a>Exceções
Reporta [exceções](./asp-net-exceptions.md) no servidor e no navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| básicoExcepção [0] montagem |string | |
| contagem basicException [0] |número inteiro |100/(taxa[de amostragem).](./sampling.md) Por exemplo 4 = &gt; 25%. |
| basicException [0] exceçãoGrupo |string | |
| basicException [0] excepçãoType |string | |
| basicException [0] failedUserCodeMethod |string | |
| basicException [0] falhou A Desmontagem de Código de Código |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |boolean | |
| básicoExcepção [0] `id` |string | |
| método basicException [0] |string | |
| basicException [0] mensagem |string |Mensagem de exceção. Comprimento máximo 10k. |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| basicException [0] outerExceptionType |string | |
| basicExcepção [0] outerId |string | |
| básicoExcepção [0] parsedStack [0] montagem |string | |
| basicException [0] parsedStack [0] fileName |string | |
| básicoExcepção [0] parsedStack [0] nível |número inteiro | |
| basicException [0] parsedStack [0] linha |número inteiro | |
| método basicException [0] parsedStack [0] |string | |
| stack basicException [0] |string |Comprimento máximo 10k |
| básicoExcepção [0] nome do tipoName |string | |

## <a name="trace-messages"></a>Mensagens de rastreio
Enviado por [TrackTrace](./api-custom-events-metrics.md#tracktrace), e pelos [adaptadores de registo.](./asp-net-trace-logs.md)

| Caminho | Tipo | Notas |
| --- | --- | --- |
| mensagem [0] nome do madeireiro |string | |
| parâmetros de mensagem [0] |string | |
| mensagem [0] crua |string |A mensagem de registo, comprimento máximo de 10k. |
| mensagem [0] severidadeLevel |string | |

## <a name="remote-dependency"></a>Dependência remota
Enviado pela TrackDependency. Usado para reportar desempenho e uso de [chamadas para dependências](./asp-net-dependencies.md) no servidor, e AJAX chama no navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| remotoDependência [0] async |boolean | |
| remotoDependência [0] nome baseName |string | |
| remotoDependência [0] comandoName |string |Por exemplo, "casa/índice" |
| contagem remotadependência [0] |número inteiro |100/(taxa[de amostragem).](./sampling.md) Por exemplo 4 = &gt; 25%. |
| remotoDependência [0] dependênciaTypeName |string |HTTP, SQL, ... |
| remotaDependência [0] duraçãoMetric.value |número |Tempo da chamada à conclusão da resposta por dependência |
| independência remota [0] `id` |string | |
| nome remotoDependência [0] |string |Url. Comprimento máximo 250. |
| controlo remotoDependência [0] resultadosDesco |string |da dependência http |
| sucesso remotoDependência [0] |boolean | |
| tipo remotoDependência [0] |string |Http, Sql,... |
| remotoDependência [0] url |string |Comprimento máximo 2000 |
| remotoDependência [0] urlData.base |string |Comprimento máximo 2000 |
| remotoDependência [0] urlData.hashTag |string | |
| remotoDependência [0] urlData.host |string |Comprimento máximo 200 |

## <a name="requests"></a>Pedidos
Enviado por [TrackRequest.](./api-custom-events-metrics.md#trackrequest) Os módulos padrão usam isto para reportar o tempo de resposta do servidor, medido no servidor.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| pedido [0] contagem |número inteiro |100/(taxa[de amostragem).](./sampling.md) Por exemplo: 4 = &gt; 25%. |
| pedido [0] duraçãoMétrico.valor |número |Tempo de pedido de resposta. 1e7 == 1s |
| pedido [0] `id` |string |`Operation id` |
| pedido [0] nome |string |GET/POST + base de url.  Comprimento máximo 250 |
| solicitar [0] respostaSCode |número inteiro |Resposta HTTP enviada ao cliente |
| solicitar [0] sucesso |boolean |Padrão == (resposta Código &lt; 400) |
| solicitar [0] url |string |Sem incluir o anfitrião |
| solicitar [0] urlData.base |string | |
| solicitar [0] urlData.hashTag |string | |
| solicitar [0] urlData.host |string | |

## <a name="page-view-performance"></a>Desempenho da visualização da página
Enviado pelo navegador. Mede o tempo para processar uma página, desde o utilizador que inicia o pedido de exibição completa (excluindo as chamadas ASYNC AJAX).

Os valores de contexto mostram o cliente OS e a versão do navegador.

| Caminho | Tipo | Notas |
| --- | --- | --- |
| clientePerformance [0] clienteProcesso.valor |número inteiro |Tempo desde o fim da receção do HTML até à visualização da página. |
| nome clientePerformance [0] |string | |
| clientePerformance [0] networkConnection.value |número inteiro |Tempo necessário para estabelecer uma ligação de rede. |
| clientePerformance [0] receberRequest.value |número inteiro |Tempo a partir do final do envio do pedido para receber o HTML em resposta. |
| clientePerformance [0] enviarRequest.value |número inteiro |Tempo de ser tirado para enviar o pedido HTTP. |
| clientePerformance [0] total.value |número inteiro |Tempo de começar a enviar o pedido para exibir a página. |
| clientePerformance [0] url |string |URL deste pedido |
| clientePerformance [0] urlData.base |string | |
| clientePerformance [0] urlData.hashTag |string | |
| clientePerformance [0] urlData.host |string | |
| clientePerformance [0] urlData.protocol |string | |

## <a name="page-views"></a>Visualizações da Página
Enviado por trackPageView() ou [stopTrackPage](./api-custom-events-metrics.md#page-views)

| Caminho | Tipo | Notas |
| --- | --- | --- |
| ver [0] contar |número inteiro |100/(taxa[de amostragem).](./sampling.md) Por exemplo 4 = &gt; 25%. |
| ver [0] duraçãoMetric.value |número inteiro |Valor opcionalmente definido no trackPageView() ou por startTrackPage() - stopTrackPage (). Não é o mesmo que os valores de desempenho do cliente. |
| ver [0] nome |string |Título da página.  Comprimento máximo 250 |
| ver [0] url |string | |
| ver [0] urlData.base |string | |
| ver [0] urlData.hashTag |string | |
| ver [0] urlData.host |string | |

## <a name="availability"></a>Disponibilidade
Relatórios [disponibilidade testes web](./monitor-web-app-availability.md).

| Caminho | Tipo | Notas |
| --- | --- | --- |
| disponibilidade [0] availabilityMetric.name |string |disponibilidade |
| disponibilidade [0] disponibilidadeMetric.value |número |1.0 ou 0.0 |
| disponibilidade [0] contagem |número inteiro |100/(taxa[de amostragem).](./sampling.md) Por exemplo 4 = &gt; 25%. |
| disponibilidade [0] dataSizeMetric.name |string | |
| disponibilidade [0] dataSizeMetric.value |número inteiro | |
| disponibilidade [0] durationMetric.name |string | |
| disponibilidade [0] duraçãoMetric.value |número |Duração do teste. 1e7==1s |
| disponibilidade [0] mensagem |string |Diagnóstico de falha |
| resultado da disponibilidade [0] |string |Aprovado/Reprovado |
| disponibilidade [0] runLocation |string |Fonte geo de http req |
| disponibilidade [0] testName |string | |
| disponibilidade [0] testRunId |string | |
| disponibilidade [0] testTimestamp |string | |

## <a name="metrics"></a>Métricas
Gerado por TrackMetric().

O valor métrico encontra-se em context.custom.metrics[0]

Por exemplo:

```json
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
      ]  
    }
  }
}
```

## <a name="about-metric-values"></a>Sobre valores métricos
Os valores métricos, tanto em relatórios métricos como noutros locais, são reportados com uma estrutura padrão de objetos. Por exemplo:

```json
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
```

Atualmente - embora isso possa mudar no futuro - em todos os valores reportados a partir dos módulos SDK padrão, `count==1` e apenas os `name` campos e campos são `value` úteis. O único caso em que seriam diferentes seria se escrevesses as tuas próprias chamadas TrackMetric nas quais defines os outros parâmetros.

O objetivo dos outros campos é permitir que as métricas sejam agregadas no SDK, para reduzir o tráfego para o portal. Por exemplo, pode ter uma média de várias leituras sucessivas antes de enviar cada relatório métrico. Em seguida, calcularia o min, máx, desvio padrão e valor agregado (soma ou média) e definiria a contagem para o número de leituras representadas pelo relatório.

Nas tabelas acima, omitimos a contagem de campos raramente usados, min, max, stdDev e sampledValue.

Em vez de métricas pré-agregantes, pode utilizar [a amostragem](./sampling.md) se precisar de reduzir o volume de telemetria.

### <a name="durations"></a>Durações
Salvo indicação em contrário, as durações são representadas em décimos de um microsegundo, de modo que 100000000000 significa 1 segundo.

## <a name="see-also"></a>Ver também
* [Application Insights](./app-insights-overview.md)
* [Exportação Contínua](export-telemetry.md)
* [Amostras de código](export-telemetry.md#code-samples)

