---
title: Tipos de função e funcionalidades na extensão de funções duráveis das funções do Azure
description: Saiba mais sobre os tipos de funções que suportam a função de função para comunicação numa orquestração de funções duráveis nas funções do Azure.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455739"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Tipos de funções duráveis e recursos (funções do Azure)

Funções duráveis é uma extensão da [as funções do Azure](../functions-overview.md). Pode utilizar funções duráveis para orquestração com estado de execução de função. Uma função durável é uma solução que é constituída por diferentes funções do Azure. As funções podem desempenhar funções diferentes numa orquestração de função durável. 

Este artigo fornece uma descrição geral dos tipos de funções que pode utilizar uma orquestração de funções duráveis. O artigo inclui alguns padrões comuns que pode utilizar para ligar as funções. Saiba como funções duráveis pode ajudar a resolver seus desafios de desenvolvimento de aplicações.

![Uma imagem que mostra os tipos de funções duráveis][1]  

## <a name="types-of-durable-functions"></a>Tipos de funções duráveis

Pode utilizar três tipos de função durável nas funções do Azure: atividade, o orchestrator e o cliente.

### <a name="activity-functions"></a>Funções de atividade

Funções de atividade são a unidade básica de trabalho numa orquestração de função durável. Funções de atividade são as funções e tarefas que são organizadas no processo. Por exemplo, poderá criar uma função durável para processar um pedido. As tarefas envolvem a verificar o inventário, cobrar o cliente e criar uma remessa. Cada tarefa seria uma função de atividade. 

Funções de atividade não forem restrito no tipo de trabalho, que pode fazer nos mesmos. Pode escrever uma função de atividade em qualquer [linguagem que oferecem suporte a funções duráveis](durable-functions-overview.md#language-support). A estrutura de tarefa durável garante que cada função de chamada de atividade será executada pelo menos uma vez durante uma orquestração.

Utilize um [acionador de atividade](durable-functions-bindings.md#activity-triggers) para acionar uma função de atividade. Funções de .NET recebem uma [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como um parâmetro. Também é possível vincular o acionador para qualquer outro objeto para passar nas entradas para a função. No JavaScript, pode acessar uma inserção de dados através da `<activity trigger binding name>` propriedade no [ `context.bindings` objeto](../functions-reference-node.md#bindings).

A função de atividade também pode retornar valores para o orchestrator. Se envia ou retorna um grande número de valores de uma função de atividade, pode utilizar [tuplas ou matrizes](durable-functions-bindings.md#passing-multiple-parameters). Pode acionar uma função de atividade apenas a partir de uma instância de orquestração. Embora uma função de atividade e a outra função (como uma função acionada por HTTP) podem compartilhar a parte do código, cada função pode ter apenas um acionador.

Para obter mais informações e exemplos, veja [funções de atividade](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funções do Orchestrator

As funções do Orchestrator descrevem como as ações são executadas e a ordem em que as ações são executadas. As funções do Orchestrator descrevem a orquestração no código (C# ou o JavaScript) conforme mostrado na [padrões de funções duráveis e conceitos técnicos](durable-functions-concepts.md). Uma orquestração pode ter vários tipos de ações, incluindo [funções de atividade](#activity-functions), [orquestrações secundárias](#sub-orchestrations), [aguardar por eventos externos](#external-events)e [temporizadores](#durable-timers). 

Uma função de orquestrador tem de ser acionada por um [acionador de orquestração](durable-functions-bindings.md#orchestration-triggers).

Um orquestrador é iniciado por um [cliente do orchestrator](#client-functions). Pode disparar o orchestrator a partir de qualquer origem (HTTP, fila, fluxo de eventos). Cada instância de uma orquestração tem um identificador de instância. O identificador de instância pode ser gerado automaticamente (recomendado) ou gerados pelo utilizador. Pode usar o identificador de instância para [gerir instâncias](durable-functions-instance-management.md) da orquestração.

Para obter mais informações e exemplos, veja [acionadores de orquestração](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funções do cliente

As funções do cliente são as funções acionadas que criar novas instâncias de uma orquestração. As funções do cliente são o ponto de entrada para a criação de uma instância de uma orquestração de funções duráveis. Pode acionar uma função de cliente a partir de qualquer origem (HTTP, fila, fluxo de eventos). Pode escrever uma função de cliente em qualquer idioma que a aplicação suporta. 

As funções do cliente também tem um [cliente de orquestração](durable-functions-bindings.md#orchestration-client) enlace. A função de um cliente pode utilizar a ligação para criar e gerir orquestrações duráveis do cliente de orquestração. 

O exemplo mais básico de uma função de cliente é uma função acionada por HTTP, que inicia uma função de orquestrador e, em seguida, devolve uma resposta da verificação de estado. Por exemplo, veja [deteção de URL da API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Para obter mais informações e exemplos, veja [cliente de orquestração](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Recursos e padrões

As secções seguintes descrevem os recursos e padrões de tipos de funções duráveis.

### <a name="sub-orchestrations"></a>Sub-orquestrações

As funções do Orchestrator podem chamar funções de atividade, mas também podem chamar outras funções do orchestrator. Por exemplo, pode criar uma orquestração maior fora de uma biblioteca de funções do orchestrator. Em alternativa, pode executar várias instâncias de uma função de orquestrador em paralelo.

Para obter mais informações e exemplos, veja [orquestrações secundárias](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Temporizadores duráveis

[Funções duráveis](durable-functions-overview.md) fornece *temporizadores duráveis* que pode usar nas funções do orchestrator para implementar atrasos ou configurar tempos limite de ações de async. Usar temporizadores duráveis nas funções do orchestrator, em vez de `Thread.Sleep` e `Task.Delay` (C#) ou `setTimeout()` e `setInterval()` (JavaScript).

Para obter mais informações e exemplos, veja [temporizadores duráveis](durable-functions-timers.md).

### <a name="external-events"></a>Eventos externos

As funções do Orchestrator podem aguardar por eventos externos atualizar uma instância de orquestração. Esta funcionalidade de funções duráveis, muitas vezes, é útil para processar uma interação humana ou outros retornos de chamada externos.

Para obter mais informações e exemplos, veja [eventos externos](durable-functions-external-events.md).

### <a name="error-handling"></a>Processamento de erros

Utilize o código para implementar orquestrações de funções duráveis. Pode usar os recursos de tratamento de erros de linguagem de programação. Como padrões `try` / `catch` funcionam na sua orquestração. 

Funções duráveis também vêm com as políticas de repetição incorporado. Uma ação pode atrasar e repita atividades automaticamente quando ocorre uma exceção. Pode usar as repetições para lidar com exceções transitórias sem ter de abandonar a orquestração.

Para obter mais informações e exemplos, veja [tratamento de erros](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Comunicação de aplicação de várias funções

Embora uma orquestração durável é executado no contexto de uma aplicação de função única, pode utilizar padrões para coordenar as orquestrações em muitas aplicações de funções. Comunicação entre aplicações poderão ocorrer através de HTTP, mas usando a estrutura de durável para cada atividade significa que ainda pode manter um processo durável entre duas aplicações.

Os exemplos seguintes demonstram a orquestração de aplicação de várias funções no C# e JavaScript. Em cada exemplo, uma atividade começa a orquestração externa. Outra atividade obtém e devolve o estado. O orchestrator aguarda que o estado ser `Complete` antes de prosseguir.

Aqui estão alguns exemplos de orquestração de aplicação de funções de entre:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Passos Seguintes

Para começar, crie sua primeira função durável na [ C# ](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre funções duráveis](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
