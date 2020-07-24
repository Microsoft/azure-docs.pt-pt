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
ms.date: 07/07/2020
ms.openlocfilehash: c2e70a4f5cdbbc7a5a408138c3ec832cc831cf33
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046580"
---
# <a name="supported-data-types"></a>Tipos de dados suportados

A tabela que se segue lista os tipos de dados suportados pela Azure Time Series Insights Gen2

| Tipo de dados | Descrição | Exemplo | Nome da coluna de propriedade em Parquet
|---|---|---|---|
| **bool** | Um tipo de dados com um de dois estados: `true` ou `false` . | "isQuestionable": verdadeiro | isQuestionable_bool
| **data** | Representa um instante no tempo, tipicamente expresso como data e hora do dia. Expresso no formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) As propriedades da data são sempre armazenadas em formato UTC. As compensações do fuso horário, se for em formatada corretamente, serão aplicadas e, em seguida, as valorizadas armazenadas na UTC. Consulte [esta](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) secção para obter mais informações sobre a propriedade do timetamp do ambiente e as compensações de datas | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z" | eventProcessedLocalTime_datetime 
| **duplo** | Um número de 64 bits de precisão dupla  | "Valor": 31.0482941 | value_double
| **long** | Um inteiro assinado de 64 bits  | "Valor": 31 | value_long
| **cadeia** | Os valores de texto devem consistir em UTF-8 válido. |  "site": "DIM_MLGGG" | site_string
| **dynamic** | Um tipo complexo (não primitivo) que consiste num conjunto ou num saco de propriedade (dicionário). Atualmente, apenas as matrizes de JSON de primitivos ou matrizes de objetos que não contenham o ID TS ou o timetamp(ies) serão armazenados como dinâmicos. Leia este [artigo](./concepts-json-flattening-escaping-rules.md) para entender como os objetos serão achatados e as matrizes podem não ser enroladas. As propriedades de carga útil armazenadas como este tipo são acessíveis através do Azure Time Series Insights Gen2 Explorer e da GetEvents Query API. |  "Valores": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * O seu ambiente Azure Time Series Insights Gen2 é fortemente dactilografado. Se os dispositivos ou tags enviarem dados integrais e não-culturais, os valores de propriedade do dispositivo serão armazenados em duas colunas duplas e longas separadas e a [função coalesce()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) deve ser utilizada ao fazer chamadas API e definir as suas expressões variáveis de modelo de séries de tempo.

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Pode enviar tipos complexos, tais como objetos e matrizes, como parte da carga útil do evento. Os objetos aninhados serão achatados e as matrizes serão armazenadas `dynamic` ou achatadas para produzir múltiplos eventos dependendo da configuração do ambiente e da forma JSON. Para saber mais sobre as [Regras de Achatamento e Fuga](./concepts-json-flattening-escaping-rules.md) da JSON

## <a name="next-steps"></a>Passos seguintes

* Leia o [JSON achatando e escapando às regras](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados. 

* Compreenda as [limitações](./concepts-streaming-ingress-throughput-limits.md) de produção do seu ambiente

* Saiba mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para ingerir dados de streaming.
