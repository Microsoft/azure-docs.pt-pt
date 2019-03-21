---
title: Teste de unidade de funções duráveis do Azure
description: Saiba como a unidade de teste funções duráveis.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094080"
---
# <a name="durable-functions-unit-testing"></a>Teste de unidade de funções durável

Teste de unidade é uma parte importante de práticas de desenvolvimento de software Moderno. Testes de unidade verificar o comportamento de lógica de negócios e proteger a introdução de alterações de última hora despercebidas no futuro. Funções duráveis facilmente podem crescer em complexidade, de modo a apresentar de testes de unidade irão ajudar a evitar alterações significativas. As secções seguintes explicam como a unidade de testar os tipos de três função - cliente de orquestração, Orchestrator e atividade de funções.

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos neste artigo requerem conhecimento dos conceitos e estruturas seguintes:

* Teste de unidade

* Funções Duráveis

* [xUnit](https://xunit.github.io/) -estrutura de testes

* [moq](https://github.com/moq/moq4) -estrutura de simulação

## <a name="base-classes-for-mocking"></a>Classes base para a simulação

Simulação é suportado por meio de três classes abstratas nas funções duráveis:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Essas classes são classes base para [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html), e [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) que definem o cliente de orquestração , Orchestrator e métodos de atividade. As simulações irão definir o comportamento esperado para métodos de classe base para que o teste de unidade pode verificar a lógica de negócios. Há um fluxo de trabalho de dois passos para teste de unidade lógica de negócios no cliente de orquestração e o Orchestrator:

1. Use as classes bases, em vez da implementação concreta, quando definir as assinaturas de cliente de orquestração e do Orchestrator.
2. Os testes de unidade simular o comportamento das base classes e verifique se a lógica de negócios.

Encontrar mais detalhes nos próximos parágrafos para testar as funções que utilizem a ligação do cliente de orquestração e o orquestrador de acionam a ligação.

## <a name="unit-testing-trigger-functions"></a>Funções de Acionador testes de unidade

Nesta secção, o teste de unidade validará a lógica da função de Acionador HTTP seguinte para iniciar a nova orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste de unidade será para verificar o valor da `Retry-After` fornecido no payload de resposta de cabeçalho. Para que o teste de unidade irá simular algumas das [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) métodos para garantir um comportamento previsível.

Em primeiro lugar, é necessária, uma simulação da classe base [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). A simulação pode ser uma nova classe que implementa [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). No entanto, usando uma estrutura de simulação, como [moq](https://github.com/moq/moq4) simplifica o processo:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Em seguida, `StartNewAsync` método é simulado para retornar um ID de instância bem conhecido.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Próxima `CreateCheckStatusResponse` é simulada para sempre devolver uma resposta HTTP 200 vazia.

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

`TraceWriter` Também é simulada:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Agora o `Run` método é chamado do teste de unidade:

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
        traceWriterMock.Object);
 ```

 O último passo é comparar o resultado com o valor esperado:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Depois de combinar todos os passos, o teste de unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funções de orchestrator de teste de unidade

As funções do Orchestrator são ainda mais interessantes para uma vez que normalmente têm muito mais lógica de negócio de teste de unidade.

Nesta secção, a unidade testes irão validar o resultado do `E1_HelloSequence` função de Orquestrador:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

O código de teste de unidade será iniciado com a criação de uma simulação:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Em seguida, irão ser simuladas as chamadas de método de atividade:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Em seguida chamará o teste de unidade `HelloSequence.Run` método:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

E, finalmente, a saída vai ser validada:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Depois de combinar todos os passos, o teste de unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funções de atividade testes de unidade

Funções de atividade podem ter suas unidades testadas da mesma forma como as funções de não duradoura.

Nesta secção, o teste de unidade irá validar o comportamento do `E1_SayHello` função de atividade:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

E os testes de unidade irão verificar o formato da saída. Os testes de unidade podem utilizar os tipos de parâmetro diretamente ou mock [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) classe:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Saiba mais sobre moq](https://github.com/Moq/moq4/wiki/Quickstart)
