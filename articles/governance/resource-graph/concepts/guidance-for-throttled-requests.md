---
title: Documentação de orientação para pedidos limitados
description: Aprenda a criar consultas melhor para evitar pedidos para o gráfico de recursos do Azure de a ser limitada.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276902"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Documentação de orientação para pedidos limitados no gráfico de recursos do Azure

Durante a criação programática e frequente a utilização de dados de gráfico de recursos do Azure, deve haver consideração de impactos como limitação os resultados das consultas. Os dados de forma a alteração é solicitada pode ajudá-lo e manter o fluxo de dados oportunos sobre os recursos do Azure e evitar a limitação de sua organização.

Este artigo abrange quatro áreas e padrões relacionados com a criação de consultas no gráfico de recursos do Azure:

- Compreender os cabeçalhos de limitação
- Consultas de criação de batches
- Impressionante de consultas
- O impacto da paginação

## <a name="understand-throttling-headers"></a>Compreender os cabeçalhos de limitação

Gráfico de recursos do Azure aloca o número de quota para cada utilizador com base numa janela de tempo. Por exemplo, um usuário pode enviar no máximo 15 consultas dentro de cada janela de 5 segundos sem a ser limitada. O valor da quota é determinado por diversos fatores e está sujeitas a alterações.

Em cada resposta de consulta, o gráfico de recursos do Azure adiciona dois cabeçalhos de limitação:

- `x-ms-user-quota-remaining` (int): A quota de recursos restantes para o utilizador. Este valor é mapeado para a contagem de consulta.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração de tempo até que o consumo de quota de um utilizador é reposto.

Para ilustrar como funcionam os cabeçalhos, vamos examinar uma resposta de consulta que tenha o cabeçalho e os valores dos `x-ms-user-quota-remaining: 10` e `x-ms-user-quota-resets-after: 00:00:03`.

- Dentro de 3 segundos que se seguem, no máximo 10 consultas podem ser enviadas sem a ser limitada.
- Em 3 segundos, os valores dos `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` será reposto para `15` e `00:00:05` , respetivamente.

Para ver um exemplo de uso de cabeçalhos para _término_ nos pedidos de consulta, consulte o exemplo na [consulta em paralelo](#query-in-parallel).

## <a name="batching-queries"></a>Consultas de criação de batches

Criação de batches de consultas por subscrição, grupo de recursos ou ao recurso individual é mais eficiente do que a paralelização de consultas. O custo de quota de uma consulta maior, muitas vezes, é menor que o custo de quota de muitas consultas de pequenas e direcionadas. O tamanho do lote é recomendado para ser inferior a _300_.

- Exemplo de uma abordagem mal otimizado

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

- Exemplo #1 uma abordagem de criação de batches otimizada

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

- Exemplo #2 uma abordagem de criação de batches otimizada

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

## <a name="staggering-queries"></a>Impressionante de consultas

Por conta da forma limitação é imposta, recomendamos que as consultas para ser escalonada. Ou seja, em vez de enviar 60 consultas ao mesmo tempo, escalonar as consultas em quatro janelas de 5 segundos:

- Agenda de consulta não-escalonadas

  | Contagem de consulta         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (seg) | 0-5 | 5-10 | 10-15 | 15-20 |

- Escalonadas agenda de consulta

  | Contagem de consulta         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (seg) | 0-5 | 5-10 | 10-15 | 15-20 |

Segue-se um exemplo de respeitar os cabeçalhos de limitação ao consultar o gráfico de recursos do Azure:

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

Apesar do processamento em lote recomenda-se ao longo da paralelização, há ocasiões em que consultas não podem ser facilmente em lotes. Nestes casos, talvez queira consultar o gráfico de recursos do Azure através do envio de várias consultas de forma paralela. Segue-se um exemplo de como _término_ com base nos cabeçalhos em tais cenários de limitação:

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

Uma vez que o gráfico de recursos do Azure devolve um máximo de 1000 entradas numa resposta de consulta única, se pretender [paginar](./work-with-data.md#paging-results) suas consultas para obter o conjunto de dados completo, que está procurando. No entanto, alguns clientes de gráfico de recursos do Azure lidar com paginação diferente das outras pessoas.

- SDK C#

  Ao utilizar o SDK de ResourceGraph, terá de lidar com a paginação, transmitindo o token de ignorar a ser retornado da resposta da consulta anterior para a próxima consulta paginada. Esse design significa que precisa coletar os resultados de todas as chamadas paginadas e combiná-los em conjunto no final. Neste caso, cada consulta paginada que enviar usa uma quota de consulta:

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

- CLI do Azure / Azure PowerShell

  Ao utilizar a CLI do Azure ou do Azure PowerShell, as consultas para o gráfico de recursos do Azure são automaticamente paginadas para obtenção de no máximo de 5000 entradas. Os resultados da consulta retornam uma lista combinada de entradas de todas as chamadas paginadas. Neste caso, dependendo do número de entradas no resultado da consulta, uma única consulta paginada pode consumir mais do que uma quota de consulta. Por exemplo, no exemplo a seguir, uma única execução da consulta pode consumir até cinco quota de consulta:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Ainda são limitados?

Se estiver obtendo otimizado depois executamos as recomendações acima, contacte a equipa em [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Forneça estes detalhes:

- Suas necessidades de controlador específico de casos de utilização e comerciais de um limite de limitação maior.
- Número de recursos tem acesso a? O número da são devolvidos por uma consulta única?
- Quais são os tipos de recursos está interessado em?
- O que é o seu padrão de consulta? X consultas por segundos de Y etc.

## <a name="next-steps"></a>Passos Seguintes

- Consulte o idioma utilizado na [consultas Starter](../samples/starter.md).
- Consulte avançada usa [avançadas consultas](../samples/advanced.md).
- Aprenda a [explore os recursos](explore-resources.md).