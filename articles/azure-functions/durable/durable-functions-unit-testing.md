---
title: Teste de unidade de Durable Functions do Azure
description: Saiba como Durable Functions de teste de unidade.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: glenga
ms.openlocfilehash: 0080365853e7a9c74d3ba0e5efb06ce5a3af2a21
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967106"
---
# <a name="durable-functions-unit-testing"></a>Teste de unidade de Durable Functions

O teste de unidade é uma parte importante das práticas modernas de desenvolvimento de software. Testes de unidade verificam o comportamento da lógica de negócios e protegem desde a introdução de alterações significativas despercebidas no futuro. Durable Functions pode facilmente crescer em complexidade, de modo que a introdução de testes de unidade ajudará a evitar alterações significativas. As seções a seguir explicam como testar a unidade dos três tipos de função: o cliente de orquestração, o Orchestrator e as funções de atividade.

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos neste artigo exigem conhecimento dos seguintes conceitos e estruturas:

* Teste de unidade

* Durable Functions

* [xUnit](https://xunit.github.io/) -estrutura de teste

* estrutura de simulação de [MOQ](https://github.com/moq/moq4)

## <a name="base-classes-for-mocking"></a>Classes base para simulação

Há suporte para a simulação por meio de três classes abstratas no Durable Functions:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Essas classes são classes base para [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)e [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) que definem os métodos de Orchestration Client, Orchestrator e Activity. As simulações definirão o comportamento esperado para métodos de classe base para que o teste de unidade possa verificar a lógica de negócios. Há um fluxo de trabalho de duas etapas para teste de unidade da lógica de negócios no Orchestrator Client e no orquestrador:

1. Use as classes base em vez da implementação concreta ao definir as assinaturas do cliente de orquestração e do Orchestrator.
2. Nos testes de unidade, simule o comportamento das classes base e verifique a lógica de negócios.

Encontre mais detalhes nos parágrafos a seguir para testar funções que usam a associação de cliente de orquestração e a associação de gatilho do Orchestrator.

## <a name="unit-testing-trigger-functions"></a>Funções de gatilho de teste de unidade

Nesta seção, o teste de unidade validará a lógica da seguinte função de gatilho HTTP para iniciar novas orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste de unidade será verificar o valor do `Retry-After` cabeçalho fornecido na carga de resposta. Portanto, o teste de unidade simulará alguns dos métodos [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) para garantir um comportamento previsível.

Primeiro, uma simulação da classe base é necessária, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). A simulação pode ser uma nova classe que implementa [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). No entanto, usar uma estrutura fictícia como o [MOQ](https://github.com/moq/moq4) simplifica o processo:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Em `StartNewAsync` seguida, o método é simulado para retornar uma ID de instância conhecida.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Em `CreateCheckStatusResponse` seguida, é simulado para sempre retornar uma resposta http 200 vazia.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`ILogger`também é simulado:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();

```  

Agora o `Run` método é chamado a partir do teste de unidade:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        loggerMock.Object);
 ```

 A última etapa é comparar a saída com o valor esperado:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Depois de combinar todas as etapas, o teste de unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funções de orquestrador de teste de unidade

As funções de orquestrador são ainda mais interessantes para testes de unidade, pois normalmente têm muito mais lógica comercial.

Nesta seção, os testes de unidade validarão a saída da `E1_HelloSequence` função de orquestrador:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

O código de teste de unidade será iniciado com a criação de uma simulação:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Em seguida, as chamadas de método de atividade serão simuladas:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Em seguida, o teste de `HelloSequence.Run` unidade chamará o método:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

E, por fim, a saída será validada:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Depois de combinar todas as etapas, o teste de unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funções de atividade de teste de unidade

As funções de atividade podem ser testadas por unidade da mesma forma que as funções não duráveis.

Nesta seção, o teste de unidade validará o comportamento da `E1_SayHello` função de atividade:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

E os testes de unidade verificarão o formato da saída. Os testes de unidade podem usar os tipos de parâmetro diretamente ou a classe fictícia [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Saiba mais sobre o MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
