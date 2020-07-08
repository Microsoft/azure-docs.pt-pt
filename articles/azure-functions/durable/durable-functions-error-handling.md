---
title: Erros de manuseamento em Funções Duradouras - Azure
description: Saiba como lidar com erros na extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85847321"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Erros de manuseamento em funções duradouras (Funções Azure)

As orquestrações de funções duradouras são implementadas em código e podem utilizar as funcionalidades de tratamento de erros incorporadas da linguagem de programação. Não há novos conceitos que precises de aprender a adicionar manipulação de erros e compensação às tuas orquestrações. No entanto, há alguns comportamentos que deve estar ciente.

## <a name="errors-in-activity-functions"></a>Erros nas funções de atividade

Qualquer exceção que seja lançada numa função de atividade é recedida para a função de orquestrador e lançada como `FunctionFailedException` um . Pode escrever código de manipulação de erros e compensação que se adapte às suas necessidades na função orquestradora.

Por exemplo, considere a seguinte função orquestradora que transfere fundos de uma conta para outra:

# <a name="c"></a>[C#](#tab/csharp)

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
> Os exemplos C# anteriores são para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Se a primeira chamada de função **CreditAccount** falhar, a função do orquestrador compensa creditando os fundos de volta à conta de origem.

## <a name="automatic-retry-on-failure"></a>Reagem automática à falha

Quando ligar para funções de atividade ou funções de sub-orquestração, pode especificar uma política de repetição automática. O exemplo a seguir tenta convocar uma função até três vezes e espera 5 segundos entre cada repetição:

# <a name="c"></a>[C#](#tab/csharp)

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
> Os exemplos C# anteriores são para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

A chamada de função de atividade no exemplo anterior requer um parâmetro para configurar uma política de retenção automática. Existem várias opções para personalizar a política de relagem automática:

* **Número máximo de tentativas**: O número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: A quantidade de tempo para esperar antes da primeira tentativa de repetição.
* **Coeficiente de recuo**: O coeficiente utilizado para determinar a taxa de aumento do recuo. Incumprimentos para 1.
* **Intervalo de retenção máxima**: O tempo máximo para esperar entre tentativas de repetição.
* **Relem os tempos limites:** O tempo máximo para passar a fazer recauchutagens. O comportamento padrão é voltar a tentar indefinidamente.
* **Cabo**: Pode especificar-se uma chamada definida pelo utilizador para determinar se uma função deve ser novamente experimentada.

## <a name="function-timeouts"></a>Intervalos de tempo de função

É melhor abandonar uma chamada de função dentro de uma função de orquestrador se demorar muito tempo a ser concluída. A forma correta de o fazer hoje é criando um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript) em conjunto com `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript), como no exemplo seguinte:

# <a name="c"></a>[C#](#tab/csharp)

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
> Os exemplos C# anteriores são para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
> Este mecanismo não termina efetivamente a execução da função de atividade em curso. Pelo contrário, simplesmente permite que a função do orquestrador ignore o resultado e siga em frente. Para mais informações, consulte a documentação dos [Temporizadores.](durable-functions-timers.md#usage-for-timeout)

## <a name="unhandled-exceptions"></a>Exceções não processadas

Se uma função orquestradora falhar com uma exceção não manipulada, os detalhes da exceção são registados e o caso completa-se com um `Failed` estatuto.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Conheça as orquestrações eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
