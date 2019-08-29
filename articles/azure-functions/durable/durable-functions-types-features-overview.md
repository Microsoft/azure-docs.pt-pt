---
title: Tipos de função e recursos na extensão Durable Functions de Azure Functions
description: Saiba mais sobre os tipos de funções e funções que dão suporte à comunicação de função a função em uma orquestração de Durable Functions no Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097748"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Tipos de Durable Functions e recursos (Azure Functions)

Durable Functions é uma extensão de [Azure Functions](../functions-overview.md). Você pode usar Durable Functions para orquestração com estado da execução da função. Uma função durável é uma solução composta por diferentes funções do Azure. As funções podem reproduzir funções diferentes em uma orquestração de função durável. 

Este artigo fornece uma visão geral dos tipos de funções que você pode usar em uma orquestração de Durable Functions. O artigo inclui alguns padrões comuns que você pode usar para conectar funções. Saiba como Durable Functions pode ajudá-lo a resolver seus desafios de desenvolvimento de aplicativos.

![Uma imagem que mostra os tipos de funções duráveis][1]  

## <a name="types-of-durable-functions"></a>Tipos de funções duráveis

Você pode usar quatro tipos de função duráveis em Azure Functions: Activity, Orchestrator, Entity e Client.

### <a name="activity-functions"></a>Funções de atividade

As funções de atividade são a unidade básica de trabalho em uma orquestração de função durável. Funções de atividade são as funções e tarefas que são orquestradas no processo. Por exemplo, você pode criar uma função durável para processar um pedido. As tarefas envolvem a verificação do inventário, o carregamento do cliente e a criação de uma remessa. Cada tarefa seria uma função de atividade. 

As funções de atividade não são restritas no tipo de trabalho que você pode fazer neles. Você pode escrever uma função de atividade em qualquer [linguagem que Durable Functions suporte](durable-functions-overview.md#language-support). A estrutura de tarefa durável garante que cada função de atividade chamada será executada pelo menos uma vez durante uma orquestração.

Use um [gatilho de atividade](durable-functions-bindings.md#activity-triggers) para disparar uma função de atividade. As funções do .NET recebem um [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como um parâmetro. Você também pode associar o gatilho a qualquer outro objeto para passar entradas para a função. No JavaScript, você pode acessar uma entrada por meio `<activity trigger binding name>` da propriedade [ `context.bindings` no objeto](../functions-reference-node.md#bindings).

Sua função de atividade também pode retornar valores para o orquestrador. Se você enviar ou retornar um grande número de valores de uma função de atividade, poderá usar [tuplas ou matrizes](durable-functions-bindings.md#passing-multiple-parameters). Você pode disparar uma função de atividade somente de uma instância de orquestração. Embora uma função de atividade e outra função (como uma função disparada por HTTP) possa compartilhar algum código, cada função pode ter apenas um gatilho.

Para obter mais informações e exemplos, consulte [funções de atividade](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funções de orquestrador

As funções de orquestrador descrevem como as ações são executadas e a ordem na qual as ações são executadas. As funções de orquestrador descrevem a orquestração noC# código (ou JavaScript), conforme mostrado em [padrões de durable Functions e conceitos técnicos](durable-functions-concepts.md). Uma orquestração pode ter muitos tipos diferentes de ações, incluindo [funções de atividade](#activity-functions), suborquestrações, [aguardando eventos externos](#external-events)e [temporizadores](#durable-timers). [](#sub-orchestrations) As funções de orquestrador também podem interagir com [funções de entidade](#entity-functions).

Uma função de orquestrador deve ser disparada por um [gatilho](durable-functions-bindings.md#orchestration-triggers)de orquestração.

Um orquestrador é iniciado por um [cliente](#client-functions)do Orchestrator. Você pode disparar o Orchestrator de qualquer fonte (HTTP, fila, fluxo de eventos). Cada instância de uma orquestração tem um identificador de instância. O identificador de instância pode ser gerado automaticamente (recomendado) ou gerado pelo usuário. Você pode usar o identificador de instância para [gerenciar instâncias](durable-functions-instance-management.md) da orquestração.

Para obter mais informações e exemplos, consulte [gatilhos](durable-functions-bindings.md#orchestration-triggers)de orquestração.

###  <a name="entity-functions"></a>Funções de entidade (visualização)

As funções de entidade definem operações para ler e atualizar pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

> [!NOTE]
> Funções de entidade e funcionalidade relacionada só estão disponíveis no Durable Functions 2,0 e superior.

Para obter mais informações sobre funções de entidade, consulte a documentação do recurso de visualização de [funções de entidade](durable-functions-preview.md#entity-functions) .

### <a name="client-functions"></a>Funções de cliente

As funções de cliente são funções disparadas que criam e gerenciam instâncias de orquestrações e entidades. Eles são efetivamente o ponto de entrada para interagir com Durable Functions. Você pode disparar uma função de cliente de qualquer fonte (HTTP, fila, fluxo de eventos, etc.). Uma função de cliente usa a [Associação de cliente](durable-functions-bindings.md#orchestration-client) de orquestração para criar e gerenciar orquestrações e entidades duráveis.

O exemplo mais básico de uma função de cliente é uma função disparada por HTTP que inicia uma função de orquestrador e, em seguida, retorna uma resposta de status de verificação. Para obter um exemplo, consulte [descoberta de URL de API http](durable-functions-http-api.md#http-api-url-discovery).

Para obter mais informações e exemplos, consulte [Orchestration Client](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Recursos e padrões

As seções a seguir descrevem os recursos e padrões de tipos de Durable Functions.

### <a name="sub-orchestrations"></a>Sub-orquestrações

As funções de orquestrador podem chamar funções de atividade, mas também podem chamar outras funções de orquestrador. Por exemplo, você pode criar uma orquestração maior a partir de uma biblioteca de funções de orquestrador. Ou, você pode executar várias instâncias de uma função de orquestrador em paralelo.

Para obter mais informações e exemplos, consulte [](durable-functions-sub-orchestrations.md)suborquestrações.

### <a name="durable-timers"></a>Temporizadores duráveis

O [Durable Functions](durable-functions-overview.md) fornece *temporizadores duráveis* que você pode usar em funções de orquestrador para implementar atrasos ou para configurar tempos limite em ações assíncronas. Use temporizadores duráveis em funções de orquestrador `Thread.Sleep` em `Task.Delay` vezC#de and `setTimeout()` ( `setInterval()` ) ou e (JavaScript).

Para obter mais informações e exemplos, consulte [temporizadores duráveis](durable-functions-timers.md).

### <a name="external-events"></a>Eventos externos

As funções de orquestrador podem aguardar eventos externos para atualizar uma instância de orquestração. Esse Durable Functions recurso geralmente é útil para lidar com uma interação humana ou com outros retornos de chamada externos.

Para obter mais informações e exemplos, consulte [eventos externos](durable-functions-external-events.md).

### <a name="error-handling"></a>Processamento de erros

Use o código para implementar orquestrações de Durable Functions. Você pode usar os recursos de tratamento de erros da linguagem de programação. Padrões como `try` / trabalharemsuaorquestração`catch` . 

Durable Functions também vem com políticas de repetição internas. Uma ação pode atrasar e repetir atividades automaticamente quando ocorre uma exceção. Você pode usar novas tentativas para lidar com exceções transitórias sem abandonar a orquestração.

Para obter mais informações e exemplos, consulte [tratamento de erros](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Comunicação de aplicativo entre funções

Embora uma orquestração durável seja executada no contexto de um único aplicativo de funções, você pode usar padrões para coordenar orquestrações em vários aplicativos de funções. A comunicação entre aplicativos pode ocorrer via HTTP, mas usar a estrutura durável para cada atividade significa que você ainda pode manter um processo durável em dois aplicativos.

Os exemplos a seguir demonstram a orquestração de aplicativo C# entre funções no e o JavaScript. Em cada exemplo, uma atividade inicia a orquestração externa. Outra atividade recupera e retorna o status. O orquestrador aguarda o status ser `Complete` antes de continuar.

Aqui estão alguns exemplos de orquestração de aplicativo entre funções:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

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

Para começar, crie sua primeira função durável no ou [C#](durable-functions-create-first-csharp.md) no [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Leia mais sobre Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
