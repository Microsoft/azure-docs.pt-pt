---
title: Singletons para Durable Functions-Azure
description: Como usar singletons na extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: ec7eb1eba2bc029d592560b39cde20e93e5afcd6
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614670"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Durable Functions (Azure Functions)

Para trabalhos em segundo plano, você geralmente precisa garantir que apenas uma instância de um orquestrador específico seja executada por vez. Você pode garantir esse tipo de comportamento singleton no [Durable Functions](durable-functions-overview.md) atribuindo uma ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

O exemplo a seguir mostra uma função HTTP-Trigger que cria uma orquestração de trabalho em segundo plano singleton. O código garante que apenas uma instância exista para uma ID de instância especificada.

### <a name="c"></a>C#

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
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar o atributo `OrchestrationClient` em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2,0)

Este é o arquivo function. JSON:
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

Este é o código JavaScript:
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

Por padrão, as IDs de instância são GUIDs gerados aleatoriamente. No exemplo anterior, no entanto, a ID da instância é passada em dados de rota da URL. O código chama `GetStatusAsync`(C#) ou `getStatus` (JavaScript) para verificar se uma instância com a ID especificada já está em execução. Se nenhuma instância desse tipo estiver em execução, uma nova instância será criada com essa ID.

> [!NOTE]
> Há uma possível condição de corrida neste exemplo. Se duas instâncias do **HttpStartSingle** forem executadas simultaneamente, ambas as chamadas de função relatarão êxito, mas apenas uma instância de orquestração será iniciada de fato. Dependendo dos seus requisitos, isso pode ter efeitos colaterais indesejáveis. Por esse motivo, é importante garantir que duas solicitações não possam executar essa função de gatilho simultaneamente.

Os detalhes de implementação da função de orquestrador não são realmente importantes. Pode ser uma função de orquestrador regular que é iniciada e concluída, ou pode ser uma que é executada para sempre (ou seja, uma [orquestração eternas](durable-functions-eternal-orchestrations.md)). O ponto importante é que há apenas uma instância em execução por vez.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os recursos HTTP nativos de orquestrações](durable-functions-http-features.md)
