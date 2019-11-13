---
title: Trabalhar com grandes conjuntos de dados
description: Entenda como obter e controlar grandes conjuntos de dados ao trabalhar com o grafo de recursos do Azure.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: a7258534b867c57f89eeb432b579e87070b4fc1d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959016"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Trabalhando com grandes conjuntos de dados de recursos do Azure

O grafo de recursos do Azure foi projetado para trabalhar com e obter informações sobre recursos em seu ambiente do Azure. O grafo de recursos torna esses dados rápidos, mesmo ao consultar milhares de registros. O grafo de recursos tem várias opções para trabalhar com esses grandes conjuntos de dados.

Para obter orientação sobre como trabalhar com consultas em alta frequência, consulte [diretrizes para solicitações limitadas](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Tamanho do resultado do conjunto de dados

Por padrão, o grafo de recursos limita qualquer consulta para retornar apenas registros **100** . Esse controle protege o usuário e o serviço de consultas não intencionais que resultariam em grandes conjuntos de dados. Esse evento geralmente acontece quando um cliente está experimentando consultas para localizar e filtrar recursos da forma que atenda às suas necessidades específicas. Esse controle é diferente de usar os operadores de linguagem [superior](/azure/kusto/query/topoperator) ou [limite](/azure/kusto/query/limitoperator) do Azure data Explorer para limitar os resultados.

> [!NOTE]
> Ao usar o **primeiro**, é recomendável ordenar os resultados por pelo menos uma coluna com `asc` ou `desc`. Sem classificação, os resultados retornados são aleatórios e não podem ser repetidos.

O limite padrão pode ser substituído por todos os métodos de interação com o grafo de recursos. Os exemplos a seguir mostram como alterar o limite de tamanho do conjunto de dados para _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

Na [API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources), o controle é **$Top** e faz parte de **QueryRequestOptions**.

O controle _mais restritivo_ ganhará. Por exemplo, se a consulta usar os operadores **superior** ou **limite** e resultar em mais registros do que o **primeiro**, os registros máximos retornados seriam iguais a **primeiro**. Da mesma forma, se **Top** ou **Limit** for menor do que **First**, o conjunto de registros retornado será o valor menor configurado por **Top** ou **Limit**.

O **primeiro** tem, no momento, um valor máximo permitido de _5000_.

## <a name="skipping-records"></a>Ignorando registros

A próxima opção para trabalhar com grandes conjuntos de dados é o controle **Skip** . Esse controle permite que a consulta salte ou ignore o número definido de registros antes de retornar os resultados. **Skip** é útil para consultas que classificam resultados de uma maneira significativa em que a intenção é obter registros em algum lugar no meio do conjunto de resultados. Se os resultados necessários estiverem no final do conjunto de dados retornado, será mais eficiente usar uma configuração de classificação diferente e recuperar os resultados da parte superior do conjunto de dados em vez disso.

> [!NOTE]
> Ao usar **Skip**, é recomendável ordenar os resultados por pelo menos uma coluna com `asc` ou `desc`. Sem classificação, os resultados retornados são aleatórios e não podem ser repetidos.

Os exemplos a seguir mostram como ignorar os _10_ primeiros registros que uma consulta resultaria, em vez de iniciar o conjunto de resultados retornado com o registro 11:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

Na [API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources), o controle é **$Skip** e faz parte de **QueryRequestOptions**.

## <a name="paging-results"></a>Resultados de paginação

Quando for necessário interromper um conjunto de resultados em conjuntos menores de registros para processamento ou porque um conjunto de resultados excederia o valor máximo permitido de _1000_ registros retornados, use paginação. O **QueryResponse** da [API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) fornece valores para indicar que um conjunto de resultados foi dividido: **resultTruncated** e **$skipToken**.
**resultTruncated** é um valor booliano que informa ao consumidor se há registros adicionais não retornados na resposta. Essa condição também pode ser identificada quando a propriedade **Count** for menor que a propriedade **totalRecords** . **totalRecords** define quantos registros correspondem à consulta.

Quando **resultTruncated** é **true**, a propriedade **$skipToken** é definida na resposta. Esse valor é usado com os mesmos valores de consulta e assinatura para obter o próximo conjunto de registros que corresponderam à consulta.

Os exemplos a seguir mostram como **ignorar** os primeiros 3000 registros e retornar os **primeiros** 1000 registros após os registros ignorados com CLI do Azure e Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A consulta deve **projetar** o campo **ID** para que a paginação funcione. Se ele estiver ausente da consulta, a resposta não incluirá o **$skipToken**.

Para obter um exemplo, consulte a [consulta de próxima página](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) nos documentos da API REST.

## <a name="formatting-results"></a>Formatando resultados

Os resultados de uma consulta de gráfico de recursos são fornecidos em dois formatos, _tabela_ e _objectarray_. O formato é configurado com o parâmetro **resultFormat** como parte das opções de solicitação. O formato de _tabela_ é o valor padrão para **resultFormat**.

Os resultados de CLI do Azure são fornecidos em JSON por padrão. Os resultados em Azure PowerShell são um **PSCustomObject** por padrão, mas podem ser convertidos rapidamente em JSON usando o cmdlet `ConvertTo-Json`. Para outros SDKs, os resultados da consulta podem ser configurados para gerar o formato _objectarray_ .

### <a name="format---table"></a>Formatar tabela

O formato padrão, _tabela_, retorna resultados em um formato JSON projetado para realçar o design de coluna e os valores de linha das propriedades retornadas pela consulta. Esse formato se assemelha bastante aos dados, conforme definido em uma tabela ou planilha estruturada, com as colunas identificadas primeiro e, em seguida, cada linha representando os dados alinhados a essas colunas.

Aqui está um exemplo de um resultado de consulta com a formatação de _tabela_ :

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

### <a name="format---objectarray"></a>Format-objectarray

O formato _objectarray_ também retorna resultados em um formato JSON. No entanto, esse design se alinha à relação de par chave/valor comum em JSON em que a coluna e os dados de linha são correspondidos em grupos de matriz.

Aqui está um exemplo de um resultado de consulta com a formatação _objectarray_ :

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

Aqui estão alguns exemplos de como definir **resultFormat** para usar o formato _objectarray_ :

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

- Consulte o idioma em uso em [consultas de início](../samples/starter.md).
- Consulte usos avançados em [consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).