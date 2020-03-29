---
title: Tratamento de eventos externos em Funções Duráveis - Azure
description: Saiba lidar com eventos externos na extensão funções duráveis para funções azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262967"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Manuseamento de eventos externos em Funções Duráveis (Funções Azure)

As funções de orquestrador têm a capacidade de esperar e ouvir eventos externos. Esta característica das [Funções Duráveis é muitas vezes](durable-functions-overview.md) útil para lidar com a interação humana ou outros gatilhos externos.

> [!NOTE]
> Eventos externos são operações assíncronas de sentido único. Não são adequados para situações em que o cliente que envia o evento precisa de uma resposta sincronizada da função orquestradora.

## <a name="wait-for-events"></a>Aguarde os eventos

Os `WaitForExternalEvent` métodos (.NET) e `waitForExternalEvent` (JavaScript) da ligação do gatilho da [orquestração](durable-functions-bindings.md#orchestration-trigger) permitem que uma função orquestradora aguarde e ouça um evento externo. A função de orquestrador de escuta declara o *nome* do evento e a *forma dos dados* que espera receber.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

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

O exemplo anterior ouve um único evento específico e toma medidas quando é recebido.

Pode ouvir vários eventos em simultâneo, como no seguinte exemplo, que aguarda uma das três notificações possíveis do evento.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

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

O exemplo anterior ouve *qualquer* um dos múltiplos eventos. Também é possível esperar por *todos os* eventos.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

Em .NET, se a carga útil do `T`evento não puder ser convertida no tipo esperado, é lançada uma exceção.

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

`WaitForExternalEvent`espera indefinidamente por alguma entrada.  A aplicação de funções pode ser descarregada com segurança enquanto espera. Se e quando um evento chega para esta instância de orquestração, é despertado automaticamente e imediatamente processa o evento.

> [!NOTE]
> Se a sua aplicação de funções utilizar o Plano de Consumo, não `WaitForExternalEvent` são incorridos encargos de faturação enquanto uma função de orquestrador aguarda uma tarefa de (.NET) ou `waitForExternalEvent` (JavaScript), não importa quanto tempo espere.

## <a name="send-events"></a>Enviar eventos

O `RaiseEventAsync` método (.NET) `raiseEvent` ou (JavaScript) da ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) envia os eventos que `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` (JavaScript) esperam.  O `RaiseEventAsync` método toma o *nome do evento* e o *eventoData* como parâmetros. Os dados do evento devem ser jSON-serializáveis.

Abaixo está uma função de exemplo desencadeada pela fila que envia um evento de "Aprovação" para uma instância de função orquestradora. A identificação da instância de orquestração vem do corpo da mensagem da fila.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve `OrchestrationClient` utilizar `DurableClient` o atributo em `DurableOrchestrationClient` vez do atributo, e deve utilizar o tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Internamente, `RaiseEventAsync` (.NET) `raiseEvent` ou (JavaScript) enfila uma mensagem que é captada pela função de orquestrador de espera. Se a instância não estiver à espera do nome do *evento especificado,* a mensagem do evento é adicionada a uma fila na memória. Se a instância de orquestração começar mais tarde a ouvir o nome do *evento,* verificará a fila para obter mensagens de evento.

> [!NOTE]
> Se não houver uma instância de orquestração com o ID de *instância*especificado, a mensagem do evento é descartada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar o tratamento de erros](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Executar uma amostra que espera pela interação humana](durable-functions-phone-verification.md)