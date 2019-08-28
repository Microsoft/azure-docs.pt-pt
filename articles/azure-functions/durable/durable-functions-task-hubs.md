---
title: Hubs de tarefas no Durable Functions-Azure
description: Saiba o que é um hub de tarefas na extensão de Durable Functions para Azure Functions. Saiba como configurar hubs de tarefas de configuração.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 40144fb50a01a64bbd67d541562b4fe0842fbf10
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097788"
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

Todos esses recursos são criados automaticamente na conta de armazenamento do Azure padrão quando as funções do Orchestrator ou da atividade são executadas ou estão agendadas para execução. O artigo [desempenho e escala](durable-functions-perf-and-scale.md) explica como esses recursos são usados.

## <a name="task-hub-names"></a>Nomes de Hub de tarefas

Os hubs de tarefas são identificados por um nome declarado no arquivo *host. JSON* , conforme mostrado no exemplo a seguir:

### <a name="hostjson-functions-1x"></a>host. JSON (funções 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host. JSON (funções 2. x)

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

Os hubs de tarefas também podem ser configurados usando as configurações do aplicativo, conforme mostrado no seguinte arquivo de exemplo *host. JSON* :

### <a name="hostjson-functions-1x"></a>host. JSON (funções 1. x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host. JSON (funções 2. x)

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

O nome do hub de tarefas será definido como o valor da `MyTaskHub` configuração do aplicativo. O seguinte `local.settings.json` demonstra como definir a `MyTaskHub` configuração como `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Aqui está um C# exemplo pré-compilado de como escrever uma função que usa um [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) para trabalhar com um hub de tarefas que está configurado como uma configuração de aplicativo:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
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

E abaixo está a configuração necessária para o JavaScript. A propriedade do hub de tarefas `function.json` no arquivo é definida por meio da configuração do aplicativo:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Os nomes de Hub de tarefas devem começar com uma letra e consistir apenas em letras e números. Se não for especificado, o nome padrão será **DurableFunctionsHub**.

> [!NOTE]
> O nome é o que diferencia um hub de tarefas de outro quando há vários hubs de tarefas em uma conta de armazenamento compartilhado. Se você tiver vários aplicativos de funções compartilhando uma conta de armazenamento compartilhado, deverá configurar explicitamente nomes diferentes para cada Hub de tarefas nos arquivos *host. JSON* . Caso contrário, os aplicativos de várias funções competirão entre si para mensagens, o que pode resultar em um comportamento indefinido.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com controle de versão](durable-functions-versioning.md)
