---
title: Singletons para Funções Duradouras - Azure
description: Como utilizar singletons na extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: deb64cf8128fd548cb74c064ab9fd6f169db5300
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87041929"
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

**function.json**

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

# <a name="python"></a>[Python](#tab/python)

**function.json**

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

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

Por padrão, os IDs de instância são gerados aleatoriamente GUIDs. No exemplo anterior, no entanto, o ID de instância é passado em dados de rota a partir do URL. O código chama `GetStatusAsync` (C#), `getStatus` (JavaScript) ou `get_status` (Python) para verificar se um caso com o ID especificado já está em execução. Se tal instância não estiver em execução, um novo caso é criado com esse ID.

> [!NOTE]
> Há uma condição de raça potencial nesta amostra. Se duas instâncias de **HttpStartSingle** executarem simultaneamente, ambas as chamadas de função reportarão o sucesso, mas apenas uma instância de orquestração irá realmente começar. Dependendo dos seus requisitos, isto pode ter efeitos colaterais indesejáveis. Por esta razão, é importante garantir que nenhum dois pedidos podem executar esta função de gatilho simultaneamente.

Os detalhes de implementação da função orquestradora não importam. Pode ser uma função orquestradora regular que começa e completa, ou pode ser uma que corre para sempre (isto é, uma [Orquestração Eterna).](durable-functions-eternal-orchestrations.md) O importante é que só há um caso a decorrer de cada vez.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça as características http nativas das orquestrações](durable-functions-http-features.md)
