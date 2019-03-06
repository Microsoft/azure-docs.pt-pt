---
title: Temporizadores nas funções duráveis - Azure
description: Saiba como implementar temporizadores duráveis na extensão de funções duráveis para as funções do Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: d225ece7b8a8841d17f20bc27de3aa640fa7d37b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436430"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores nas funções duráveis (funções do Azure)

[Funções duráveis](durable-functions-overview.md) fornece *temporizadores duráveis* para utilização nas funções do orchestrator para implementar atrasos ou configurar tempos limite de ações de async. Temporizadores duráveis devem ser utilizados nas funções do orchestrator, em vez de `Thread.Sleep` e `Task.Delay` (c#), ou `setTimeout()` e `setInterval()` (JavaScript).

Criar um temporizador durável chamando o [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) método [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) no .NET, ou o `createTimer` método de `DurableOrchestrationContext` em JavaScript. O método retorna uma tarefa que retoma uma data e hora especificadas.

## <a name="timer-limitations"></a>Limitações de temporizador

Quando cria um temporizador que expira às 4:30 pm, a coloca em fila de Framework durável de tarefa subjacente uma mensagem que se torna visível apenas no 4:30 pm. Quando em execução no plano de consumo das funções do Azure, a mensagem de temporizador recentemente visível garantirá que a aplicação de funções é ativada numa VM adequada.

> [!NOTE]
> * Temporizadores duráveis não podem durar mais do que 7 dias devido a limitações no armazenamento do Azure. Estamos a trabalhar num [pedido de funcionalidade para aumentar a temporizadores, para além de 7 dias](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Utilize sempre [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) em vez de `DateTime.UtcNow` no .NET e `currentUtcDateTime` em vez de `Date.now` ou `Date.UTC` em JavaScript, conforme mostrado nos exemplos abaixo ao computar um prazo relativo de um temporizador durável .

## <a name="usage-for-delay"></a>Utilização de atraso

O exemplo a seguir ilustra como usar temporizadores duráveis para atrasar a execução. O exemplo está a emitir uma notificação de faturação de todos os dias para dez dias.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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
> Evite loops infinitos nas funções do orchestrator. Para obter informações sobre como com segurança e eficiência implementar cenários de loop infinito, consulte [Orquestrações externas](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Utilização para o tempo limite

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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
> Utilize um `CancellationTokenSource` para cancelar um temporizador durável (c#) ou uma chamada `cancel()` retornado no `TimerTask` (JavaScript), se seu código não aguarda que este seja concluído. A estrutura de tarefa durável não irá alterar o estado de uma orquestração como "concluído" até que todas as tarefas pendentes sejam concluídas ou foi canceladas.

Esse mecanismo não encerra, na verdade, a execução de função da atividade em curso. Em vez disso, ele simplesmente permite que a função de orquestrador ignorar o resultado e avançar. Se a sua aplicação function app utiliza o plano de consumo, ainda será cobrado para qualquer tempo e memória consumida pela função atividade abandonados. Por predefinição, as funções em execução no plano de consumo têm um tempo limite de cinco minutos. Se este limite for excedido, o anfitrião de funções do Azure é reciclado para parar a execução de todas as e impede uma situação de faturação de fuga. O [tempo limite da função é configurável](../functions-host-json.md#functiontimeout).

Para obter um exemplo mais aprofundado de como implementar tempos limite nas funções do orchestrator, consulte a [interação humana & tempos-limite de verificação do telefone](durable-functions-phone-verification.md) passo a passo.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como gerar e manipular eventos externos](durable-functions-external-events.md)
