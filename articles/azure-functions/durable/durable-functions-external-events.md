---
title: Lidar com eventos externos em Funções Duradouras - Azure
description: Saiba como lidar com eventos externos na extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 387b5d920de4a295366cc7e948862a12cea901d3
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165554"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Lidar com eventos externos em Funções Duradouras (Funções Azure)

As funções orquestradoras têm a capacidade de esperar e ouvir eventos externos. Esta característica de [Funções Duradouras](durable-functions-overview.md) é frequentemente útil para lidar com a interação humana ou outros gatilhos externos.

> [!NOTE]
> Eventos externos são operações assíncronos. Não são adequados para situações em que o cliente que envia o evento necessita de uma resposta sincronizada da função orquestradora.

## <a name="wait-for-events"></a>Aguarde os eventos

Os métodos [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) e `waitForExternalEvent` (JavaScript) da ligação do gatilho da [orquestração](durable-functions-bindings.md#orchestration-trigger) permitem que uma função orquestradora aguarde assíncronamente e ouça um evento externo. A função de orquestrador de escuta declara o *nome* do evento e a *forma dos dados* que espera receber.

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

---

`WaitForExternalEvent`espera indefinidamente por alguma entrada.  A aplicação de função pode ser descarregada com segurança enquanto espera. Se e quando um evento chega para esta instância de orquestração, é despertado automaticamente e processa imediatamente o evento.

> [!NOTE]
> Se a sua aplicação de função utilizar o Plano de Consumo, não são incorridos encargos de faturação enquanto uma função de orquestrador aguarda uma tarefa de `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` (JavaScript), não importa quanto tempo espere.

## <a name="send-events"></a>Enviar eventos

Pode utilizar os métodos [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) ou `raiseEventAsync` (JavaScript) para enviar um evento externo para uma orquestração. Estes métodos são expostos pela ligação do cliente da [orquestração.](durable-functions-bindings.md#orchestration-client) Também pode utilizar o evento de elevação incorporado [HTTP API](durable-functions-http-api.md#raise-event) para enviar um evento externo para uma orquestração.

Um evento elevado inclui um *ID de exemplo,* um *nome de eventos,* e *o EventData* como parâmetros. As funções de orquestrador lidam com estes eventos usando as `WaitForExternalEvent` APIs (.NET) ou `waitForExternalEvent` (JavaScript). O *nome do evento* deve coincidir tanto com as extremidades de envio como de receção para que o evento seja processado. Os dados do evento também devem ser serializáveis por JSON.

Internamente, os mecanismos de "raise event" encostam uma mensagem que é captada pela função de orquestrador de espera. Se a instância não estiver à espera do nome especificado do *evento,* a mensagem do evento é adicionada a uma fila de memória. Se a instância de orquestração começar mais tarde a ouvir o nome do *evento,* verificará a fila para obter mensagens de evento.

> [!NOTE]
> Se não houver qualquer instância de orquestração com o *ID de instância*especificado, a mensagem do evento é descartada.

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

---

Internamente, `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript) encoste uma mensagem que é captada pela função de orquestrador de espera. Se a instância não estiver à espera do nome especificado do *evento,* a mensagem do evento é adicionada a uma fila de memória. Se a instância de orquestração começar mais tarde a ouvir o nome do *evento,* verificará a fila para obter mensagens de evento.

> [!NOTE]
> Se não houver qualquer instância de orquestração com o *ID de instância*especificado, a mensagem do evento é descartada.

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