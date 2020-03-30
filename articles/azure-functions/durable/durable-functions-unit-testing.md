---
title: Teste da unidade de funções duráveis Azure
description: Aprenda a equipa restar funções duráveis.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231231"
---
# <a name="durable-functions-unit-testing"></a>Ensaio da unidade de funções duráveis

O teste de unidades é uma parte importante das práticas modernas de desenvolvimento de software. Os testes unitários verificam o comportamento da lógica do negócio e protegem da introdução de mudanças de rutura despercebidas no futuro. Funções Duráveis podem facilmente crescer em complexidade, por isso a introdução de testes unitários ajudará a evitar alterações. As seguintes secções explicam como testar os três tipos de funções - cliente de orquestração, orquestrador e funções de atividade.

> [!NOTE]
> Este artigo fornece orientação para testes unitários para aplicações de funções duráveis que visam funções duráveis 1.x. Ainda não foi atualizado para ter em conta as alterações introduzidas nas Funções Duráveis 2.x. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos deste artigo requerem conhecimento dos seguintes conceitos e quadros:

* Teste de unidade

* Funções Duráveis

* [xUnidade](https://xunit.github.io/) - Quadro de ensaio

* [moq](https://github.com/moq/moq4) - Estrutura de zombaria

## <a name="base-classes-for-mocking"></a>Aulas de base para gozar

A zombaria é suportada através de três classes abstratas em Funções Duráveis 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Estas aulas são `DurableOrchestrationClient`classes `DurableOrchestrationContext`base `DurableActivityContext` para, e que definem métodos de Cliente de Orquestração, Orquestrador e Atividade. As simulações definirão o comportamento esperado para métodos de classe base para que o teste da unidade possa verificar a lógica do negócio. Há um fluxo de trabalho em duas etapas para a unidade que testa a lógica de negócio no Cliente de Orquestração e Orquestrador:

1. Utilize as classes base em vez da implementação concreta ao definir assinaturas de funções de cliente de orquestração e orquestrador.
2. Na unidade os testes ridicularizam o comportamento das classes base e verificam a lógica do negócio.

Encontre mais detalhes nos parágrafos seguintes para funções de teste que utilizam a ligação do cliente de orquestração e a ligação do gatilho orquestrador.

## <a name="unit-testing-trigger-functions"></a>Funções do gatilho de teste da unidade

Nesta secção, o teste unitário validará a lógica da seguinte função de gatilho HTTP para iniciar novas orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste da unidade `Retry-After` será verificar o valor do cabeçalho fornecido na carga útil da resposta. Assim, o teste da `DurableOrchestrationClientBase` unidade irá ridicularizar alguns métodos para garantir um comportamento previsível.

Primeiro, é necessária uma simulação `DurableOrchestrationClientBase`da classe base. A simulação pode ser uma `DurableOrchestrationClientBase`nova classe que implementa. No entanto, usar um quadro de zombaria como [o moq](https://github.com/moq/moq4) simplifica o processo:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Em `StartNewAsync` seguida, o método é ridicularizado para devolver uma identificação de instância bem conhecida.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Em `CreateCheckStatusResponse` seguida é ridicularizado para sempre devolver uma resposta http 200 vazia.

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

`ILogger`é também gozado:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Agora `Run` o método é chamado do teste da unidade:

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

 O último passo é comparar a produção com o valor esperado:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Depois de combinar todas as etapas, o teste da unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funções de orquestrador de teste de unidade

As funções de orquestradores são ainda mais interessantes para testes unitários, uma vez que geralmente têm muito mais lógica de negócio.

Nesta secção, os testes unitários `E1_HelloSequence` validarão a saída da função Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

O código de teste da unidade começará com a criação de uma simulação:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Em seguida, as chamadas do método de atividade serão ridicularizadas:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Em seguida, o `HelloSequence.Run` teste da unidade chamará o método:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

E, finalmente, a saída será validada:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Depois de combinar todas as etapas, o teste da unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funções de atividade de teste unitário

As funções de atividade podem ser testadas da mesma forma que as funções não duráveis.

Nesta secção, o teste unitário `E1_SayHello` validará o comportamento da função Atividade:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

E os testes da unidade verificarão o formato da saída. Os ensaios unitários podem utilizar `DurableActivityContextBase` diretamente os tipos de parâmetros ou a classe de simulação:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Saiba mais sobre o moq](https://github.com/Moq/moq4/wiki/Quickstart)
