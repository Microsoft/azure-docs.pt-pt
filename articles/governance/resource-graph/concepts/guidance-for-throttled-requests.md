---
title: Documentação de orientação para pedidos limitados
description: Aprenda a criar consultas melhores para evitar que as solicitações para o grafo de recursos do Azure sejam limitadas.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 85d68beb27ab27a2ada9acbf9482d35dec438c06
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980326"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Diretrizes para solicitações limitadas no grafo de recursos do Azure

Ao criar uso programático e frequente de dados do grafo de recursos do Azure, deve-se considerar como a limitação afeta os resultados das consultas. Alterar a maneira como os dados são solicitados pode ajudar você e sua organização a evitar a limitação e manter o fluxo de dados oportunos sobre os recursos do Azure.

Este artigo aborda quatro áreas e padrões relacionados à criação de consultas no grafo de recursos do Azure:

- Entender os cabeçalhos de limitação
- Consultas em lote
- Consultas de sobredisposição
- O impacto da paginação

## <a name="understand-throttling-headers"></a>Entender os cabeçalhos de limitação

O grafo de recursos do Azure aloca o número da cota para cada usuário com base em uma janela de tempo. Por exemplo, um usuário pode enviar no máximo 15 consultas dentro de cada janela de 5 segundos sem ser limitado. O valor da cota é determinado por muitos fatores e está sujeito a alterações.

Em cada resposta de consulta, o grafo de recursos do Azure adiciona dois cabeçalhos de limitação:

- `x-ms-user-quota-remaining` (int): A cota de recursos restante para o usuário. Esse valor é mapeado para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração de tempo até que o consumo de cota de um usuário seja redefinido.

Para ilustrar como os cabeçalhos funcionam, vamos examinar uma resposta de consulta que tem o cabeçalho e os valores de `x-ms-user-quota-remaining: 10` e `x-ms-user-quota-resets-after: 00:00:03`.

- Nos próximos 3 segundos, no máximo 10 consultas podem ser enviadas sem limitação.
- Em 3 segundos, os valores de `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` serão redefinidos para `15` e `00:00:05`, respectivamente.

Para ver um exemplo de como usar os cabeçalhos para _retirada_ em solicitações de consulta, consulte o exemplo em [consulta em paralelo](#query-in-parallel).

## <a name="batching-queries"></a>Consultas em lote

O envio em lote de consultas por assinatura, grupo de recursos ou recurso individual é mais eficiente do que paralelizar consultas. O custo de cota de uma consulta maior geralmente é menor do que o custo de cota de muitas consultas pequenas e direcionadas. O tamanho do lote é recomendado para ser menor que _300_.

- Exemplo de uma abordagem mal otimizada

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exemplo #1 de uma abordagem de envio em lote otimizada

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exemplo #2 de uma abordagem de envio em lote otimizada

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Consultas de sobredisposição

Devido à maneira como a limitação é imposta, recomendamos que as consultas sejam escalonadas. Ou seja, em vez de enviar consultas 60 ao mesmo tempo, escalonar as consultas em quatro janelas de 5 segundos:

- Agendamento de consulta não escalonado

  | Contagem de consultas         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (s) | 0-5 | 5-10 | 10-15 | 15-20 |

- Agenda de consulta escalonada

  | Contagem de consultas         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (s) | 0-5 | 5-10 | 10-15 | 15-20 |

Abaixo está um exemplo de como respeitar cabeçalhos de limitação ao consultar o grafo de recursos do Azure:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Consulta em paralelo

Embora o envio em lote seja recomendado sobre a paralelização, há ocasiões em que as consultas não podem ser facilmente agrupadas em lote. Nesses casos, talvez você queira consultar o grafo de recursos do Azure enviando várias consultas de maneira paralela. Veja abaixo um exemplo de como fazer a _retirada_ com base nos cabeçalhos de limitação em tais cenários:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginação

Como o grafo de recursos do Azure retorna no máximo 1000 entradas em uma única resposta de consulta, talvez seja necessário [paginar](./work-with-data.md#paging-results) suas consultas para obter o conjunto de dado completo que você está procurando. No entanto, alguns clientes do grafo de recursos do Azure manipulam a paginação diferente de outras

- SDK C#

  Ao usar o SDK do ResourceGraph, você precisa manipular a paginação passando o token Skip que está sendo retornado da resposta de consulta anterior para a próxima consulta paginada. Esse design significa que você precisa coletar resultados de todas as chamadas paginadas e combiná-las no final. Nesse caso, cada consulta paginada enviada usa uma cota de consulta:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- CLI do Azure/Azure PowerShell

  Ao usar CLI do Azure ou Azure PowerShell, as consultas para o grafo de recursos do Azure são paginadas automaticamente para buscar no máximo 5000 entradas. Os resultados da consulta retornam uma lista combinada de entradas de todas as chamadas paginadas. Nesse caso, dependendo do número de entradas no resultado da consulta, uma única consulta paginada pode consumir mais de uma cota de consulta. Por exemplo, no exemplo a seguir, uma única execução da consulta pode consumir até cinco cotas de consulta:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Ainda fica limitado?

Se você estiver ficando limitado depois de exercer as recomendações acima, entre em contato com a equipe em [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Forneça estes detalhes:

- Seu caso de uso específico e o driver de negócios precisam de um limite de limitação mais alto.
- A quantos recursos você tem acesso? Quantos dos são retornados de uma única consulta?
- Em que tipos de recursos você está interessado?
- Qual é seu padrão de consulta? X consultas por Y segundos etc.

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso em [consultas de início](../samples/starter.md).
- Consulte usos avançados em [consultas avançadas](../samples/advanced.md).
- Aprenda a [explorar os recursos](explore-resources.md).