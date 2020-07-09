---
title: Singletons para Funções Duradouras - Azure
description: Como utilizar singletons na extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76262814"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores Singleton em Funções Duradouras (Funções Azure)

Para trabalhos de fundo, é frequente garantir que apenas um exemplo de um determinado orquestrador é executado de cada vez. Você pode garantir este tipo de comportamento singleton em [Funções Duradouras,](durable-functions-overview.md) atribuindo um ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo singleton

O exemplo a seguir mostra uma função de gatilho HTTP que cria uma orquestração de trabalho de fundo singleton. O código garante que existe apenas um exemplo para um ID de instância especificado.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.js**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

---

Por padrão, os IDs de instância são gerados aleatoriamente GUIDs. No exemplo anterior, no entanto, o ID de instância é passado em dados de rota a partir do URL. O código chama `GetStatusAsync` (C#) ou `getStatus` (JavaScript) para verificar se uma instância com o ID especificado já está em execução. Se tal instância não estiver em execução, um novo caso é criado com esse ID.

> [!NOTE]
> Há uma condição de raça potencial nesta amostra. Se duas instâncias de **HttpStartSingle** executarem simultaneamente, ambas as chamadas de função reportarão o sucesso, mas apenas uma instância de orquestração irá realmente começar. Dependendo dos seus requisitos, isto pode ter efeitos colaterais indesejáveis. Por esta razão, é importante garantir que nenhum dois pedidos podem executar esta função de gatilho simultaneamente.

Os detalhes de implementação da função orquestradora não importam. Pode ser uma função orquestradora regular que começa e completa, ou pode ser uma que corre para sempre (isto é, uma [Orquestração Eterna).](durable-functions-eternal-orchestrations.md) O importante é que só há um caso a decorrer de cada vez.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Conheça as características http nativas das orquestrações](durable-functions-http-features.md)
