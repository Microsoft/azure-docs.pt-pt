---
title: Trabalhar com grandes conjuntos de dados
description: Entenda como obter, formato, página e saltar registos em grandes conjuntos de dados enquanto trabalha com o Azure Resource Graph.
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 6054d2cd2cf012c21f451ece87db672897fa0398
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843354"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Trabalhar com grandes conjuntos de dados de recursos Azure

O Azure Resource Graph foi concebido para trabalhar e obter informações sobre recursos no seu ambiente Azure. O Gráfico de Recursos torna a obtenção destes dados rapidamente, mesmo quando consulta milhares de registos. O Gráfico de Recursos tem várias opções para trabalhar com estes grandes conjuntos de dados.

Para obter orientações sobre o trabalho com consultas em alta frequência, consulte [orientação para pedidos acelerados](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Tamanho do resultado do conjunto de dados

Por predefinição, o Gráfico de Recursos limita qualquer consulta à devolução de apenas **100** registos. Este controlo protege tanto o utilizador como o serviço de consultas não intencionais que resultariam em grandes conjuntos de dados. Este evento acontece mais frequentemente como um cliente está experimentando consultas para encontrar e filtrar recursos da forma que se adequa às suas necessidades particulares. Este controlo é diferente de utilizar os operadores de idiomas Azure Data Explorer [superiores](/azure/kusto/query/topoperator) ou [limitam](/azure/kusto/query/limitoperator) os resultados.

> [!NOTE]
> Ao utilizar **primeiro,** é aconselhável encomendar os resultados por pelo menos uma coluna com `asc` ou `desc` . Sem triagem, os resultados devolvidos são aleatórios e não repetíveis.

O limite predefinido pode ser ultrapassado através de todos os métodos de interação com o Gráfico de Recursos. Os exemplos a seguir mostram como alterar o limite de tamanho do conjunto de dados para _200:_

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

Na [API REST,](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)o controlo é **$top** e faz parte das **Opções DeryRequestOptions**.

O controlo _mais restritivo_ vencerá. Por exemplo, se a sua consulta utilizar os operadores **superiores** ou **limitos** e resultar em mais registos do que **o First,** os registos máximos devolvidos seriam iguais ao **First**. Da mesma forma, se **o topo** ou **o limite** for menor do que **o Primeiro,** o recorde devolvido seria o valor mais pequeno configurado por **cima** ou **por limite**.

**Primeiro** tem atualmente um valor máximo permitido de _5000,_ o que consegue através [da paging resultados](#paging-results) _de 1000_ recordes de cada vez.

> [!IMPORTANT]
> Quando **a First** é configurada para ser superior a _1000_ registos, a consulta deve **projetar** o campo **de id** para que a paginação funcione. Se faltar da consulta, a resposta não será [pageed](#paging-results) e os resultados estão limitados a _1000_ registos.

## <a name="skipping-records"></a>Ignorando registos

A próxima opção para trabalhar com grandes conjuntos de dados é o controlo **Skip.** Este controlo permite que a sua consulta salte ou salte o número definido de registos antes de devolver os resultados. **Skip** é útil para consultas que tipo resultados de uma forma significativa onde a intenção é obter em registros em algum lugar no meio do conjunto de resultados. Se os resultados necessários estiverem no final do conjunto de dados devolvidos, é mais eficiente utilizar uma configuração de tipo diferente e recuperar os resultados do topo do conjunto de dados.

> [!NOTE]
> Ao utilizar **skip,** é aconselhável encomendar os resultados por pelo menos uma coluna com `asc` ou `desc` . Sem triagem, os resultados devolvidos são aleatórios e não repetíveis. Se `limit` ou for utilizado na `take` consulta, **skip** é ignorado.

Os exemplos a seguir mostram como saltar os primeiros _10_ registos que uma consulta resultaria, em vez de iniciar o conjunto de resultados devolvidos com o 11º recorde:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

Na [API REST,](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)o controlo é **$skip** e faz parte das **Opções DeryRequestOptions**.

## <a name="paging-results"></a>Resultados de paginação

Quando for necessário quebrar um resultado definido em conjuntos menores de registos para processamento ou porque um conjunto de resultados excederia o valor máximo permitido de _1000_ registos devolvidos, use a paging. A [PER API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) 
 **QueryResponse** fornece valores que indicam que um conjunto de resultados foi quebrado: **resultados $skipToken** e **$skipToken**. **resultadoTruncado** é um valor booleano que informa o consumidor se houver registos adicionais não devolvidos na resposta. Esta condição também pode ser identificada quando a propriedade **de contagem** é inferior à propriedade **TotalRecords.** **totalRecords** define quantos registos correspondem à consulta.

 **resultadoSTruncado** é **verdadeiro** quando ou a paging é desativada ou não é possível porque nenhuma `id` coluna ou quando há menos recursos disponíveis do que uma consulta está solicitando. Quando **o resultado É** **verdade,** a **propriedade $skipToken** não está definida.

Os exemplos a seguir mostram como **saltar** os primeiros 3000 discos e devolver os **primeiros** 1000 registos depois de esses registos terem saltado com Azure CLI e Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A consulta deve **projetar** o campo **de identificação** para que a paginação funcione. Se faltar da consulta, a resposta não incluirá o **$skipToken.**

Por exemplo, consulte [a consulta de página seguinte](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query) nos docs REST API.

## <a name="formatting-results"></a>Resultados de formatação

Os resultados de uma consulta de gráfico de recurso são fornecidos em dois formatos, _Tabela_ e _ObjectArray._ O formato está configurado com o **resultadoForme** o parâmetro como parte das opções de pedido. O formato _tabela_ é o valor predefinido para **resultadosForme.**

Os resultados do Azure CLI são fornecidos em JSON por padrão. Os resultados em Azure PowerShell são um **PSCustomObject** por padrão, mas podem ser rapidamente convertidos para JSON usando o `ConvertTo-Json` cmdlet. Para outros SDKs, os resultados da consulta podem ser configurados para a saída do formato _ObjectArray._

### <a name="format---table"></a>Formato - Tabela

O formato predefinido, _Table_, retorna resulta num formato JSON projetado para realçar os valores de design da coluna e linha das propriedades devolvidas pela consulta. Este formato assemelha-se muito aos dados definidos numa tabela estruturada ou numa folha de cálculo com as colunas identificadas primeiro e, em seguida, cada linha que representa dados alinhados com essas colunas.

Aqui está uma amostra de um resultado de consulta com a formatação _da tabela:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Formato - ObjectArray

O formato _ObjectArray_ também retorna os resultados num formato JSON. No entanto, este design alinha-se com a relação de par chave/valor comum no JSON, onde a coluna e os dados da linha são combinados em grupos de matrizes.

Aqui está uma amostra de um resultado de consulta com a formatação _ObjectArray:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Aqui estão alguns exemplos de definição de **resultadosFormat** para utilizar o formato _ObjectArray:_

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso nas [consultas de arranque](../samples/starter.md).
- Consulte utilizações avançadas em [consultas avançadas.](../samples/advanced.md)
- Saiba mais sobre como [explorar recursos.](explore-resources.md)
