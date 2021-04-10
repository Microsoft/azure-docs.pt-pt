---
title: Teste da unidade de funções duradouras de Azure
description: Saiba como unir o teste de Funções Duradouras.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 89b6419e95b3971b0d272490e19354f300204e1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491049"
---
# <a name="durable-functions-unit-testing"></a>Testes de unidade de funções duradouras

O teste de unidade é uma parte importante das práticas modernas de desenvolvimento de software. Os testes de unidade verificam o comportamento da lógica do negócio e protegem de introduzir mudanças de rutura despercebidas no futuro. As funções duradouras podem crescer facilmente em complexidade, pelo que a introdução de testes unitários ajudará a evitar alterações. As secções seguintes explicam como testar os três tipos de funções - Cliente de orquestração, orquestrador e funções de atividade.

> [!NOTE]
> Este artigo fornece orientações para testes de unidade para aplicações de Funções Duradouras que visam funções duráveis 2.x. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos deste artigo requerem conhecimento dos seguintes conceitos e quadros:

* Teste de unidades

* Funções Duráveis

* [xUnit](https://github.com/xunit/xunit) - Estrutura de testes

* [moq](https://github.com/moq/moq4) - Quadro de zombaria

## <a name="base-classes-for-mocking"></a>Aulas base para zombaria

O escárnio é suportado através da seguinte interface:

* [IDurableOrchestrationClient,](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationClient) [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.IDurableEntityClient) e [IDurableClient](/dotnet/api/microsoft.azure.webjobs.IDurableClient)

* [IDurableOrchestrationContexto](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationContext)

* [IDurableActivityContexto](/dotnet/api/microsoft.azure.webjobs.IDurableActivityContext)
  
* [IDurableEntityContexto](/dotnet/api/microsoft.azure.webjobs.IDurableEntityContext)

Estas interfaces podem ser utilizadas com o gatilho e as ligações suportadas por Funções Duradouras. Ao executar as suas Funções Azure, o tempo de execução das funções executará o seu código de função com uma implementação concreta destas interfaces. Para testes de unidade, pode passar numa versão ridicularizada destas interfaces para testar a sua lógica de negócio.

## <a name="unit-testing-trigger-functions"></a>Funções de gatilho de teste de unidade

Nesta secção, o teste de unidade validará a lógica da seguinte função de gatilho HTTP para iniciar novas orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste da unidade será verificar o valor do `Retry-After` cabeçalho fornecido na carga útil de resposta. Assim, o teste de unidade irá ridicularizar alguns dos `IDurableClient` métodos para garantir um comportamento previsível.

Em primeiro lugar, usamos uma estrutura de zombaria[(moq](https://github.com/moq/moq4) neste caso) para `IDurableClient` gozar:

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Enquanto pode simular interfaces implementando diretamente a interface como uma classe, as estruturas de zombaria simplificam o processo de várias maneiras. Por exemplo, se um novo método for adicionado à interface através de versões menores, o moq não exigirá alterações de código, ao contrário de implementações concretas.

Em seguida, `StartNewAsync` o método é ridicularizado para devolver uma identificação de instância bem conhecida.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

Em `CreateCheckStatusResponse` seguida, é ridicularizado para sempre devolver uma resposta HTTP 200 vazia.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
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
    durableClientMock.Object,
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
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
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

E os testes de unidade verificarão o formato da saída. Os testes de unidade podem utilizar os tipos de parâmetros diretamente ou simular `IDurableActivityContext` a classe:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Saiba mais sobre moq](https://github.com/Moq/moq4/wiki/Quickstart)
