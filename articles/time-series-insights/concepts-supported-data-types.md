---
title: Tipos de dados suportados - Azure Time Series Insights Gen2 Microsoft Docs
description: Saiba mais sobre os tipos de dados suportados na Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170876"
---
# <a name="supported-data-types"></a>Tipos de dados suportados

A tabela que se segue lista os tipos de dados suportados pela Azure Time Series Insights Gen2

| Tipo de dados | Descrição | Exemplo | Nome da coluna de propriedade em Parquet
|---|---|---|---|
| **bool** | Um tipo de dados com um de dois estados: `true` ou `false` . | `"isQuestionable" : true` | isQuestionable_bool
| **data** | Representa um instante no tempo, tipicamente expresso como data e hora do dia. Expresso no formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) As propriedades da data são sempre armazenadas em formato UTC. As compensações do fuso horário, se for em formatada corretamente, serão aplicadas e, em seguida, as valorizadas armazenadas na UTC. Consulte [esta](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) secção para obter mais informações sobre a propriedade do timetamp do ambiente e as compensações de datas | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **duplo** | Um número de 64 bits de precisão dupla  | `"value": 31.0482941` | value_double
| **longo** | Um inteiro assinado de 64 bits  | `"value" : 31` | value_long
| **cadeia** | Os valores de texto devem consistir em UTF-8 válido. Cordas nulas e vazias são tratadas da mesma forma. |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | Um tipo complexo (não primitivo) que consiste num conjunto ou num saco de propriedade (dicionário). Atualmente apenas os conjuntos de JSON de primitivos ou matrizes de objetos que não contenham a propriedade TS ID ou timetamp(ies) serão armazenados como dinâmicos. Leia este [artigo](./concepts-json-flattening-escaping-rules.md) para entender como os objetos serão achatados e as matrizes podem ser desenroladas. As propriedades de carga útil armazenadas como este tipo são acessíveis através do Azure Time Series Insights Gen2 Explorer e da `GetEvents`   API de Consulta. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Envio de tipos de dados mistos

O seu ambiente Azure Time Series Insights Gen2 é fortemente dactilografado. Se dispositivos ou tags enviarem dados de diferentes tipos para uma propriedade do dispositivo, os valores serão armazenados em duas colunas separadas e a [função coalesce()](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions) deve ser usada ao definir as suas expressões variáveis de modelo de série de tempo em chamadas API.

O Azure Time Series Insights Explorer oferece uma forma de se colidência automática das colunas separadas da propriedade do mesmo dispositivo. No exemplo abaixo, o sensor envia uma `PresentValue` propriedade que pode ser tanto longa como dupla. Para consultar todos os valores armazenados (independentemente do tipo de dados) da `PresentValue` propriedade, escolha `PresentValue (Double | Long)` e as colunas serão acolhidas para si.

[![Coalesce de automóveis do Explorador](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objetos e matrizes

Pode enviar tipos complexos, tais como objetos e matrizes, como parte da carga útil do evento. Os objetos aninhados serão achatados e as matrizes serão armazenadas `dynamic` ou achatadas para produzir múltiplos eventos dependendo da configuração do ambiente e da forma JSON. Para saber mais sobre as [Regras de Achatamento e Fuga](./concepts-json-flattening-escaping-rules.md) da JSON

## <a name="next-steps"></a>Passos seguintes

* Leia o [JSON achatando e escapando às regras](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados.

* Compreenda as [limitações](./concepts-streaming-ingress-throughput-limits.md) de produção do seu ambiente

* Saiba mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para ingerir dados de streaming.
