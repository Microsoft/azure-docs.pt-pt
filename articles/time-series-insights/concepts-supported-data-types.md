---
title: Tipos de dados suportados - Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre os tipos de dados suportados na Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: b0536a152797d17cba0930b3a142a7fb92eaf5ea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685162"
---
# <a name="supported-data-types"></a>Tipos de dados suportados

A tabela que se segue lista os tipos de dados suportados pela Azure Time Series Insights Gen2

| Tipo de dados | Descrição | Exemplo | [Sintaxe de expressão de série de tempo](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Nome da coluna de propriedade em Parquet
|---|---|---|---|---|
| **bool** | Um tipo de dados com um de dois estados: `true` ou `false` . | `"isQuestionable" : true` | `$event.isQuestionable.Bool` ou `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | Representa um instante no tempo, tipicamente expresso como data e hora do dia. Expresso no formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) As propriedades da data são sempre armazenadas em formato UTC. As compensações do fuso horário, se for em formatada corretamente, serão aplicadas e, em seguida, as valorizadas armazenadas na UTC. Consulte [esta](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) secção para obter mais informações sobre a propriedade do timetamp do ambiente e as compensações de datas | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Se "eventProcessedLocalTime" for o tempo de origem do evento: `$event.$ts` . Se for outra propriedade JSON: `$event.eventProcessedLocalTime.DateTime` ou `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **duplo** | Um número de 64 bits de precisão dupla  | `"value": 31.0482941` | `$event.value.Double` ou `$event['value'].Double` |  `value_double`
| **longo** | Um inteiro assinado de 64 bits  | `"value" : 31` | `$event.value.Long` ou `$event['value'].Long` |  `value_long`
| **string** | Os valores de texto devem consistir em UTF-8 válido. Cordas nulas e vazias são tratadas da mesma forma. |  `"site": "DIM_MLGGG"`| `$event.site.String` ou `$event['site'].String`| `site_string`
| **dynamic** | Um tipo complexo (não primitivo) que consiste num conjunto ou num saco de propriedade (dicionário). Atualmente apenas os conjuntos de JSON de primitivos ou matrizes de objetos que não contenham a propriedade TS ID ou timetamp(ies) serão armazenados como dinâmicos. Leia este [artigo](./concepts-json-flattening-escaping-rules.md) para entender como os objetos serão achatados e as matrizes podem ser desenroladas. As propriedades de carga útil armazenadas como este tipo só são acessíveis selecionando `Explore Events` no Time Series Insights Explorer para visualizar eventos crus, ou através da API de consulta para análise do lado do [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) cliente. |  `"values": "[197, 194, 189, 188]"` | Referenciar tipos dinâmicos numa Expressão de Séries Temporizadas ainda não está suportado | `values_dynamic`

> [!NOTE]
> 64 bits de valores inteiros são suportados, mas o maior número que o Azure Time Series Insights Explorer pode expressar com segurança é de 9.007.199.254.740.991 (2^53-1) devido às limitações do JavaScript. Se trabalhar com números no seu modelo de dados acima deste, pode reduzir o tamanho criando uma [variável modelo série de tempo](./concepts-variables.md#numeric-variables) e [convertendo](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) o valor.

> [!NOTE]
> **O** tipo de corda não é anulado:
>
> * Uma [expressão de série de tempo (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) expressa numa consulta da Série De [tempo](/rest/api/time-series-insights/reference-query-apis) comparando o valor de uma corda vazia **(''** contra **NUL** comportar-se-á da mesma forma: `$event.siteid.String = NULL` é equivalente a `$event.siteid.String = ''` .
> * A API pode devolver valores **NUOS** mesmo que os eventos originais contenham cordas vazias.
> * Não tome dependência dos valores **NUOS** nas colunas **de cordas** para fazer comparações ou avaliações, tratá-los da mesma forma que as cordas vazias.

## <a name="sending-mixed-data-types"></a>Envio de tipos de dados mistos

O seu ambiente Azure Time Series Insights Gen2 é fortemente dactilografado. Se dispositivos ou tags enviarem dados de diferentes tipos para uma propriedade do dispositivo, os valores serão armazenados em duas colunas separadas e a [função coalesce()](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) deve ser usada ao definir as suas expressões variáveis de modelo de série de tempo em chamadas API.

O Azure Time Series Insights Explorer oferece uma forma de se colidência automática das colunas separadas da propriedade do mesmo dispositivo. No exemplo abaixo, o sensor envia uma `PresentValue` propriedade que pode ser tanto longa como dupla. Para consultar todos os valores armazenados (independentemente do tipo de dados) da `PresentValue` propriedade, escolha `PresentValue (Double | Long)` e as colunas serão acolhidas para si.

[![Coalesce de automóveis do Explorador](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objetos e matrizes

Pode enviar tipos complexos, tais como objetos e matrizes, como parte da carga útil do evento. Os objetos aninhados serão achatados e as matrizes serão armazenadas `dynamic` ou achatadas para produzir múltiplos eventos dependendo da configuração do ambiente e da forma JSON. Para saber mais sobre as [Regras de Achatamento e Fuga](./concepts-json-flattening-escaping-rules.md) da JSON

## <a name="next-steps"></a>Passos seguintes

* Leia o [JSON achatando e escapando às regras](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados.

* Compreenda as [limitações](./concepts-streaming-ingress-throughput-limits.md) de produção do seu ambiente

* Saiba mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para ingerir dados de streaming.
