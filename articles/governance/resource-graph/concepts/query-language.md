---
title: Entender a linguagem de consulta
description: Descreve tabelas de gráficos de recursos e os tipos de dados, operadores e funções disponíveis de Kusto utilizáveis com o Azure Resource Graph.
ms.date: 11/18/2020
ms.topic: conceptual
ms.openlocfilehash: 34aaaa60ed9d757cc1a63ffaebb2225900cff61f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966688"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Compreender a linguagem de consulta de gráfico de recurso Azure

A linguagem de consulta para o Gráfico de Recursos Azure suporta uma série de operadores e funções. Cada trabalho e funciona com base na [Língua De Consulta de Kusto (KQL)](/azure/kusto/query/index). Para saber mais sobre a linguagem de consulta utilizada pelo Gráfico de Recursos, comece com o [tutorial para KQL](/azure/kusto/query/tutorial).

Este artigo abrange os componentes linguísticos suportados pelo Gráfico de Recursos:

- [Tabelas de gráficos de recursos](#resource-graph-tables)
- [Elementos de linguagem personalizada do gráfico de recurso](#resource-graph-custom-language-elements)
- [Elementos linguísticos KQL suportados](#supported-kql-language-elements)
- [Âmbito da consulta](#query-scope)
- [Personagens de fuga](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelas de gráficos de recursos

O Resource Graph fornece várias tabelas para os dados que armazena sobre os tipos de recursos do Azure Resource Manager e as suas propriedades. Algumas tabelas podem ser `join` usadas ou `union` operadores para obter propriedades de tipos de recursos relacionados. Aqui está a lista de tabelas disponíveis no Gráfico de Recursos:

|Tabela de gráficos de recursos |`join`Pode? |Descrição |
|---|---|
|Recursos |Yes |A tabela predefinitiva se nenhuma for definida na consulta. A maioria dos tipos e propriedades de recursos do Gestor de Recursos estão aqui. |
|RecursosContainers |Yes |Inclui a subscrição (na pré-visualização -- `Microsoft.Resources/subscriptions` ) e os tipos de recursos e dados do grupo de `Microsoft.Resources/subscriptions/resourcegroups` recursos. |
|Recursos Consultivos |No |Inclui recursos _relacionados_ `Microsoft.Advisor` com. |
|AlertasManagementResources |No |Inclui recursos _relacionados_ `Microsoft.AlertsManagement` com. |
|GuestConfigurationResources |No |Inclui recursos _relacionados_ `Microsoft.GuestConfiguration` com. |
|Fontes de manutenção |No |Inclui recursos _relacionados_ `Microsoft.Maintenance` com. |
|Recursos de Política |No |Inclui recursos _relacionados_ `Microsoft.PolicyInsights` com. **(Pré-visualização)**|
|Recursos de Segurança |No |Inclui recursos _relacionados_ `Microsoft.Security` com. |
|ServiceHealthResources |No |Inclui recursos _relacionados_ `Microsoft.ResourceHealth` com. |

Para obter uma lista completa, incluindo tipos de recursos, consulte [Referência: Tabelas suportadas e tipos de recursos](../reference/supported-tables-resources.md).

> [!NOTE]
> _Os recursos_ são a tabela padrão. Ao consultar a tabela _Recursos,_ não é necessário fornecer o nome da mesa a menos `join` que ou sejam `union` usados. No entanto, a prática recomendada é incluir sempre a tabela inicial na consulta.

Utilize o Resource Graph Explorer no portal para descobrir que tipos de recursos estão disponíveis em cada tabela. Como alternativa, utilize uma consulta de modo `<tableName> | distinct type` a obter uma lista de tipos de recursos, tendo em conta os suportes de tabela de gráficos de recursos que existem no seu ambiente.

A seguinte consulta mostra um simples `join` . O resultado da consulta mistura as colunas e quaisquer nomes de colunas duplicados da tabela aderida, _RecursosContainers_ neste exemplo, são anexados a **1**. Como a tabela _ResourceContainers_ tem tipos tanto para subscrições como grupos de recursos, qualquer tipo pode ser usado para se juntar ao recurso a partir da tabela de _recursos._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A seguinte consulta mostra uma utilização mais complexa de `join` . A consulta limita a tabela associada aos recursos de subscrições e `project` com a inclusão apenas da subscrição original _de campoId_ e o campo _de nome_ renomeado para _SubName_. O renome de campo evita `join` adicioná-lo como _nome1_ uma vez que o campo já existe em _Recursos._ A tabela original é filtrada `where` e a seguinte inclui `project` colunas de ambas as tabelas. O resultado da consulta é um único tipo de exposição de um cofre de chave, o nome do cofre da chave, e o nome da subscrição em que está.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ao limitar os `join` resultados `project` com, o imóvel utilizado para `join` relacionar as duas tabelas, _subscriçãoId_ no exemplo acima, deve ser incluído em `project` .

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Propriedades estendidas (pré-visualização)

Como uma funcionalidade de _pré-visualização,_ alguns dos tipos de recursos no Gráfico de Recursos têm propriedades adicionais relacionadas com o tipo disponíveis para consulta além das propriedades fornecidas pelo Azure Resource Manager. Este conjunto de valores, conhecido como _propriedades estendidas,_ existe num tipo de recurso suportado em `properties.extended` . Para ver quais os tipos de recursos que têm _propriedades estendidas,_ utilize a seguinte consulta:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Exemplo: Obtenha a contagem de máquinas virtuais `instanceView.powerState.code` por:

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Elementos de linguagem personalizada do gráfico de recurso

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Sintaxe de consulta partilhada (pré-visualização)

Como uma funcionalidade de pré-visualização, uma [consulta partilhada](../tutorials/create-share-query.md) pode ser acedida diretamente numa consulta de Gráfico de Recurso. Este cenário permite criar consultas padrão como consultas partilhadas e reutilizá-las. Para chamar uma consulta partilhada dentro de uma consulta de Gráfico de Recurso, utilize a `{{shared-query-uri}}` sintaxe. O URI da consulta partilhada é o _ID_ de recurso da consulta partilhada na página **Definições** para essa consulta. Neste exemplo, a nossa consulta partilhada URI `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` é.
Este URI aponta para a subscrição, grupo de recursos e nome completo da consulta partilhada que queremos referir em outra consulta. Esta consulta é a mesma que foi criada no [Tutorial: Criar e partilhar uma consulta.](../tutorials/create-share-query.md)

> [!NOTE]
> Não se pode guardar uma consulta que refira uma consulta partilhada como uma consulta partilhada.

Exemplo 1: Utilize apenas a consulta partilhada

Os resultados desta consulta de gráfico de recurso são os mesmos que a consulta armazenada na consulta partilhada.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Exemplo 2: Incluir a consulta partilhada como parte de uma consulta maior

Esta consulta utiliza primeiro a consulta partilhada e, em seguida, utiliza `limit` para restringir ainda mais os resultados.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Elementos linguísticos KQL suportados

O Gráfico de Recursos suporta um subconjunto de tipos de [dados](/azure/kusto/query/scalar-data-types/)KQL, [funções escalar,](/azure/kusto/query/scalarfunctions) [operadores escalares](/azure/kusto/query/binoperators)e [funções de agregação.](/azure/kusto/query/any-aggfunction) Operadores [tabulares específicos](/azure/kusto/query/queries) são suportados por Resource Graph, alguns dos quais têm comportamentos diferentes.

### <a name="supported-tabulartop-level-operators"></a>Operadores tabulares/de nível superior suportados

Aqui está a lista de operadores tabulares KQL suportados por Gráfico de Recursos com amostras específicas:

|KQL |Consulta de amostra de gráfico de recurso |Notas |
|---|---|---|
|[contar](/azure/kusto/query/countoperator) |[Conde cofres-chave](../samples/starter.md#count-keyvaults) | |
|[distinto](/azure/kusto/query/distinctoperator) |[Mostrar valores distintos para um pseudónimo específico](../samples/starter.md#distinct-alias-values) | |
|[estender](/azure/kusto/query/extendoperator) |[Contar máquinas virtuais por tipo de SO](../samples/starter.md#count-os) | |
|[juntar-se](/azure/kusto/query/joinoperator) |[Cofre de chave com nome de assinatura](../samples/advanced.md#join) |Junte os sabores suportados: [interior,](/azure/kusto/query/joinoperator#default-join-flavor) [interior,](/azure/kusto/query/joinoperator#inner-join) [canhoto.](/azure/kusto/query/joinoperator#left-outer-join) Limite de 3 `join` numa única consulta. Estratégias de junção personalizadas, como a junção de transmissão, não são permitidas. Para as tabelas que podem ser `join` [utilizadas,](#resource-graph-tables)consulte as tabelas De Gráfico de Recursos . |
|[limite](/azure/kusto/query/limitoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinónimo `take` de. Não funciona com [o Skip.](./work-with-data.md#skipping-records) |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operador legado, use `mv-expand` em vez disso. _LinhaLimit_ max de 400. O padrão é 128. |
|[mv-expandir](/azure/kusto/query/mvexpandoperator) |[List Cosmos DB com locais de escrita específicos](../samples/advanced.md#mvexpand-cosmosdb) |_LinhaLimit_ max de 400. O padrão é 128. |
|[order](/azure/kusto/query/orderoperator) |[Listar recursos ordenados pelo nome](../samples/starter.md#list-resources) |Sinónimo de `sort` |
|[projeto](/azure/kusto/query/projectoperator) |[Listar recursos ordenados pelo nome](../samples/starter.md#list-resources) | |
|[projeto-away](/azure/kusto/query/projectawayoperator) |[Remover colunas dos resultados](../samples/advanced.md#remove-column) | |
|[tipo](/azure/kusto/query/sortoperator) |[Listar recursos ordenados pelo nome](../samples/starter.md#list-resources) |Sinónimo de `order` |
|[resumir](/azure/kusto/query/summarizeoperator) |[Contar recursos do Azure](../samples/starter.md#count-resources) |Primeira página simplificada apenas |
|[take](/azure/kusto/query/takeoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinónimo `limit` de. Não funciona com [o Skip.](./work-with-data.md#skipping-records) |
|[Início](/azure/kusto/query/topoperator) |[Mostrar as primeiras cinco máquinas virtuais pelo nome e o seu tipo de SO](../samples/starter.md#show-sorted) | |
|[união](/azure/kusto/query/unionoperator) |[Combine resultados de duas consultas num único resultado](../samples/advanced.md#unionresults) |Tabela única permitida: _Tabela T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _ColumnName_ \] _Table_. Limite de 3 `union` pernas numa única consulta. Não é permitida a resolução fuzzy das mesas das `union` pernas. Pode ser usado dentro de uma única tabela ou entre as _tabelas Recursos_ e _RecursosContainers._ |
|[onde](/azure/kusto/query/whereoperator) |[Mostrar recursos que contenham armazenamento](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>Âmbito de consulta

O âmbito das subscrições a partir das quais os recursos são devolvidos por uma consulta dependem do método de acesso ao Gráfico de Recursos. A Azure CLI e Azure PowerShell preenchem a lista de subscrições a incluir no pedido com base no contexto do utilizador autorizado. A lista de subscrições pode ser definida manualmente para cada uma com as **subscrições** e parâmetros **de Subscrição,** respectivamente.
Na REST API e em todos os outros SDKs, a lista de subscrições a incluir recursos deve ser explicitamente definida como parte do pedido.

Como **pré-visualização,** a versão REST API `2020-04-01-preview` adiciona um imóvel para estender a consulta a um [grupo de gestão](../../management-groups/overview.md). Esta API de pré-visualização também torna opcional a propriedade de subscrição. Se um grupo de gestão ou uma lista de subscrição não estiver definido, o âmbito de consulta é todos os recursos, que incluem recursos delegados [do Farol Azure,](../../../lighthouse/concepts/azure-delegated-resource-management.md) a que o utilizador autenticado pode aceder. O novo `managementGroupId` imóvel leva o ID do grupo de gestão, que é diferente do nome do grupo de gestão. Quando `managementGroupId` especificados, os recursos das primeiras 5000 assinaturas na hierarquia do grupo de gestão especificada são incluídos. `managementGroupId` não pode ser usado ao mesmo tempo que `subscriptions` .

Exemplo: Consultar todos os recursos dentro da hierarquia do grupo de gestão denominado 'My Management Group' com ID 'myMG'.

- URI da API REST

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Personagens de fuga

Alguns nomes de propriedade, como os que incluem um `.` `$` ou, devem ser embrulhados ou escapados na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.` - Embrulhe o nome da propriedade como tal: `['propertyname.withaperiod']`
  
  Consulta de exemplo que envolve a propriedade _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - Fuja do personagem no nome da propriedade. O carácter de fuga utilizado depende do gráfico de recursos da concha.

  - **festa** - `\`

    Consulta de exemplo que escapa ao _\$ tipo_ de propriedade em bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Não fuja do `$` personagem.

  - **PowerShell** - ``` ` ```

    Consulta de exemplo que escapa ao _\$ tipo_ de propriedade em PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso nas [consultas de arranque](../samples/starter.md).
- Consulte utilizações avançadas em [consultas avançadas.](../samples/advanced.md)
- Saiba mais sobre como [explorar recursos.](explore-resources.md)
