---
title: Compreenda a linguagem de consulta
description: Descreve as tabelas de Gráficos de Recursos e os tipos de dados, operadores e funções disponíveis do Kusto com o Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927486"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Compreender a linguagem de consulta do gráfico de recursos azure

A linguagem de consulta para o Gráfico de Recursos Azure suporta vários operadores e funções. Cada trabalho e opera com base na [Linguagem de Consulta Kusto (KQL)](/azure/kusto/query/index). Para saber sobre a linguagem de consulta usada pelo Resource Graph, comece com o [tutorial para KQL](/azure/kusto/query/tutorial).

Este artigo abrange os componentes linguísticos suportados pelo Resource Graph:

- [Tabelas de gráficos de recursos](#resource-graph-tables)
- [Elementos linguísticos KQL suportados](#supported-kql-language-elements)
- [Personagens de fuga](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelas de gráficos de recursos

O Resource Graph fornece várias tabelas para os dados que armazena sobre os tipos de recursos do Gestor de Recursos e as suas propriedades. Estas tabelas podem ser utilizadas com operadores `join` ou `union` para obter propriedades de tipos de recursos relacionados. Aqui está a lista de tabelas disponíveis no Graph de Recursos:

|Tabelas de gráficos de recursos |Descrição |
|---|---|
|Recursos |A tabela padrão se nenhuma definida na consulta. A maioria dos tipos e propriedades do Gestor de Recursos estão aqui. |
|Contentores de Recursos |Inclui tipos e dados de recursos de subscrição (em pré-visualização - `Microsoft.Resources/subscriptions`) e do grupo de recursos (`Microsoft.Resources/subscriptions/resourcegroups`). |
|Recursos Consultivos |Inclui recursos _relacionados com_ `Microsoft.Advisor`. |
|AlertasGeResources |Inclui recursos _relacionados com_ `Microsoft.AlertsManagement`. |
|Recursos de Manutenção |Inclui recursos _relacionados com_ `Microsoft.Maintenance`. |
|Recursos de Segurança |Inclui recursos _relacionados com_ `Microsoft.Security`. |

Para obter uma lista completa, incluindo tipos de recursos, consulte [Referência: Tabelas suportadas e tipos](../reference/supported-tables-resources.md)de recursos .

> [!NOTE]
> _Recursos_ é a tabela padrão. Enquanto consulta a tabela _Recursos,_ não é necessário fornecer o nome da mesa a menos que `join` ou `union` sejam usados. No entanto, a prática recomendada é incluir sempre a tabela inicial na consulta.

Utilize o Resource Graph Explorer no portal para descobrir que tipos de recursos estão disponíveis em cada tabela. Como alternativa, utilize uma consulta como `<tableName> | distinct type` para obter uma lista de tipos de recursos que a tabela do Gráfico de Recursos dada suporta que existem no seu ambiente.

A seguinte consulta mostra um simples `join`. O resultado da consulta mistura as colunas e quaisquer nomes de colunas duplicados da tabela unida, _Os Contentores_ de Recursos neste exemplo, são anexados com **1**. Como a tabela _ResourceContainers_ tem tipos para subscrições e grupos de recursos, qualquer tipo pode ser usado para aderir ao recurso a partir da tabela de _recursos._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A seguinte consulta mostra um uso mais complexo de `join`. A consulta limita a tabela unida aos recursos de subscrição e com `project` a incluir apenas a subscrição original do _campoId_ e o campo de _nome_ renomeado para _SubNome_. O nome de campo evita `join` adicioná-lo como _nome1_ uma vez que o campo já existe em _Recursos._ A mesa original é filtrada com `where` e a seguinte `project` inclui colunas de ambas as mesas. O resultado da consulta é um único tipo de abóbada chave, o nome do cofre chave, e o nome da assinatura em que está.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ao limitar os resultados `join` com `project`, o imóvel utilizado por `join` para relacionar as duas tabelas, _subscrição Id_ no exemplo acima referido, deve ser incluído na `project`.

## <a name="supported-kql-language-elements"></a>Elementos linguísticos KQL suportados

O Resource Graph suporta todos os [tipos](/azure/kusto/query/scalar-data-types/)de dados KQL, [funções escalar,](/azure/kusto/query/scalarfunctions) [operadores escalar,](/azure/kusto/query/binoperators)e funções de [agregação.](/azure/kusto/query/any-aggfunction) Operadores [tabulares específicos](/azure/kusto/query/queries) são suportados pelo Resource Graph, alguns dos quais têm comportamentos diferentes.

### <a name="supported-tabulartop-level-operators"></a>Operadores tabular/de nível superior suportados

Aqui está a lista de operadores tabular KQL suportados pelo Resource Graph com amostras específicas:

|KQL |Consulta de amostra de gráfico de recursos |Notas |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Abobadais-chave de contagem](../samples/starter.md#count-keyvaults) | |
|[distinto](/azure/kusto/query/distinctoperator) |[Mostrar valores distintos para um pseudónimo específico](../samples/starter.md#distinct-alias-values) | |
|[estender](/azure/kusto/query/extendoperator) |[Contar máquinas virtuais por tipo de SO](../samples/starter.md#count-os) | |
|[aderir](/azure/kusto/query/joinoperator) |[Cofre chave com nome de subscrição](../samples/advanced.md#join) |Junte sabores suportados: [innerunique,](/azure/kusto/query/joinoperator#default-join-flavor) [inner,](/azure/kusto/query/joinoperator#inner-join) [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite de 3 `join` numa única consulta. Não são permitidas estratégias de adesão personalizadas, como a adesão à emissão. Pode ser utilizado numa única tabela ou entre as tabelas _de Recursos_ e Contentores de _Recursos._ |
|[limite](/azure/kusto/query/limitoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinónimo de `take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operador legado, use `mv-expand` em vez disso. _RowLimit_ max de 400. O padrão é 128. |
|[mv-expandir](/azure/kusto/query/mvexpandoperator) |[List Cosmos DB com localizações de escrita específicas](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max de 400. O padrão é 128. |
|[ordem](/azure/kusto/query/orderoperator) |[Listar recursos ordenados por nome](../samples/starter.md#list-resources) |Sinónimo de `sort` |
|[projeto](/azure/kusto/query/projectoperator) |[Listar recursos ordenados por nome](../samples/starter.md#list-resources) | |
|[projeto-longe](/azure/kusto/query/projectawayoperator) |[Remover colunas dos resultados](../samples/advanced.md#remove-column) | |
|[classificar](/azure/kusto/query/sortoperator) |[Listar recursos ordenados por nome](../samples/starter.md#list-resources) |Sinónimo de `order` |
|[resumo](/azure/kusto/query/summarizeoperator) |[Contar recursos do Azure](../samples/starter.md#count-resources) |Primeira página simplificada apenas |
|[tomar](/azure/kusto/query/takeoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinónimo de `limit` |
|[Início](/azure/kusto/query/topoperator) |[Mostrar as primeiras cinco máquinas virtuais por nome e por tipo de SO](../samples/starter.md#show-sorted) | |
|[união](/azure/kusto/query/unionoperator) |[Combine os resultados de duas consultas num único resultado](../samples/advanced.md#unionresults) |Tabela única permitida: _T_ `| union`  \[`kind=`   `inner`\|`outer` \] \[`withsource=`_ColumnName_\] _Table_. Limite de 3 `union` pernas numa única consulta. Resolução fuzzy de `union` mesas de pernas não é permitida. Pode ser utilizado numa única tabela ou entre as tabelas _de Recursos_ e Contentores de _Recursos._ |
|[onde onde](/azure/kusto/query/whereoperator) |[Mostrar recursos que contêm armazenamento](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Personagens de fuga

Alguns nomes de propriedade, como os que incluem um `.` ou `$`, devem ser embrulhados ou escapados na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.` - Embrulhe o nome da propriedade como tal: `['propertyname.withaperiod']`
  
  Consulta de exemplo que envolve a propriedade _odata.type:_

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - Escape o personagem no nome da propriedade. O carácter de fuga usado depende do gráfico de recursos da concha.

  - **`\`**  - bash

    Consulta de exemplo que escapa da propriedade _\$tipo_ em festa:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Não escape ao personagem `$`.

  - ``` ` ``` de - **PowerShell**

    Consulta de exemplo que escapa da propriedade _\$tipo_ em PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma utilizado nas [consultas de arranque](../samples/starter.md).
- Consulte [utilizações avançadas em consultas avançadas.](../samples/advanced.md)
- Saiba mais sobre como [explorar recursos.](explore-resources.md)