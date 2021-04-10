---
title: Documentação de orientação para pedidos limitados
description: Aprenda a agrupar, cambalear, paginar e consultar em paralelo para evitar que os pedidos sejam estrangulados pelo Azure Resource Graph.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd3cf4d411733e831c94039c3bc9aeaf0e95271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917712"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Orientação para pedidos acelerados no Gráfico de Recursos Azure

Ao criar dados programáticos e frequentes de gráficos de recursos Azure, deve ser feita consideração sobre o impacto do estrangulamento nos resultados das consultas. Alterar a forma como os dados são solicitados pode ajudá-lo e a sua organização a evitar ser estrangulado e manter o fluxo de dados oportunos sobre os seus recursos Azure.

Este artigo abrange quatro áreas e padrões relacionados com a criação de consultas no Azure Resource Graph:

- Compreender os cabeçalhos de limitação
- Consultas de agrupamento
- Escalonamento de consultas
- O impacto da paginação

## <a name="understand-throttling-headers"></a>Compreender os cabeçalhos de limitação

O Azure Resource Graph atribui um número de quota para cada utilizador com base numa janela de tempo. Por exemplo, um utilizador pode enviar no máximo 15 consultas dentro de cada janela de 5 segundos sem ser estrangulado. O valor do contingente é determinado por muitos fatores e está sujeito a alterações.

Em cada resposta de consulta, o Azure Resource Graph adiciona dois cabeçalhos de estrangulamento:

- `x-ms-user-quota-remaining` (int): A restante quota de recursos para o utilizador. Este valor mapeia para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração do tempo até ao reinício do consumo de quota de um utilizador.

Quando um titular de segurança tem acesso a mais de 5000 subscrições dentro do âmbito de [consulta](./query-language.md#query-scope)do arrendatário ou do grupo de gestão, a resposta limita-se às primeiras 5000 assinaturas e o `x-ms-tenant-subscription-limit-hit` cabeçalho é devolvido como `true` .

Para ilustrar como funcionam os cabeçalhos, vejamos uma resposta de consulta que tenha o cabeçalho e os valores de `x-ms-user-quota-remaining: 10` `x-ms-user-quota-resets-after: 00:00:03` e .

- Nos próximos 3 segundos, no máximo 10 consultas podem ser submetidas sem serem estranguladas.
- Em 3 segundos, os valores de `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` serão reiniciados `15` e `00:00:05` respectivamente.

Para ver um exemplo de utilização dos cabeçalhos para _recuar_ em pedidos de consulta, consulte a amostra em [Consulta em Paralelo](#query-in-parallel).

## <a name="grouping-queries"></a>Consultas de agrupamento

As consultas de agrupamento por subscrição, grupo de recursos ou recursos individuais são mais eficientes do que consultas paralelas. O custo de quota de uma consulta maior é muitas vezes inferior ao custo de quota de muitas consultas pequenas e direcionadas. Recomenda-se que o tamanho do grupo seja inferior a _300_.

- Exemplo de uma abordagem mal otimizada

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exemplo #1 de uma abordagem de agrupamento otimizada

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exemplo #2 de uma abordagem de agrupamento otimizada para obter múltiplos recursos em uma consulta

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Escalonamento de consultas

Devido à forma como o estrangulamento é aplicado, recomendamos que as consultas sejam escalonadas. Isto é, em vez de enviar 60 consultas ao mesmo tempo, escalonar as consultas em quatro janelas de 5 segundos:

- Horário de consulta não escalonado

  | Contagem de consultas         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (seg) | 0-5 | 5-10 | 10-15 | 15-20 |

- Horário de consulta escalonado

  | Contagem de consultas         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (seg) | 0-5 | 5-10 | 10-15 | 15-20 |

Aqui está um exemplo de respeito pelos cabeçalhos de estrangulamento ao consultar o Azure Resource Graph:

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

### <a name="query-in-parallel"></a>Consulta em Paralelo

Embora o agrupamento seja recomendado sobre a paralelização, há momentos em que as consultas não podem ser facilmente agrupadas. Nestes casos, poderá querer consultar o Azure Resource Graph enviando várias consultas de forma paralela. Aqui está um exemplo de como _recuar_ com base em cabeçalhos de estrangulamento em tais cenários:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

Uma vez que o Azure Resource Graph retorna no máximo 1000 entradas numa única resposta de consulta, poderá precisar de [paginar](./work-with-data.md#paging-results) as suas consultas para obter o conjunto de dados completo que procura. No entanto, alguns clientes do Azure Resource Graph lidam com a paginação de forma diferente de outros.

- SDK C#

  Ao utilizar o SDK do ResourceGraph, é necessário lidar com a paginação passando o token de salto sendo devolvido da resposta de consulta anterior à consulta paginada seguinte. Este design significa que você precisa recolher resultados de todas as chamadas paginadas e combiná-los juntos no final. Neste caso, cada consulta paginada que envia requer uma quota de consulta:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
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

- Azure CLI / Azure PowerShell

  Ao utilizar o Azure CLI ou o Azure PowerShell, as consultas ao Azure Resource Graph são automaticamente paginadas para obter no máximo 5000 entradas. Os resultados da consulta devolvem uma lista combinada de entradas de todas as chamadas paginadas. Neste caso, dependendo do número de entradas no resultado da consulta, uma única consulta paginada pode consumir mais do que uma quota de consulta. Por exemplo, nos seguintes exemplos, uma única tiragem da consulta pode consumir até cinco quotas de consulta:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Ainda é estrangulado?

Se for estrangulado depois de exercer as recomendações acima, contacte a equipa em [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com) .

Indique estes detalhes:

- O seu caso de utilização específico e o seu motorista de negócios precisam de um limite de estrangulamento mais elevado.
- A quantos recursos tem acesso? Quantos são devolvidos de uma única consulta?
- Que tipo de recursos está interessado?
- Qual é o seu padrão de consulta? X consultas por Y segundos, e assim por diante.

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso nas [consultas de arranque](../samples/starter.md).
- Consulte utilizações avançadas em [consultas avançadas.](../samples/advanced.md)
- Saiba mais sobre como [explorar recursos.](explore-resources.md)