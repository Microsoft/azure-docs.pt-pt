---
title: Compreender a linguagem de consulta
description: Descreve os operadores de Kusto disponíveis e as funções ser utilizáveis com o gráfico de recursos do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800515"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do gráfico de recursos do Azure

A linguagem de consulta para o gráfico de recursos do Azure suporta um número de operadores e funções. Cada trabalho e operasse com base em [Explorador de dados do Azure](../../../data-explorer/data-explorer-overview.md).

A melhor forma de saber mais sobre a linguagem de consulta usada pelo gráfico de recursos é começar com a documentação para o Azure Data Explorer [linguagem de consulta](/azure/kusto/query/index). Ele fornece uma compreensão sobre como o idioma é estruturado e como os vários suportados os operadores e funções funcionam em conjunto.

## <a name="supported-tabular-operators"></a>Operadores de tabela suportados

Eis a lista de operadores tabulares suportados no gráfico de recursos:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [Ordenar por](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [Ordenar por](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funções suportadas

Eis a lista de funções no gráfico de recursos:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Caracteres de escape

Alguns nomes de propriedade, como os que incluem uma `.` ou `$`, tem de ser encapsuladas ou escape da consulta ou a propriedade nome é interpretado incorretamente e não fornece os resultados esperados.

- `.` -Encapsule o nome da propriedade assim: `['propertyname.withaperiod']`
  
  Consulta de exemplo que encapsula num wrapper a propriedade _OData_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -No nome da propriedade caráter de escape. O caráter de escape utilizado depende o shell que gráfico do recurso é executado a partir.

  - **bash** - `\`

    Consulta de exemplo que escapes a propriedade  _\$tipo_ no bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -não saiam do `$` caráter.

  - **PowerShell** - ``` ` ```

    Consulta de exemplo que escapes a propriedade  _\$tipo_ no PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passos Seguintes

- Consulte o idioma utilizado na [consultas Starter](../samples/starter.md)
- Consulte avançada usa no [avançadas consultas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)