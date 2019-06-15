---
title: Singletons para funções duráveis - Azure
description: Como utilizar singletons na extensão de funções duráveis para as funções do Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c032ba046668310ff71d067d22a805fc6446667c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683808"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores de singleton nas funções durável (funções do Azure)

Para tarefas em segundo plano, muitas vezes, precisa garantir que apenas uma instância de um orquestrador específico é executado cada vez. Isso pode ser feito [funções duráveis](durable-functions-overview.md) atribuindo específicas de uma ID de instância para um orquestrador durante a criação.

## <a name="singleton-example"></a>Exemplo de singleton

O seguinte C# e exemplos de JavaScript mostram uma função de Acionador de HTTP que cria uma orquestração de tarefa em segundo plano singleton. O código garante que apenas uma instância não existe para um ID de instância especificada.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

Por predefinição, a instância IDs são aleatoriamente gerado GUIDs. Mas, neste caso, o ID de instância é passado encaminhar os dados da URL. O código chama [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) ou `getStatus` (JavaScript) para verificar se uma instância com o ID especificado já está em execução. Se não for, é criada uma instância com esse ID.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, terá de definir a variável de ambiente `WEBSITE_HOSTNAME` para `localhost:<port>`, por ex. `localhost:7071` Para utilizar os métodos em `DurableOrchestrationClient`. Para obter mais informações sobre este requisito, consulte a [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> Neste exemplo, existe uma condição de corrida potencial. Se duas instâncias da **HttpStartSingle** executadas simultaneamente, ambas as chamadas de função reportará o êxito, mas a instância de apenas uma orquestração, na verdade, será iniciado. Dependendo dos requisitos, isso pode ter efeitos de colaterais indesejáveis. Por esse motivo, é importante certificar-se de que não existem dois pedidos podem executar esta função de Acionador em simultâneo.

Os detalhes da implementação da função de orquestrador, na verdade, não importa. Pode ser uma função de orquestrador regular que é iniciada e concluída, ou pode ser que seja executado Eternamente (ou seja, uma [orquestração externas](durable-functions-eternal-orchestrations.md)). O ponto importante é que há apenas nunca uma instância em execução ao mesmo tempo.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como chamar orquestrações secundárias](durable-functions-sub-orchestrations.md)
