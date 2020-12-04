---
title: Teste da unidade de funções duradouras de Azure
description: Saiba como unir o teste de Funções Duradouras.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 7786a0a2e2d31086e1938b70e63fe2374e16fe7f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601361"
---
# <a name="durable-functions-unit-testing"></a>Testes de unidade de funções duradouras

O teste de unidade é uma parte importante das práticas modernas de desenvolvimento de software. Os testes de unidade verificam o comportamento da lógica do negócio e protegem de introduzir mudanças de rutura despercebidas no futuro. As funções duradouras podem crescer facilmente em complexidade, pelo que a introdução de testes unitários ajudará a evitar alterações. As secções seguintes explicam como testar os três tipos de funções - Cliente de orquestração, orquestrador e funções de atividade.

> [!NOTE]
> Este artigo fornece orientações para testes de unidade para aplicações de Funções Duradouras que visam funções duráveis 1.x. Ainda não foi atualizado para ter em conta as alterações introduzidas nas Funções Duráveis 2.x. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos deste artigo requerem conhecimento dos seguintes conceitos e quadros:

* Teste de unidades

* Funções Duráveis

* [xUnit](https://github.com/xunit/xunit) - Estrutura de testes

* [moq](https://github.com/moq/moq4) - Quadro de zombaria

## <a name="base-classes-for-mocking"></a>Aulas base para zombaria

O zombaria é suportado através de três classes abstratas em Funções Duráveis 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Estas aulas são aulas base `DurableOrchestrationClient` `DurableOrchestrationContext` para, e `DurableActivityContext` que definem os métodos de Orquestração Cliente, Orquestrador e Atividade. As simulações definirão o comportamento esperado para os métodos de classe base para que o teste da unidade possa verificar a lógica de negócio. Existe um fluxo de trabalho em duas etapas para testar a lógica de negócio no Cliente de Orquestração e Orquestrador:

1. Utilize as classes base em vez da implementação concreta ao definir assinaturas de funções de cliente de orquestração e orquestrador.
2. Nos testes de unidade ridicularizam o comportamento das classes base e verificam a lógica de negócio.

Encontre mais detalhes nos parágrafos seguintes para testar funções que usam a ligação do cliente de orquestração e a ligação do gatilho do orquestrador.

## <a name="unit-testing-trigger-functions"></a>Funções de gatilho de teste de unidade

Nesta secção, o teste de unidade validará a lógica da seguinte função de gatilho HTTP para iniciar novas orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste da unidade será verificar o valor do `Retry-After` cabeçalho fornecido na carga útil de resposta. Assim, o teste de unidade irá ridicularizar alguns dos `DurableOrchestrationClientBase` métodos para garantir um comportamento previsível.

Primeiro, é necessária uma simulação da classe `DurableOrchestrationClientBase` base. A zombaria pode ser uma nova classe que `DurableOrchestrationClientBase` implementa. No entanto, a utilização de uma estrutura de zombaria como [o moq](https://github.com/moq/moq4) simplifica o processo:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Em seguida, `StartNewAsync` o método é ridicularizado para devolver uma identificação de instância bem conhecida.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Em `CreateCheckStatusResponse` seguida, é ridicularizado para sempre devolver uma resposta HTTP 200 vazia.

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

`ILogger` é também gozado:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
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
        loggerMock.Object);
 ```

 O último passo é comparar a saída com o valor esperado:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Depois de combinar todas as etapas, o teste de unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Unidade de teste funções orquestradoras

As funções de orquestrador são ainda mais interessantes para os testes de unidade, uma vez que geralmente têm muito mais lógica de negócio.

Nesta secção, os testes unitários validarão a saída da `E1_HelloSequence` função Orquestrador:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

O código de teste da unidade começará por criar uma simulação:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Em seguida, as chamadas do método de atividade serão ridicularizadas:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Em seguida, o teste de unidade chamará `HelloSequence.Run` o método:

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

Depois de combinar todas as etapas, o teste de unidade terá o seguinte código:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funções de atividade de teste de unidade

As funções de atividade podem ser testadas de forma a que as funções não duráveis sejam testadas.

Nesta secção, o teste de unidade validará o comportamento da `E1_SayHello` função Atividade:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

E os testes de unidade verificarão o formato da saída. Os testes de unidade podem utilizar os tipos de parâmetros diretamente ou simular `DurableActivityContextBase` a classe:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba mais sobre xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Saiba mais sobre moq](https://github.com/Moq/moq4/wiki/Quickstart)
