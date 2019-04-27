---
title: Hubs de tarefas nas funções duráveis - Azure
description: Saiba o que um hub de tarefa é na extensão de funções duráveis para as funções do Azure. Saiba como configurar configurar hubs de tarefas.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2017
ms.date: 02/21/2019
ms.author: v-junlch
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730277"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tarefas nas funções durável (funções do Azure)

R *hub da tarefa* na [funções duráveis](durable-functions-overview.md) é um contentor lógico para os recursos de armazenamento do Azure que são utilizadas para orquestrações. As funções do Orchestrator e atividade só podem interagir entre si quando eles pertencem ao mesmo hub de tarefa.

Se várias aplicações de funções partilham uma conta de armazenamento, cada aplicação de funções *tem* ser configurado com um nome de hub de tarefa separada. Uma conta de armazenamento pode conter diversos hubs de tarefas. O diagrama seguinte ilustra um hub de tarefa por aplicação de funções nas contas de armazenamento dedicados e partilhados.

![Diagrama que mostra partilhados e dedicados a contas de armazenamento.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Recursos de armazenamento do Azure

Um concentrador de tarefa inclui os seguintes recursos de armazenamento:

- Uma ou mais filas de controle.
- Uma fila de item de trabalho.
- Tabela de histórico de um.
- Uma tabela de instâncias.
- Um contentor de armazenamento que contém um ou mais blobs de concessão.

Todos esses recursos são criados automaticamente na conta de armazenamento do Azure do padrão quando o orchestrator ou funções de atividade executarem ou são agendadas para serem executadas. O [desempenho e dimensionamento](durable-functions-perf-and-scale.md) artigo explica como esses recursos são usados.

## <a name="task-hub-names"></a>Nomes de hubs de tarefas

Hubs de tarefas são identificados por um nome que é declarado no *Host. JSON* ficheiro, conforme mostrado no exemplo a seguir:

### <a name="hostjson-functions-1x"></a>Host. JSON (funções 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host. JSON (funções 2.x)

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

Hubs de tarefas também podem ser configurados com as definições de aplicação, conforme mostrado a seguir *Host. JSON* exemplo de ficheiro:

### <a name="hostjson-functions-1x"></a>Host. JSON (funções 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host. JSON (funções 2.x)

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

O nome do hub de tarefas será definido para o valor da `MyTaskHub` definição de aplicação. O seguinte procedimento `local.settings.json` demonstra como definir o `MyTaskHub` definir como `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Aqui está um pré-compilados C# exemplo de como escrever uma função que utiliza uma [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) trabalhar com um concentrador de tarefa que está configurado como uma definição de aplicação:

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

E abaixo é a configuração necessária para JavaScript. A propriedade do hub de tarefas no `function.json` ficheiro é definido por meio da definição de aplicação:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Os nomes de hubs de tarefas devem começar com uma letra e consistir apenas letras e números. Se não for especificado, o nome predefinido é **DurableFunctionsHub**.

> [!NOTE]
> O nome é o que diferencia um hub de tarefa de outro quando existem diversos hubs de tarefas numa conta de armazenamento partilhado. Se tiver várias aplicações function App, compartilhamento de uma conta de armazenamento partilhado, deverá configurar explicitamente nomes diferentes para cada hub de tarefas no *Host. JSON* ficheiros. Caso contrário, as várias aplicações de funções irão competem entre si para mensagens, que podem resultar num comportamento indefinido.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saber como lidar com controlo de versões](durable-functions-versioning.md)

<!-- Update_Description: wording update -->