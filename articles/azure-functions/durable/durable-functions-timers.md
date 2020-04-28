---
title: Temporizadores em Funções Duráveis - Azure
description: Aprenda a implementar tempos duráveis na extensão funções duráveis para funções azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76261488"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores em Funções Duráveis (Funções Azure)

[As Funções Duráveis](durable-functions-overview.md) fornecem *tempos duradouros* para serem utilizados em funções de orquestradores para implementar atrasos ou para configurar tempos em ações de sincronização. Os tempoizadors duráveis devem ser utilizados em funções `setTimeout()` `setInterval()` de orquestrador em vez de `Thread.Sleep` e `Task.Delay` (C#), ou (JavaScript).

Cria-se um temporizador `CreateTimer` durável, ligando `createTimer` para o método (.NET) ou o método (JavaScript) da ligação do gatilho da [orquestração](durable-functions-bindings.md#orchestration-trigger). O método devolve uma tarefa que completa numa data e hora especificadas.

## <a name="timer-limitations"></a>Limitações do temporizador

Quando se cria um temporizador que expira às 16h30, o Quadro de Tarefas Duráveis subjacente insere uma mensagem que só fica visível às 16h30. Ao executar o plano de consumo de funções Azure, a mensagem temporizador recentemente visível irá garantir que a aplicação de função é ativada num VM apropriado.

> [!NOTE]
> * Os tempos duradouros estão atualmente limitados a 7 dias. Se forem necessários atrasos mais longos, podem ser `while` simulados utilizando as APIs do temporizador num loop.
> * `CurrentUtcDateTime` Utilize sempre `DateTime.UtcNow` em vez `currentUtcDateTime` de `Date.now` em `Date.UTC` .NET ou em vez de ou em JavaScript ao calcular o tempo de incêndio para os tempos de incêndio duráveis. Para mais informações, consulte o artigo de restrições de código de [função orquestrador.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>Utilização por atraso

O exemplo que se segue ilustra como utilizar tempos duradouros para atrasar a execução. O exemplo é a emissão de uma notificação de faturação todos os dias durante 10 dias.

# <a name="c"></a>[C #](#tab/csharp)

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
> O exemplo c# anterior visa funções duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

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

---

> [!WARNING]
> Evite laços infinitos em funções orquestradoras. Para obter informações sobre como implementar de forma segura e eficiente cenários de loop infinitos, consulte [As Orquestrações Eternas.](durable-functions-eternal-orchestrations.md)

## <a name="usage-for-timeout"></a>Utilização para intervalos

Este exemplo ilustra como usar tempos duradouros para implementar tempos.

# <a name="c"></a>[C #](#tab/csharp)

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
> O exemplo c# anterior visa funções duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

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

---

> [!WARNING]
> Utilize `CancellationTokenSource` um (.NET) `cancel()` ou `TimerTask` ligue para o (JavaScript) devolvido para cancelar um temporizador durável se o seu código não esperar que o seu código esteja completo. O Quadro de Tarefas Duráveis não alterará o estatuto de orquestração para "concluído" até que todas as tarefas pendentes estejam concluídas ou canceladas.

Este mecanismo de cancelamento não termina a função de atividade em curso ou as execuções de sub-orquestração. Pelo contrário, simplesmente permite que a função orquestradora ignore o resultado e siga em frente. Se a sua aplicação de funções utilizar o plano de consumo, ainda será faturada por qualquer momento e memória consumida pela função de atividade abandonada. Por defeito, as funções em execução no plano de consumo têm um prazo de cinco minutos. Se este limite for ultrapassado, o hospedeiro das Funções Azure é reciclado para impedir toda a execução e evitar uma situação de faturação em fuga. O tempo de tempo de [função é configurável](../functions-host-json.md#functiontimeout).

Para um exemplo mais aprofundado de como implementar intervalos em funções orquestradoras, consulte o artigo de [Interação Humana & Timeouts - Artigo](durable-functions-phone-verification.md) de Verificação de Telefones.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como levantar e lidar com eventos externos](durable-functions-external-events.md)
