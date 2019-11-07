---
title: Teste de unidade de Durable Functions do Azure
description: Saiba como Durable Functions de teste de unidade.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: glenga
ms.openlocfilehash: 95c6afcb2f7e864da4b9b43235326a17bed785fa
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614540"
---
# <a name="durable-functions-unit-testing"></a>Teste de unidade de Durable Functions

O teste de unidade é uma parte importante das práticas modernas de desenvolvimento de software. Testes de unidade verificam o comportamento da lógica de negócios e protegem desde a introdução de alterações significativas despercebidas no futuro. Durable Functions pode facilmente crescer em complexidade, de modo que a introdução de testes de unidade ajudará a evitar alterações significativas. As seções a seguir explicam como testar a unidade dos três tipos de função: o cliente de orquestração, o Orchestrator e as funções de atividade.

> [!NOTE]
> Este artigo fornece diretrizes para testes de unidade para Durable Functions aplicativos direcionados Durable Functions 1. x. Ele ainda não foi atualizado para considerar as alterações introduzidas no Durable Functions 2. x. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos neste artigo exigem conhecimento dos seguintes conceitos e estruturas:

* Teste de unidade

* Funções Duráveis

* [xUnit](https://xunit.github.io/) -estrutura de teste

* estrutura de simulação de [MOQ](https://github.com/moq/moq4)

## <a name="base-classes-for-mocking"></a>Classes base para simulação

Há suporte para a simulação por meio de três classes abstratas no Durable Functions 1. x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Essas classes são classes base para `DurableOrchestrationClient`, `DurableOrchestrationContext`e `DurableActivityContext` que definem os métodos de cliente de orquestração, orquestrador e atividade. As simulações definirão o comportamento esperado para métodos de classe base para que o teste de unidade possa verificar a lógica de negócios. Há um fluxo de trabalho de duas etapas para teste de unidade da lógica de negócios no Orchestrator Client e no orquestrador:

1. Use as classes base em vez da implementação concreta ao definir as assinaturas de cliente de orquestração e de função de orquestrador.
2. Nos testes de unidade, simule o comportamento das classes base e verifique a lógica de negócios.

Encontre mais detalhes nos parágrafos a seguir para testar funções que usam a associação de cliente de orquestração e a associação de gatilho do Orchestrator.

## <a name="unit-testing-trigger-functions"></a>Funções de gatilho de teste de unidade

Nesta seção, o teste de unidade validará a lógica da seguinte função de gatilho HTTP para iniciar novas orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste de unidade será verificar o valor do cabeçalho de `Retry-After` fornecido na carga de resposta. Portanto, o teste de unidade simulará alguns dos métodos `DurableOrchestrationClientBase` para garantir um comportamento previsível.

Primeiro, uma simulação da classe base é necessária, `DurableOrchestrationClientBase`. A simulação pode ser uma nova classe que implementa `DurableOrchestrationClientBase`. No entanto, usar uma estrutura fictícia como o [MOQ](https://github.com/moq/moq4) simplifica o processo:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Em seguida, `StartNewAsync` método é simulado para retornar uma ID de instância conhecida.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

A próxima `CreateCheckStatusResponse` é simulada para sempre retornar uma resposta HTTP 200 vazia.

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

`ILogger` também é simulado:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Agora, o método `Run` é chamado a partir do teste de unidade:

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

Nesta seção, os testes de unidade validarão a saída da função de orquestrador `E1_HelloSequence`:

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

Em seguida, o teste de unidade chamará `HelloSequence.Run` método:

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

Nesta seção, o teste de unidade validará o comportamento da função de atividade de `E1_SayHello`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

E os testes de unidade verificarão o formato da saída. Os testes de unidade podem usar os tipos de parâmetro diretamente ou simular `DurableActivityContextBase` classe:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Saiba mais sobre o MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
