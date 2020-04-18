---
title: Centros de tarefas em Funções Duráveis - Azure
description: Saiba qual é o centro de tarefas na extensão de Funções Duráveis para funções azure. Aprenda a configurar centros de tarefas.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604605"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centros de tarefas em funções duráveis (Funções Azure)

Um *centro de tarefas* em [Funções Duráveis](durable-functions-overview.md) é um recipiente lógico para os recursos de armazenamento azure que são usados para orquestrações. As funções de orquestrador e de atividade só podem interagir entre si quando pertencem ao mesmo centro de tarefas.

Se várias aplicações de função partilharem uma conta de armazenamento, cada aplicação de função *deve* ser configurada com um nome de centro de tarefas separado. Uma conta de armazenamento pode conter vários centros de tarefas. O diagrama seguinte ilustra um centro de tarefas por aplicação de função em contas de armazenamento partilhadas e dedicadas.

![Diagrama mostrando contas de armazenamento partilhadas e dedicadas.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Recursos de Armazenamento Azure

Um centro de tarefas consiste nos seguintes recursos de armazenamento:

* Uma ou mais filas de controlo.
* Uma fila de trabalho.
* Uma mesa de história.
* Uma mesa de casos.
* Um recipiente de armazenamento contendo uma ou mais bolhas de aluguer.
* Um recipiente de armazenamento contendo grandes cargas de mensagem, se aplicável.

Todos estes recursos são criados automaticamente na conta de Armazenamento Azure padrão quando as funções de orquestração, entidade ou atividade funcionam ou estão programadas para funcionar. O artigo [Performance and Scale](durable-functions-perf-and-scale.md) explica como estes recursos são utilizados.

## <a name="task-hub-names"></a>Nomes do centro de tarefas

Os centros de tarefas são identificados por um nome que está em conformidade com estas regras:

* Contém apenas caracteres alfanuméricos
* Começa com uma carta
* Tem um comprimento mínimo de 3 caracteres, comprimento máximo de 45 caracteres

O nome do centro de tarefas é declarado no ficheiro *host.json,* como mostra o seguinte exemplo:

### <a name="hostjson-functions-20"></a>host.json (Funções 2.0)

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

### <a name="hostjson-functions-1x"></a>host.json (Funções 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Os centros de tarefas também podem ser configurados `host.json` utilizando as definições da aplicação, como mostra o seguinte ficheiro exemplo:

### <a name="hostjson-functions-10"></a>host.json (Funções 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Funções 2.0)

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

O nome do centro de tarefas `MyTaskHub` será definido para o valor da definição da aplicação. O `local.settings.json` seguinte demonstra como `MyTaskHub` definir `samplehubname`a definição como:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

O seguinte código demonstra como escrever uma função que utiliza a ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) para trabalhar com um centro de tarefas que é configurado como uma Definição de Aplicações:

# <a name="c"></a>[C #](#tab/csharp)

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
> O exemplo c# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A propriedade do `function.json` centro de tarefas no ficheiro é definida através da Definição de Aplicações:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Os nomes do centro de tarefas devem começar com uma letra e consistir apenas em letras e números. Se não especificado, um nome de centro de tarefas predefinido será utilizado como mostrado na tabela seguinte:

| Versão de extensão durável | Nome do centro de tarefas padrão |
| - | - |
| 2.x | Quando implantado em Azure, o nome do centro de tarefas é derivado do nome da _aplicação de função_. Quando se corre para fora de `TestHubName`Azure, o nome do centro de tarefas padrão é . |
| 1.x | O nome padrão do centro `DurableFunctionsHub`de tarefas para todos os ambientes é . |

Para obter mais informações sobre as diferenças entre versões de extensão, consulte o artigo de [versões de Funções Duráveis.](durable-functions-versions.md)

> [!NOTE]
> O nome é o que diferencia um centro de tarefas de outro quando existem múltiplos centros de tarefas numa conta de armazenamento partilhada. Se tiver várias aplicações de função que partilham uma conta de armazenamento partilhada, deve configurar explicitamente diferentes nomes para cada centro de tarefas nos *ficheiros host.json.* Caso contrário, as aplicações de múltiplas funções competirão entre si por mensagens, o `Pending` que `Running` pode resultar em comportamentos indefinidos, incluindo orquestrações ficando inesperadamente "presas" no ou estado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com a versão da orquestração](durable-functions-versioning.md)
