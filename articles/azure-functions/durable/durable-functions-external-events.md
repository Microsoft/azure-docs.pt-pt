---
title: Lidar com eventos externos em Funções Duradouras - Azure
description: Saiba como lidar com eventos externos na extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: a7625a6fcd1000595c2c582935c839ba6d26b20d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728492"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Lidar com eventos externos em Funções Duradouras (Funções Azure)

As funções orquestradoras têm a capacidade de esperar e ouvir eventos externos. Esta característica de [Funções Duradouras](durable-functions-overview.md) é frequentemente útil para lidar com a interação humana ou outros gatilhos externos.

> [!NOTE]
> Eventos externos são operações assíncronos. Não são adequados para situações em que o cliente que envia o evento necessita de uma resposta sincronizada da função orquestradora.

## <a name="wait-for-events"></a>Aguarde os eventos

Os métodos [WaitForExternalEvent](/dotnet/api/microsoft.azure.webjobs.durableorchestrationcontextbase.waitforexternalevent?view=azure-dotnet-legacy&preserve-view=true) (.NET), `waitForExternalEvent` (JavaScript) e `wait_for_external_event` (Python) da [ligação](durable-functions-bindings.md#orchestration-trigger) do gatilho da orquestração permitem que uma função orquestradora aguarde assíncronamente e ouça um evento externo. A função de orquestrador de escuta declara o *nome* do evento e a *forma dos dados* que espera receber.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

O exemplo anterior ouve um evento específico e toma medidas quando é recebido.

Pode ouvir vários eventos em simultâneo, como no exemplo seguinte, que aguarda uma das três possíveis notificações de eventos.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

O exemplo anterior ouve *qualquer um* dos múltiplos eventos. Também é possível esperar por *todos os* eventos.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> O código anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

Em .NET, se a carga útil do evento não puder ser convertida no tipo `T` esperado, é lançada uma exceção.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` espera indefinidamente por alguma entrada.  A aplicação de função pode ser descarregada com segurança enquanto espera. Se e quando um evento chega para esta instância de orquestração, é despertado automaticamente e processa imediatamente o evento.

> [!NOTE]
> Se a sua aplicação de função utilizar o Plano de Consumo, não são incorridos encargos de faturação enquanto uma função de orquestrador aguarda uma tarefa de `WaitForExternalEvent` (.NET), `waitForExternalEvent` (JavaScript) ou `wait_for_external_event` (Python), não importa quanto tempo espere.

## <a name="send-events"></a>Enviar eventos

Pode utilizar os métodos [RaiseEventAsync](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclientbase.raiseeventasync?view=azure-dotnet-legacy&preserve-view=true) (.NET) ou `raiseEventAsync` (JavaScript) para enviar um evento externo para uma orquestração. Estes métodos são expostos pela ligação do cliente da [orquestração.](durable-functions-bindings.md#orchestration-client) Também pode utilizar o evento de elevação incorporado [HTTP API](durable-functions-http-api.md#raise-event) para enviar um evento externo para uma orquestração.

Um evento elevado inclui um *ID de exemplo,* um *nome de eventos,* e *o EventData* como parâmetros. As funções de orquestrador lidam com estes eventos usando as `WaitForExternalEvent` APIs (.NET) ou `waitForExternalEvent` (JavaScript). O *nome do evento* deve coincidir tanto com as extremidades de envio como de receção para que o evento seja processado. Os dados do evento também devem ser serializáveis por JSON.

Internamente, os mecanismos de "raise event" encostam uma mensagem que é captada pela função de orquestrador de espera. Se a instância não estiver à espera do nome especificado do *evento,* a mensagem do evento é adicionada a uma fila de memória. Se a instância de orquestração começar mais tarde a ouvir o nome do *evento,* verificará a fila para obter mensagens de evento.

> [!NOTE]
> Se não houver qualquer instância de orquestração com o *ID de instância* especificado, a mensagem do evento é descartada.

Abaixo está uma função de exemplo desencadeada por filas que envia um evento de "Aprovação" para uma instância de função orquestradora. A identificação do caso de orquestração vem do corpo da mensagem da fila.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> O código C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve utilizar o tipo de `DurableOrchestrationClient` parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Internamente, `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript) ou `raise_event` (Python) encoste uma mensagem que é captada pela função de orquestrador de espera. Se a instância não estiver à espera do nome especificado do *evento,* a mensagem do evento é adicionada a uma fila de memória. Se a instância de orquestração começar mais tarde a ouvir o nome do *evento,* verificará a fila para obter mensagens de evento.

> [!NOTE]
> Se não houver qualquer instância de orquestração com o *ID de instância* especificado, a mensagem do evento é descartada.

### <a name="http"></a>HTTP

Segue-se um exemplo de um pedido HTTP que eleva um evento de "Aprovação" a uma instância de orquestração. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

Neste caso, o ID de exemplo é codificado como *MyInstanceId*.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar o tratamento de erros](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Executar uma amostra que espera pela interação humana](durable-functions-phone-verification.md)