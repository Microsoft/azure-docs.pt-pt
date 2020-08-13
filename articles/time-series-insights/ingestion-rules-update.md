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
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168206"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Próximas alterações ao JSON achatando e escapando às regras para novos ambientes

**Estas alterações serão aplicadas apenas aos ambientes da Azure Time Series Insights Gen2 *recém-criados.* Estas alterações não se aplicam aos ambientes da Gen1.**

O seu ambiente Azure Time Series Insights Gen2 cria dinamicamente as suas colunas de armazenamento, seguindo um conjunto particular de convenções de nomeação. Quando um evento é ingerido, um conjunto de regras é aplicado à carga útil JSON e nomes de propriedade. As alterações à forma como os dados da JSON são achatados e armazenados entrarão em vigor para novos ambientes Azure Time Series Insights Gen2 em julho de 2020. Esta alteração tem impacto nos seguintes casos:

* Se a sua carga JSON contiver objetos aninhados
* Se a sua carga útil JSON contiver matrizes
* Se utilizar um dos seguintes quatro caracteres especiais num nome de propriedade JSON: \ . '
* Se uma ou mais das suas propriedades de ID TS estiverem dentro de um objeto aninhado.

Se criar um novo ambiente e um ou mais dos casos acima se aplicam à carga útil do evento, verá os seus dados achatados e armazenados de forma diferente. Abaixo está um resumo das alterações:

| Regra atual | Nova Regra | Exemplo JSON | Nome da coluna anterior | Nome da nova coluna
|---|---| ---| ---|  ---|
| Aninhado JSON é achatado usando um sublinhado como o delineador |Aninhado JSON é achatado usando um período como delineador  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Personagens especiais não escapam | Nomes de propriedade JSON que incluem os caracteres especiais. [ \ e ' são escapadas usando [' e ']. Dentro de [' e '] há escapatória adicional de citações individuais e backslashes. Uma única citação será escrita como \' e um backslash será escrito como\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Matrizes de primitivos são armazenados como uma corda | Matrizes de tipos primitivos são armazenados como um tipo dinâmico  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Matrizes de objetos são sempre achatados, produzindo vários eventos | Se os objetos dentro de uma matriz não tiverem o ID TS ou o timetamp propert(ies), a matriz de objetos é armazenada inteiramente como um tipo dinâmico | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Alterações recomendadas para novos ambientes

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se a sua propriedade TS ID e/ou timetamp estiver aninhada dentro de um objeto

* Quaisquer novas implementações terão de corresponder às novas regras de ingestão. Por exemplo, se o seu ID TS `telemetry_tagId` for, terá de atualizar quaisquer modelos do Azure Resource Manager (ARM) ou de implementar scripts automatizados para configurar `telemetry.tagId` como o ambiente TS ID. Esta mudança é necessária para os tempos de origem do evento também em JSON aninhado.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se a sua carga útil contiver JSON aninhado ou caracteres especiais e automatizar expressões variáveis [do Modelo de Séries](.\time-series-insights-update-tsm.md) de Tempo

* Atualize o código de execução do seu cliente [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) para corresponder às novas regras de ingestão. Por exemplo, uma expressão anterior da Série de [Tempo](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) deve ser atualizada para uma `"value": {"tsx": "$event.series_value.Double"}` das opções abaixo:
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Passos seguintes

* Leia [Azure Time Series Insights Armazenamento e entrada da Gen2](./time-series-insights-update-storage-ingress.md).

* Leia mais sobre como consultar os seus dados utilizando [APIs de consulta de séries de tempo](./concepts-query-overview.md).

* Leia mais sobre a [nova sintaxe de expressão da série de tempo](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).
