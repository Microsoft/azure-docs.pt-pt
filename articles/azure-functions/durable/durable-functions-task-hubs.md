---
title: Hubs de tarefas no Durable Functions-Azure
description: Saiba o que é um hub de tarefas na extensão de Durable Functions para Azure Functions. Saiba como configurar os hubs de tarefas.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: ffb3d590aebe80994de1e7e834a2eba5777df9a1
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262491"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tarefas em Durable Functions (Azure Functions)

Um *Hub de tarefas* no [Durable Functions](durable-functions-overview.md) é um contêiner lógico para recursos de armazenamento do Azure que são usados para orquestrações. As funções de orquestrador e atividade só podem interagir umas com as outras quando pertencem ao mesmo Hub de tarefas.

Se vários aplicativos de funções compartilharem uma conta de armazenamento, cada aplicativo de funções *deverá* ser configurado com um nome de Hub de tarefas separado. Uma conta de armazenamento pode conter vários hubs de tarefas. O diagrama a seguir ilustra um hub de tarefas por aplicativo de funções em contas de armazenamento compartilhadas e dedicadas.

![Diagrama mostrando contas de armazenamento compartilhadas e dedicadas.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Recursos de armazenamento do Azure

Um hub de tarefas consiste nos seguintes recursos de armazenamento:

* Uma ou mais filas de controle.
* Uma fila de item de trabalho.
* Uma tabela de histórico.
* Uma tabela de instâncias.
* Um contêiner de armazenamento que contém um ou mais blobs de concessão.
* Um contêiner de armazenamento que contém cargas de mensagens grandes, se aplicável.

Todos esses recursos são criados automaticamente na conta de armazenamento do Azure padrão quando as funções Orchestrator, Entity ou Activity são executadas ou estão agendadas para execução. O artigo [desempenho e escala](durable-functions-perf-and-scale.md) explica como esses recursos são usados.

## <a name="task-hub-names"></a>Nomes de Hub de tarefas

Os hubs de tarefas são identificados por um nome declarado no arquivo *host. JSON* , conforme mostrado no exemplo a seguir:

### <a name="hostjson-functions-20"></a>host. JSON (funções 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host. JSON (funções 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Os hubs de tarefas também podem ser configurados usando as configurações do aplicativo, conforme mostrado no seguinte arquivo de exemplo `host.json`:

### <a name="hostjson-functions-10"></a>host. JSON (funções 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host. JSON (funções 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

O nome do hub de tarefas será definido como o valor da configuração do aplicativo `MyTaskHub`. O `local.settings.json` a seguir demonstra como definir a configuração de `MyTaskHub` como `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

O código a seguir demonstra como escrever uma função que usa a [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) para trabalhar com um hub de tarefas que está configurado como uma configuração de aplicativo:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> O exemplo C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A propriedade Hub de tarefas no arquivo `function.json` é definida por meio da configuração do aplicativo:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Os nomes de Hub de tarefas devem começar com uma letra e consistir apenas em letras e números. Se não for especificado, um nome de Hub de tarefas padrão será usado conforme mostrado na tabela a seguir:

| Versão de extensão durável | Nome do hub de tarefas padrão |
| - | - |
| 2.x | Quando implantado no Azure, o nome do hub de tarefas é derivado do nome do _aplicativo de funções_. Ao executar fora do Azure, o nome do hub de tarefas padrão é `TestHubName`. |
| 1.x | O nome do hub de tarefas padrão para todos os ambientes é `DurableFunctionsHub`. |

Para obter mais informações sobre as diferenças entre as versões de extensão, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

> [!NOTE]
> O nome é o que diferencia um hub de tarefas de outro quando há vários hubs de tarefas em uma conta de armazenamento compartilhado. Se você tiver vários aplicativos de funções compartilhando uma conta de armazenamento compartilhado, deverá configurar explicitamente nomes diferentes para cada Hub de tarefas nos arquivos *host. JSON* . Caso contrário, os aplicativos de várias funções competirão uns com os outros para mensagens, o que pode resultar em um comportamento indefinido, incluindo orquestrações sendo inesperadas "presas" no estado de `Pending` ou `Running`.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão de orquestração](durable-functions-versioning.md)
