---
title: Tipos de dados suportados - Azure Time Series Insights / Microsoft Docs
description: Saiba mais sobre os tipos de dados suportados na pré-visualização do Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050104"
---
# <a name="supported-data-types"></a>Tipos de dados suportados

A tabela que se segue lista os tipos de dados suportados pela Time Series Insights

| Tipo de dados | Descrição | Exemplo | Nome da coluna de propriedade em Parquet
|---|---|---|---|
| **bool** | Um tipo de dados com um de dois estados: `true` ou `false` . | "isQuestionable": verdadeiro | isQuestionable_bool
| **datetime** | Representa um instante no tempo, tipicamente expresso como data e hora do dia. Expresso no formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) As propriedades da data são sempre armazenadas em formato UTC. As compensações do fuso horário, se for em formatada corretamente, serão aplicadas e, em seguida, as valorizadas armazenadas na UTC. Consulte [esta](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) secção para obter mais informações sobre a propriedade do timetamp do ambiente e as compensações de datas | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z" | eventProcessedLocalTime_datetime 
| **double** | Um número de 64 bits de precisão dupla  | "Valor": 31.0482941 | value_double
| **long** | Um inteiro assinado de 64 bits  | "Valor": 31 | value_long
| **string** | Os valores de texto devem consistir em UTF-8 válido. |  "site": "DIM_MLGGG" | site_string
| **dynamic** | Um tipo complexo (não primitivo) que consiste num conjunto ou num saco de propriedade (dicionário). Atualmente, apenas as matrizes de JSON de primitivos ou matrizes de objetos que não contenham o ID TS ou o timetamp(ies) serão armazenados como dinâmicos. Leia este [artigo](./concepts-json-flattening-escaping-rules.md) para entender como os objetos serão achatados e matrizes podem ser não enrolados |  "Valores": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * O seu ambiente de TSI é fortemente dactilografado. Se os dispositivos ou tags enviarem dados integrais e não-culturais, os valores de propriedade do dispositivo serão armazenados em duas colunas duplas e longas separadas e a [função coalesce()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) deve ser utilizada ao fazer chamadas API e definir as suas expressões variáveis de modelo de séries de tempo.

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Pode enviar tipos complexos, tais como objetos e matrizes, como parte da carga útil do evento. Os objetos aninhados serão achatados e as matrizes serão armazenadas `dynamic` ou achatadas para produzir múltiplos eventos dependendo da configuração do ambiente e da forma JSON. Para saber mais sobre as [Regras de Achatamento e Fuga](./concepts-json-flattening-escaping-rules.md) da JSON

## <a name="next-steps"></a>Próximos passos

* Leia o [JSON achatando e escapando às regras](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados. 

* Compreenda as [limitações](concepts-streaming-throughput-limitations.md) de produção do seu ambiente

* Saiba mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para ingerir dados de streaming.
