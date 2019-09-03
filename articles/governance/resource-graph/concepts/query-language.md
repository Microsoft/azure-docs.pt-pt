---
title: Entender a linguagem de consulta
description: Descreve os operadores Kusto e funções disponíveis utilizáveis com o grafo de recursos do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231508"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do grafo de recursos do Azure

A linguagem de consulta para o grafo de recursos do Azure dá suporte a vários operadores e funções. Cada trabalho e opera com base no [Azure data Explorer](../../../data-explorer/data-explorer-overview.md).

A melhor maneira de aprender sobre a linguagem de consulta usada pelo grafo de recursos é começar com a documentação para a [linguagem de consulta](/azure/kusto/query/index)de data Explorer do Azure. Ele fornece uma compreensão sobre como a linguagem é estruturada e como os vários operadores e funções com suporte funcionam em conjunto.

## <a name="supported-tabular-operators"></a>Operadores de tabela com suporte

Aqui está a lista de operadores de tabela com suporte no grafo de recursos:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [ordenar por](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [classificar por](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [Início](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [Hitters superior](/azure/kusto/query/tophittersoperator)
- [posição](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funções com suporte

Aqui está a lista de funções com suporte no grafo de recursos:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Caracteres de escape

Alguns nomes de propriedade, como aqueles que incluem um `.` ou `$`, devem ser encapsulados ou ter escape na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.`-Encapsular o nome da propriedade como tal:`['propertyname.withaperiod']`
  
  Exemplo de consulta que encapsula a propriedade _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Escape o caractere no nome da propriedade. O caractere de escape usado depende do grafo de recursos do Shell em execução.

  - **bash** - `\`

    Exemplo de consulta que escapa o  _\$tipo_ de propriedade no bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -não escapar do `$` caractere.

  - **PowerShell** - ``` ` ```

    Exemplo de consulta que escapa o  _\$tipo_ de propriedade no PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passos Seguintes

- Consulte o idioma em uso em [consultas de início](../samples/starter.md)
- Consulte usos avançados em [consultas avançadas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)