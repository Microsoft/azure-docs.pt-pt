---
title: Próximas alterações às regras de ingestão e achatamento em Azure Time Series Insights Gen2 Microsoft Docs
description: Alterações na regra da ingestão
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016722"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Próximas alterações ao Achatamento de JSON e fuga às regras para novos ambientes

> [!IMPORTANT]
> Estas alterações serão aplicadas apenas aos ambientes do Microsoft Azure Time Series Insights Gen2 *recém-criados.* As mudanças não se aplicam aos ambientes da Gen1.

O seu ambiente Azure Time Series Insights Gen2 cria dinamicamente as suas colunas de armazenamento, seguindo um conjunto particular de convenções de nomeação. Quando um evento é ingerido, a Time Series Insights aplica um conjunto de regras à carga útil json e nomes de propriedade. Alterações na forma como os dados da JSON são achatados e armazenados entraram em vigor para novos ambientes Azure Time Series Insights Gen2 em julho de 2020. Esta alteração tem impacto nos seguintes casos:

* A sua carga JSON contém objetos aninhados.
* A sua carga JSON contém matrizes.
* Você usa qualquer um dos quatro caracteres especiais seguintes em um nome de propriedade JSON: `[` `\` `.``'`
* Uma ou mais propriedades de ID da Série De Tempo (TS) estão dentro de um objeto aninhado.

Se criar um novo ambiente e um ou mais destes casos se aplicarem à carga útil do seu evento, os seus dados serão achatados e armazenados de forma diferente. O quadro que se segue resume as alterações:

| Regra atual | Nova regra | Exemplo JSON | Nome da coluna anterior | Nome da coluna nova
|---|---| ---| ---|  ---|
| O JSON aninhado é achatado utilizando um sublinhado como delineador. |O JSON aninhado é achatado utilizando um período como delineador.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Personagens especiais não escapam. | Nomes de propriedade JSON que incluem os caracteres `.` `[`   `\` especiais e `'` são escapados usando `['` e `']` . Dentro `['` e , há `']` escapatória adicional de citações individuais e costas. Uma única citação será escrita como `\'` e um retrocesso como `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Matrizes de primitivos são armazenadas como uma corda. | As matrizes de tipos primitivos são armazenadas como um tipo dinâmico.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Conjuntos de objetos são sempre achatados, produzindo múltiplos eventos. | Se os objetos dentro de uma matriz não tiverem as propriedades de identificação de TS ou de hora, a matriz de objetos é armazenada inteira como um tipo dinâmico. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Alterações recomendadas para novos ambientes

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se a sua propriedade TS ID e/ou timetamp estiver aninhada dentro de um objeto

Quaisquer novas implementações têm de corresponder às novas regras de ingestão. Por exemplo, se o seu ID TS `telemetry_tagId` estiver, precisa atualizar quaisquer modelos do Azure Resource Manager ou enviar scripts automatizados para configurar `telemetry.tagId` como o ID de ID de ambiente. Você também precisa desta mudança para os tempos de origem do evento em JSON aninhado.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se a sua carga útil contiver JSON aninhado ou caracteres especiais e automatizar expressões variáveis [do Modelo de Séries](./concepts-model-overview.md) de Tempo

Atualize o código do cliente que executa [o TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) para corresponder às novas regras de ingestão. Por exemplo, deve atualizar uma expressão anterior da [Série de Tempo](/rest/api/time-series-insights/reference-time-series-expression-syntax) de uma das `"value": {"tsx": "$event.series_value.Double"}` seguintes opções:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre o armazenamento e entrada da [Azure Time Series Insights Gen2](./concepts-ingestion-overview.md).

* Saiba como consultar os seus dados utilizando [APIs de consulta de séries de tempo](./concepts-query-overview.md).

* Leia mais sobre a [nova sintaxe de expressão da série de tempo](/rest/api/time-series-insights/reference-time-series-expression-syntax).