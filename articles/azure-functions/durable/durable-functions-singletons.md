---
title: Singletons para Durable Functions-Azure
description: Como usar singletons na extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9bf9687f60e649fee98869ef263117177ad5efd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097935"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Durable Functions (Azure Functions)

Para trabalhos em segundo plano, você geralmente precisa garantir que apenas uma instância de um orquestrador específico seja executada por vez. Isso pode ser feito em [Durable Functions](durable-functions-overview.md) atribuindo uma ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

Os exemplos C# a seguir e JavaScript mostram uma função de gatilho http que cria uma orquestração de trabalho em segundo plano singleton. O código garante que apenas uma instância exista para uma ID de instância especificada.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
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

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

Este é o ficheiro de Function:
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

Eis o código JavaScript:
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

Por padrão, as IDs de instância são GUIDs gerados aleatoriamente. Mas, nesse caso, a ID da instância é passada em dados de rota da URL. O código chama [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) ou `getStatus` (JavaScript) para verificar se uma instância com a ID especificada já está em execução. Caso contrário, uma instância será criada com essa ID.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, será necessário definir a variável `WEBSITE_HOSTNAME` de ambiente como, por `localhost:<port>`exemplo, `localhost:7071`para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, consulte o [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> Há uma possível condição de corrida neste exemplo. Se duas instâncias do **HttpStartSingle** forem executadas simultaneamente, ambas as chamadas de função relatarão êxito, mas apenas uma instância de orquestração será iniciada de fato. Dependendo dos seus requisitos, isso pode ter efeitos colaterais indesejáveis. Por esse motivo, é importante garantir que duas solicitações não possam executar essa função de gatilho simultaneamente.

Os detalhes de implementação da função de orquestrador não são realmente importantes. Pode ser uma função de orquestrador regular que é iniciada e concluída, ou pode ser uma que é executada para sempre (ou seja, uma [orquestração eternas](durable-functions-eternal-orchestrations.md)). O ponto importante é que há apenas uma instância em execução por vez.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como chamar suborquestrações](durable-functions-sub-orchestrations.md)
