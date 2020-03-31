---
title: Singletons para Funções Duráveis - Azure
description: Como utilizar singletons na extensão Funções Duráveis para funções azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262814"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores Singleton em Funções Duráveis (Funções Azure)

Para trabalhos de fundo, é frequente garantir que apenas um exemplo de um determinado orquestrador funciona de cada vez. Você pode garantir este tipo de comportamento singleton em [Funções Duráveis](durable-functions-overview.md) atribuindo uma identificação de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo singleton

O exemplo seguinte mostra uma função http-trigger que cria uma orquestração de trabalho de fundo singleton. O código garante que existe apenas uma instância para uma identificação de instância especificada.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**função.json**

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

Por padrão, os IDs de instância são GUIDs gerados aleatoriamente. No exemplo anterior, no entanto, o ID de instância é passado em dados de rota a partir do URL. As chamadas `GetStatusAsync`de código `getStatus` (C#) ou (JavaScript) para verificar se uma instância com o ID especificado já está em execução. Se tal instância não estiver a decorrer, é criado um novo exemplo com esse ID.

> [!NOTE]
> Há uma condição de raça potencial nesta amostra. Se duas instâncias de **HttpStartSingle** executarem simultaneamente, ambas as chamadas de função reportarão sucesso, mas apenas uma instância de orquestração começará. Dependendo dos seus requisitos, isto pode ter efeitos colaterais indesejáveis. Por esta razão, é importante garantir que nenhum pedido pode executar esta função de gatilho simultaneamente.

Os detalhes de implementação da função orquestradora não importam. Pode ser uma função orquestradora regular que começa e completa, ou pode ser uma que corre para sempre (isto é, uma [Orquestração Eterna).](durable-functions-eternal-orchestrations.md) O importante é que só há um caso a decorrer de cada vez.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça as características nativas do HTTP das orquestrações](durable-functions-http-features.md)
