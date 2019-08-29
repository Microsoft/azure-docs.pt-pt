---
title: Temporizadores no Durable Functions-Azure
description: Saiba como implementar temporizadores duráveis na extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: 11edfc11fc1e54684a99774c21517d4c322348b1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087049"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores em Durable Functions (Azure Functions)

O [Durable Functions](durable-functions-overview.md) fornece *temporizadores duráveis* para uso em funções de orquestrador para implementar atrasos ou para configurar tempos limite em ações assíncronas. Temporizadores duráveis devem ser usados em funções de orquestrador `Thread.Sleep` em `Task.Delay` vezC#de e ( `setTimeout()` ) `setInterval()` ou e (JavaScript).

Você cria um temporizador durável chamando o [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) método CreateTimer de [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) no .net ou o `createTimer` método de `DurableOrchestrationContext` em JavaScript. O método retorna uma tarefa que é retomada em uma data e hora especificadas.

## <a name="timer-limitations"></a>Limitações do temporizador

Quando você cria um temporizador que expira às 4:30 PM, o Framework de tarefa durável subjacente enfileira uma mensagem que se torna visível somente às 4:30 PM. Ao executar no plano de consumo de Azure Functions, a mensagem de temporizador recentemente visível garantirá que o aplicativo de funções seja ativado em uma VM apropriada.

> [!NOTE]
> * Os temporizadores duráveis não podem durar mais de 7 dias devido a limitações no armazenamento do Azure. Estamos trabalhando em uma [solicitação de recurso para estender temporizadores além de 7 dias](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Sempre use [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) em vez `DateTime.UtcNow` de em .NET `currentUtcDateTime` e em `Date.now` vez `Date.UTC` de ou em JavaScript, conforme mostrado nos exemplos abaixo ao calcular um prazo relativo de um temporizador durável.

## <a name="usage-for-delay"></a>Uso de atraso

O exemplo a seguir ilustra como usar temporizadores duráveis para atrasar a execução. O exemplo está emitindo uma notificação de cobrança todos os dias por dez dias.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Evite loops infinitos em funções de orquestrador. Para obter informações sobre como implementar de forma segura e eficiente cenários de loop infinito, consulte orquestrações de [eternas](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Uso para tempo limite

Este exemplo ilustra como usar temporizadores duráveis para implementar tempos limite.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Use um `CancellationTokenSource` para cancelar um temporizador durávelC#() ou `cancel()` chamar sobre o `TimerTask` retornado (JavaScript) se o seu código não esperar que ele seja concluído. O Framework de tarefa durável não alterará o status de uma orquestração para "concluído" até que todas as tarefas pendentes sejam concluídas ou canceladas.

Esse mecanismo não encerra realmente a execução da função de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e passe. Se seu aplicativo de funções usar o plano de consumo, você ainda será cobrado por qualquer tempo e memória consumida pela função de atividade abandonada. Por padrão, as funções em execução no plano de consumo têm um tempo limite de cinco minutos. Se esse limite for excedido, o host de Azure Functions será reciclado para interromper toda a execução e evitar uma situação de cobrança de fuga. O [tempo limite da função é configurável](../functions-host-json.md#functiontimeout).

Para obter um exemplo mais detalhado de como implementar tempos limite em funções de orquestrador, consulte a [interação humana & tempos limite de verificação de telefone](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como gerar e manipular eventos externos](durable-functions-external-events.md)
