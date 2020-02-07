---
title: Manipulando erros no Durable Functions-Azure
description: Saiba como tratar erros na extensão de Durable Functions para Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047248"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Tratamento de erros em Durable Functions (Azure Functions)

Orquestrações de função duráveis são implementadas no código e podem usar os recursos internos de tratamento de erros da linguagem de programação. Na verdade, não há nenhum novo conceito que você precise aprender para adicionar o tratamento de erros e a compensação às suas orquestrações. No entanto, há alguns comportamentos que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Qualquer exceção que seja lançada numa função de atividade é remeterida para a função de orquestrador e lançada como uma `FunctionFailedException`. Você pode escrever código de compensação e tratamento de erros que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador que transfere fundos de uma conta para outra:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Os exemplos C# anteriores são para Durable Functions 2. x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

---

Se a primeira chamada de função **CreditAccount** falhar, a função orquestradora compensa creditando os fundos de volta à conta fonte.

## <a name="automatic-retry-on-failure"></a>Nova tentativa automática em caso de falha

Quando você chama funções de atividade ou funções de suborquestração, pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e aguarda 5 segundos entre cada repetição:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Os exemplos C# anteriores são para Durable Functions 2. x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

A chamada de função de atividade no exemplo anterior usa um parâmetro para configurar uma política de repetição automática. Há várias opções para personalizar a política de repetição automática:

* **Número máximo de tentativas**: O número máximo de tentativas de retenção.
* Primeiro intervalo de **repetição:** O tempo de espera antes da primeira tentativa de repetição.
* **Coeficiente de backoff:** O coeficiente utilizado para determinar a taxa de aumento do recuo. Incumprimentos para 1.
* Intervalo de **repetição máximo**: O tempo máximo para esperar entre tentativas de repetição.
* **Retry timeout**: O máximo de tempo para passar a fazer repetições. O comportamento padrão é repetir indefinidamente.
* **Manipule**: Pode ser especificada uma chamada definida pelo utilizador para determinar se uma função deve ser novamente tentada.

## <a name="function-timeouts"></a>Tempos limite da função

Talvez você queira abandonar uma chamada de função dentro de uma função de orquestrador se estiver demorando muito tempo para ser concluída. A maneira correta de o fazer hoje é criando um [temporizador durável](durable-functions-timers.md) utilizando `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript) em conjunto com `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript), como no seguinte exemplo:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Os exemplos C# anteriores são para Durable Functions 2. x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

---

> [!NOTE]
> Esse mecanismo não encerra realmente a execução da função de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e passe. Para mais informações, consulte a documentação dos [Timers.](durable-functions-timers.md#usage-for-timeout)

## <a name="unhandled-exceptions"></a>Exceções não processadas

Se uma função de orquestrador falhar com uma exceção não tratada, os detalhes da exceção são registados e a instância completa com um estatuto de `Failed`.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre orquestrações eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
