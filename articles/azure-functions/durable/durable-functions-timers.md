---
title: Temporizadores em Funções Duradouras - Azure
description: Saiba como implementar temporizadores duráveis na extensão funções duradouras para funções Azure.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876452"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores em Funções Duradouras (Funções Azure)

[Funções Duradouras](durable-functions-overview.md) proporcionam *temporizadores duradouros* para uso em funções de orquestrador para implementar atrasos ou para configurar intervalos de tempo em ações de async. Os temporizadores duráveis devem ser utilizados em funções de orquestrador em vez de `Thread.Sleep` e `Task.Delay` (C#), ou `setTimeout()` `setInterval()` (JavaScript), ou `time.sleep()` (Python).

Cria um temporizador durável chamando o `CreateTimer` método (.NET) ou o `createTimer` método (JavaScript) da ligação do gatilho de [orquestração](durable-functions-bindings.md#orchestration-trigger). O método devolve uma tarefa que completa numa data e hora especificadas.

## <a name="timer-limitations"></a>Limitações do temporizador

Quando se cria um temporizador que expira às 16h30, o Quadro de Tarefas Durável subjacente encoste uma mensagem que só fica visível às 16h30. Ao executar o plano de consumo de funções Azure, a mensagem do temporizador recentemente visível garantirá que a aplicação de função seja ativada num VM apropriado.

> [!NOTE]
> * Começando pela [versão 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) da Extensão Durável, os temporizadores duráveis são ilimitados. Nas versões anteriores da extensão, os temporizadores duráveis estão limitados a sete dias. Quando estiver a utilizar uma versão anterior e precisar de um atraso superior a sete dias, utilize as APIs do temporizador num `while` loop para simular este atraso.
> * Utilize sempre `CurrentUtcDateTime` em vez de em `DateTime.UtcNow` .NET ou em vez de `currentUtcDateTime` ou em `Date.now` `Date.UTC` JavaScript ao calcular o tempo de incêndio para temporizadores duradouros. Para obter mais informações, consulte o artigo de restrições de [função do orquestrador.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>Utilização para atraso

O exemplo a seguir ilustra como utilizar temporizadores duradouros para atrasar a execução. O exemplo é a emissão de uma notificação de faturação todos os dias durante 10 dias.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> O exemplo C# anterior visa funções duráveis 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> Evite laços infinitos nas funções orquestradoras. Para obter informações sobre como implementar cenários infinitos de ciclo infinito e de forma segura, consulte [Orquestrações Eternas.](durable-functions-eternal-orchestrations.md)

## <a name="usage-for-timeout"></a>Utilização para intervalo de tempo

Este exemplo ilustra como usar temporizadores duradouros para implementar intervalos de tempo.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> O exemplo C# anterior visa funções duráveis 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> Utilize um `CancellationTokenSource` (.NET) ou ligue `cancel()` para o devolvido `TimerTask` (JavaScript) para cancelar um temporizador durável se o seu código não esperar que este esteja concluído. O Quadro de Tarefas Duráveis não alterará o estatuto de orquestração para "concluído" até que todas as tarefas pendentes sejam concluídas ou canceladas.

Este mecanismo de cancelamento não termina a função de atividade em curso ou execuções de sub-orquestração. Pelo contrário, simplesmente permite que a função do orquestrador ignore o resultado e siga em frente. Se a sua aplicação de função utilizar o plano de Consumo, continuará a ser faturado por qualquer momento e memória consumida pela função de atividade abandonada. Por predefinição, as funções em execução no plano de consumo têm um intervalo de cinco minutos. Se este limite for ultrapassado, o hospedeiro Azure Functions é reciclado para impedir toda a execução e evitar uma situação de faturação em fuga. O [tempo limite de função é configurável](../functions-host-json.md#functiontimeout).

Para obter um exemplo mais aprofundado de como implementar intervalos de tempo em funções orquestradoras, consulte o [artigo timeouts & Timeouts de Interação Humana - Verificação de Telefone.](durable-functions-phone-verification.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como levantar e lidar com eventos externos](durable-functions-external-events.md)
