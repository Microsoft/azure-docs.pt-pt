---
title: Entender a linguagem de consulta
description: Descreve as tabelas de gráfico de recursos e os tipos de dados, operadores e funções do Kusto disponíveis utilizáveis com o grafo de recursos do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 80b33212afa7fed3f87b241d5cf69b43be66574d
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755921"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do grafo de recursos do Azure

A linguagem de consulta para o grafo de recursos do Azure dá suporte a vários operadores e funções. Cada trabalho e opera com base na [linguagem de consulta Kusto (KQL)](/azure/kusto/query/index). Para saber mais sobre a linguagem de consulta usada pelo grafo de recursos, comece com o [tutorial para KQL](/azure/kusto/query/tutorial).

Este artigo aborda os componentes de linguagem com suporte no grafo de recursos:

- [Tabelas do grafo de recursos](#resource-graph-tables)
- [Elementos de linguagem KQL com suporte](#supported-kql-language-elements)
- [Caracteres de escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelas do grafo de recursos

O grafo de recursos fornece várias tabelas para os dados que ele armazena sobre os tipos de recursos do Resource Manager e suas propriedades. Essas tabelas podem ser usadas com operadores `join` ou `union` para obter propriedades de tipos de recursos relacionados. Aqui está a lista de tabelas disponíveis no grafo de recursos:

|Tabelas do grafo de recursos |Descrição |
|---|---|
|Recursos |A tabela padrão se nenhuma for definida na consulta. A maioria dos tipos de recursos e propriedades do Resource Manager estão aqui. |
|ResourceContainers |Inclui a assinatura (em visualização `Microsoft.Resources/subscriptions`) e os tipos de recurso e os dados do grupo de recursos (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AlertsManagementResources |Inclui recursos _relacionados_ a `Microsoft.AlertsManagement`. |
|SecurityResources |Inclui recursos _relacionados_ a `Microsoft.Security`. |

> [!NOTE]
> _Recursos_ é a tabela padrão. Ao consultar a tabela de _recursos_ , não é necessário fornecer o nome da tabela, a menos que `join` ou `union` sejam usados. No entanto, a prática recomendada é sempre incluir a tabela inicial na consulta.

Use o Gerenciador de grafo de recursos no portal para descobrir quais tipos de recursos estão disponíveis em cada tabela. Como alternativa, use uma consulta como `<tableName> | distinct type` para obter uma lista de tipos de recursos que a tabela de grafo de recursos fornecida dá suporte, que existe em seu ambiente.

A consulta a seguir mostra um `join` simples. O resultado da consulta mistura as colunas e quaisquer nomes de coluna duplicados da tabela unida, _ResourceContainers_ neste exemplo, são acrescentados com **1**. Como a tabela _ResourceContainers_ tem tipos para assinaturas e grupos de recursos, qualquer tipo pode ser usado para ingressar no recurso da tabela de _recursos_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A consulta a seguir mostra um uso mais complexo de `join`. A consulta limita a tabela unida aos recursos de assinaturas e com `project` para incluir apenas o campo original _SubscriptionId_ e o campo de _nome_ renomeado como _subnomear_. A renomeação do campo evita `join` adicioná-lo como _Nome1_ , pois o campo já existe nos _recursos_. A tabela original é filtrada com `where` e o seguinte `project` inclui colunas de ambas as tabelas. O resultado da consulta é um único cofre de chaves que exibe o tipo, o nome do cofre de chaves e o nome da assinatura em que ele está.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ao limitar os resultados de `join` com `project`, a propriedade usada por `join` para relacionar as duas tabelas, _SubscriptionId_ no exemplo acima, deve ser incluída em `project`.

## <a name="supported-kql-language-elements"></a>Elementos de linguagem KQL com suporte

O grafo de recursos dá suporte a todos os [tipos de dados](/azure/kusto/query/scalar-data-types/)KQL, [funções escalares](/azure/kusto/query/scalarfunctions), [operadores escalares](/azure/kusto/query/binoperators)e [funções de agregação](/azure/kusto/query/any-aggfunction). Há suporte para [operadores tabulares](/azure/kusto/query/queries) específicos pelo grafo de recursos, alguns dos quais têm comportamentos diferentes.

### <a name="supported-tabulartop-level-operators"></a>Operadores de nível de tabela/superior com suporte

Aqui está a lista de operadores de tabela KQL com suporte do grafo de recursos com exemplos específicos:

|KQL |Consulta de exemplo de grafo de recursos |Notas |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Contar cofres de chaves](../samples/starter.md#count-keyvaults) | |
|[distinção](/azure/kusto/query/distinctoperator) |[Mostrar valores distintos para um alias específico](../samples/starter.md#distinct-alias-values) | |
|[estender](/azure/kusto/query/extendoperator) |[Contar máquinas virtuais por tipo de SO](../samples/starter.md#count-os) | |
|[aderir](/azure/kusto/query/joinoperator) |[Key Vault com o nome da assinatura](../samples/advanced.md#join) |Tipos de junção com suporte: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite de 3 `join` em uma única consulta. Estratégias de junção personalizadas, como junção de difusão, não são permitidas. Pode ser usado em uma única tabela ou entre as tabelas de _recursos_ e _ResourceContainers_ . |
|[limite](/azure/kusto/query/limitoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinônimo de `take` |
|[MV-expandir](/azure/kusto/query/mvexpandoperator) |[Listar Cosmos DB com locais de gravação específicos](../samples/advanced.md#mvexpand-cosmosdb) |_Limite_ máximo de 400 |
|[Ordene](/azure/kusto/query/orderoperator) |[Listar recursos ordenados por nome](../samples/starter.md#list-resources) |Sinônimo de `sort` |
|[projeto](/azure/kusto/query/projectoperator) |[Listar recursos ordenados por nome](../samples/starter.md#list-resources) | |
|[Projeto-ausente](/azure/kusto/query/projectawayoperator) |[Remover colunas dos resultados](../samples/advanced.md#remove-column) | |
|[organizar](/azure/kusto/query/sortoperator) |[Listar recursos ordenados por nome](../samples/starter.md#list-resources) |Sinônimo de `order` |
|[resumir](/azure/kusto/query/summarizeoperator) |[Contar recursos do Azure](../samples/starter.md#count-resources) |Somente primeira página simplificada |
|[ter](/azure/kusto/query/takeoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinônimo de `limit` |
|[Início](/azure/kusto/query/topoperator) |[Mostrar as primeiras cinco máquinas virtuais por nome e por tipo de SO](../samples/starter.md#show-sorted) | |
|[unida](/azure/kusto/query/unionoperator) |[Combinar resultados de duas consultas em um único resultado](../samples/advanced.md#unionresults) |Única tabela permitida: _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=`_ColumnName_ 1 _tabela_. Limite de 3 `union` pernas em uma única consulta. A resolução difusa de tabelas de trechos `union` não é permitida. Pode ser usado em uma única tabela ou entre as tabelas de _recursos_ e _ResourceContainers_ . |
|[posição](/azure/kusto/query/whereoperator) |[Mostrar recursos que contêm armazenamento](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Caracteres de escape

Alguns nomes de propriedade, como aqueles que incluem um `.` ou `$`, devem ser encapsulados ou ter escape na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.` encapsular o nome da propriedade como tal: `['propertyname.withaperiod']`
  
  Exemplo de consulta que encapsula a propriedade _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-escapar do caractere no nome da propriedade. O caractere de escape usado depende do grafo de recursos do Shell em execução.

  - `\` **bash**  - 

    Exemplo de consulta que escapa a propriedade _\$type_ no bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -não escapar do caractere `$`.

  - @No__t_1 do **PowerShell** ``` ` ```

    Exemplo de consulta que escapa a propriedade _\$type_ no PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso em [consultas de início](../samples/starter.md)
- Consulte usos avançados em [consultas avançadas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)