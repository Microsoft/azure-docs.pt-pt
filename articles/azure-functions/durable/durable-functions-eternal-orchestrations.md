---
title: Orquestrações externas nas funções duráveis - Azure
description: Saiba como implementar orquestrações externas com a extensão de funções duráveis para as funções do Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 99eabf3bc91887ff19b3a0bc9cf6647d32fa6750
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787555"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações externas nas funções durável (funções do Azure)

*Orquestrações externas* são as funções do orchestrator que nunca terminam. Eles são úteis quando desejar usar [funções duráveis](durable-functions-overview.md) para agregadores e qualquer cenário que requeira um loop infinito.

## <a name="orchestration-history"></a>Histórico de orquestração

Conforme explicado [pontos de verificação e repetição](durable-functions-checkpointing-and-replay.md), a estrutura de tarefa durável mantém um registro de histórico de orquestração de cada função. Esse histórico que aumenta continuamente, desde que a função de orquestrador continua a agendar trabalho novo. Se a função de orquestrador entra num loop infinito e continuamente as agendas de trabalho, esse histórico poderia aumentar extremamente grande e causar problemas de desempenho significativo. O *orquestração externas* conceito foi projetado para atenuar esses tipos de problemas para aplicativos que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>A repor e reiniciar

Em vez de usar loops infinitos, as funções do orchestrator repor o seu estado chamando o [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) método. Esse método obtém um único parâmetro JSON serializável, que se torna a nova entrada para a próxima geração de função do orchestrator.

Quando `ContinueAsNew` é chamado, a instância coloca em fila uma mensagem para si mesmo antes de ele sai. A mensagem reinicia a instância com o novo valor de entrada. O mesmo ID de instância é mantido, mas o histórico da função de orquestrador efetivamente é truncado.

> [!NOTE]
> A estrutura de tarefa durável mantém o mesmo ID de instância, mas internamente cria uma nova *ID de execução* para a função de orquestrador que é reposta pela `ContinueAsNew`. Este ID de execução em geral, não é exposto externamente, mas poderá ser útil saber sobre a execução de orquestração de depuração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódica

Um caso de uso para orquestrações externas é o código que precisa para fazer o trabalho periódico indefinidamente.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

A diferença entre esse exemplo e uma função acionada por temporizador é que os tempos de Acionador de limpeza aqui não se baseiam numa agenda. Por exemplo, uma agenda CRON que executa uma função a cada hora irá executá-lo à 1:00, 2:00, 3:00 etc. e potencialmente pode se deparar com problemas de sobreposição. Neste exemplo, no entanto, se a limpeza demorar 30 minutos, em seguida, ele será agendado com prioridade às 1:00, 2 minutos e 30, 4:00, etc. e não há nenhuma chance de sobreposição.

## <a name="exit-from-an-eternal-orchestration"></a>Sair de uma orquestração externas

Se uma função de orquestrador tem de concluir, eventualmente, então, tudo o que precisa fazer é *não* chamar `ContinueAsNew` e deixar que a função de saída.

Se uma função de orquestrador for num loop infinito e tem de ser parado, utilize o [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) método interrompê-lo. Para obter mais informações, consulte [gerenciamento de instância](durable-functions-instance-management.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aprenda a implementar orquestrações singleton](durable-functions-singletons.md)
