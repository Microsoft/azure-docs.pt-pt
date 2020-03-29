---
title: Trabalhar com grandes conjuntos de dados
description: Compreenda como obter, formato, página e saltar registos em grandes conjuntos de dados enquanto trabalha com o Azure Resource Graph.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064747"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Trabalhar com grandes conjuntos de dados de recursos Azure

O Azure Resource Graph foi concebido para trabalhar e obter informações sobre recursos no seu ambiente Azure. O Resource Graph faz com que estes dados sejam rápidos, mesmo quando consultam milhares de registos. O Resource Graph tem várias opções para trabalhar com estes grandes conjuntos de dados.

Para obter orientações sobre o trabalho com consultas de alta frequência, consulte [Orientação para pedidos acelerados](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Tamanho do resultado do conjunto de dados

Por padrão, o Resource Graph limita qualquer consulta à devolução de apenas **100** registos. Este controlo protege tanto o utilizador como o serviço de consultas não intencionais que resultariam em grandes conjuntos de dados. Este evento acontece mais frequentemente como um cliente está experimentando consultas para encontrar e filtrar recursos da forma que se adequa às suas necessidades particulares. Este controlo é diferente de utilizar os [operadores](/azure/kusto/query/topoperator) de línguas do Top Ou [limite](/azure/kusto/query/limitoperator) o Azure Data Explorer para limitar os resultados.

> [!NOTE]
> Ao utilizar **primeiro,** recomenda-se encomendar os resultados `asc` por `desc`pelo menos uma coluna com ou . Sem triagem, os resultados devolvidos são aleatórios e não repetíveis.

O limite padrão pode ser ultrapassado através de todos os métodos de interação com o Resource Graph. Os seguintes exemplos mostram como alterar o limite de tamanho do conjunto de dados para _200:_

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

Na [API REST,](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)o controlo é **$top** e faz parte das **Opções De Pedidos**de Consulta .

O controlo _mais restritivo_ vai ganhar. Por exemplo, se a sua consulta utilizar os operadores **superiores** ou **limitadores** e resultar em mais registos do que **primeiro,** os registos máximos devolvidos seriam iguais ao **Primeiro**. Da mesma forma, se **o topo** ou o **limite** forem menores do que **o Primeiro,** o recorde devolvido seria o valor mais pequeno configurado por **topo** ou **limite**.

**A primeira** tem atualmente um valor máximo permitido de _5000_.

## <a name="skipping-records"></a>Faltando recordes

A próxima opção para trabalhar com grandes conjuntos de dados é o controlo **Skip.** Este controlo permite que a sua consulta salte ou salte o número definido de registos antes de devolver os resultados. **Skip** é útil para consultas que tipo resulta de uma forma significativa onde a intenção é obter em algum lugar no meio do conjunto de resultados. Se os resultados necessários estiverem no final do conjunto de dados devolvidos, é mais eficiente utilizar uma configuração de tipo diferente e recuperar os resultados do topo do conjunto de dados.

> [!NOTE]
> Ao utilizar **o Skip,** recomenda-se encomendar os resultados por pelo menos uma coluna com `asc` ou `desc`. Sem triagem, os resultados devolvidos são aleatórios e não repetíveis.

Os seguintes exemplos mostram como saltar os primeiros _10_ registos em que uma consulta resultaria, em vez de iniciar o resultado devolvido estabelecido com o 11º recorde:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

Na [API REST,](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)o controlo é **$skip** e faz parte das **Opções De Pedidos**de Consulta .

## <a name="paging-results"></a>Resultados de paginação

Quando for necessário quebrar um resultado definido em registos menores para processamento ou porque um conjunto de resultados excederia o valor máximo permitido de _1000_ registos devolvidos, use a paging. O [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** fornece valores que indicam que foi quebrado um conjunto de resultados: **resultadoTruncated** e **$skipToken**.
**resultadoTruncated** é um valor booleano que informa o consumidor se houver registos adicionais não devolvidos na resposta. Esta condição também pode ser identificada quando a propriedade **contada** é inferior à propriedade **total da Records.** **totalRecords** define quantos registos correspondem à consulta.

 **resultadoTruncated** é **verdade** quando ou a pagagem é `id` desativada ou não é possível devido a nenhuma coluna ou quando há menos recursos disponíveis do que uma consulta está solicitando. Quando **o resultadoTruncado** é **verdadeiro,** a **propriedade $skipToken** não está definida.

Os seguintes exemplos mostram como **saltar** os primeiros 3000 discos e devolver os **primeiros** 1000 registos depois de esses registos terem saltado com o Azure CLI e o Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A consulta deve **projetar** o campo **de identificação** para que a paginação funcione. Se faltar na consulta, a resposta não incluirá o **$skipToken.**

Por exemplo, consulte a [consulta da página seguinte](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) nos médicos REST API.

## <a name="formatting-results"></a>Resultados da formatação

Os resultados de uma consulta de gráfico de recursos são fornecidos em dois formatos, _Tabela_ e _ObjectArray_. O formato é configurado com o parâmetro **formato de resultados** como parte das opções de pedido. O formato _Tabela_ é o valor padrão para **o resultadoFormat**.

Os resultados do Azure CLI são fornecidos em JSON por padrão. Os resultados em Azure PowerShell são um **PSCustomObject** por padrão, `ConvertTo-Json` mas podem ser rapidamente convertidos para JSON usando o cmdlet. Para outros SDKs, os resultados da consulta podem ser configurados para a saída do formato _ObjectArray._

### <a name="format---table"></a>Formato - Tabela

O formato predefinido, _Tabela,_ devolve resultados num formato JSON projetado para destacar o design da coluna e os valores de linha das propriedades devolvidas pela consulta. Este formato assemelha-se de perto aos dados definidos numa tabela estruturada ou numa folha de cálculo com as colunas identificadas primeiro e, em seguida, cada linha representando dados alinhados com essas colunas.

Aqui está uma amostra de um resultado de consulta com a formatação da _tabela:_

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

O formato _ObjectArray_ também devolve resultados num formato JSON. No entanto, este design alinha-se com a relação chave/relação de par de valor comum na JSON, onde os dados da coluna e da linha são combinados em grupos de matriz.

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

Aqui estão alguns exemplos de definição **do resultadoForma** para usar o formato _ObjectArray:_

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

- Consulte o idioma utilizado nas [consultas de arranque](../samples/starter.md).
- Consulte [utilizações avançadas em consultas avançadas.](../samples/advanced.md)
- Saiba mais sobre como [explorar recursos.](explore-resources.md)