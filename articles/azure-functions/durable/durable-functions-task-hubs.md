---
title: Centros de tarefas em Funções Duradouras - Azure
description: Saiba o que é um centro de tarefas na extensão de Funções Duradouras para Funções Azure. Aprenda a configurar centros de tarefas.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009522"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centros de tarefas em Funções Duradouras (Funções Azure)

Um *centro de tarefas* em [Funções Duradouras](durable-functions-overview.md) é um recipiente lógico para os recursos de armazenamento Azure que são usados para orquestrações. Orquestradores e funções de atividade só podem interagir uns com os outros quando pertencem ao mesmo centro de tarefas.

Se várias aplicações de funções partilharem uma conta de armazenamento, cada aplicação de função *deve* ser configurada com um nome de hub de tarefa separado. Uma conta de armazenamento pode conter vários centros de tarefas. O diagrama seguinte ilustra um centro de tarefas por aplicação de função em contas de armazenamento partilhadas e dedicadas.

![Diagrama mostrando contas de armazenamento partilhadas e dedicadas.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Recursos de armazenamento Azure

Um centro de tarefas consiste nos seguintes recursos de armazenamento:

* Uma ou mais filas de controlo.
* Uma fila de artigos de trabalho.
* Uma mesa de história.
* Uma mesa de casos.
* Um recipiente de armazenamento contendo uma ou mais bolhas de arrendamento.
* Um recipiente de armazenamento contendo cargas de mensagens grandes, se aplicável.

Todos estes recursos são criados automaticamente na conta padrão do Azure Storage quando o orquestrador, entidade ou funções de atividade funcionam ou estão programados para executar. O artigo [Performance and Scale](durable-functions-perf-and-scale.md) explica como estes recursos são utilizados.

## <a name="task-hub-names"></a>Nomes de centros de tarefas

Os centros de tarefas são identificados por um nome que está em conformidade com estas regras:

* Contém apenas caracteres alfanuméricos
* Começa com uma carta
* Tem um comprimento mínimo de 3 caracteres, comprimento máximo de 45 caracteres

O nome do centro de tarefas é declarado no *host.jsem* arquivo, como mostra o seguinte exemplo:

### <a name="hostjson-functions-20"></a>host.js(Funções 2.0)

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

### <a name="hostjson-functions-1x"></a>host.jsem (Funções 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Os centros de tarefa também podem ser configurados usando as definições de aplicações, como mostrado no `host.json` seguinte ficheiro exemplo:

### <a name="hostjson-functions-10"></a>host.js(Funções 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.js(Funções 2.0)

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

O nome do hub de tarefa será definido para o valor da definição da `MyTaskHub` aplicação. O seguinte `local.settings.json` demonstra como definir o cenário como `MyTaskHub` `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

O seguinte código demonstra como escrever uma função que usa a ligação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) para trabalhar com um centro de tarefas configurado como uma Definição de Aplicação:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> O exemplo C# anterior é para Funções Duradouras 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A propriedade do hub de tarefa no `function.json` ficheiro é definida através da Definição de Aplicação:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

A propriedade do hub de tarefa no `function.json` ficheiro é definida através da Definição de Aplicação:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Os nomes dos centros de tarefa devem começar com uma letra e consistir apenas em letras e números. Se não for especificado, será utilizado um nome de hub de tarefa predefinido como indicado no quadro seguinte:

| Versão de extensão duradoura | Nome do centro de tarefas padrão |
| - | - |
| 2.x | Quando implantado no Azure, o nome do centro de tarefas é derivado do nome da _aplicação de função_. Quando corre fora de Azure, o nome do centro de tarefas padrão é `TestHubName` . |
| 1.x | O nome padrão do centro de tarefas para todos os ambientes é `DurableFunctionsHub` . |

Para obter mais informações sobre as diferenças entre as versões de extensão, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

> [!NOTE]
> O nome é o que diferencia um centro de tarefas de outro quando existem múltiplos centros de tarefas numa conta de armazenamento partilhada. Se tiver várias aplicações de funções que partilhem uma conta de armazenamento partilhada, tem de configurar explicitamente diferentes nomes para cada centro de tarefas no *host.jsem* ficheiros. Caso contrário, as aplicações de múltiplas funções competirão entre si por mensagens, o que pode resultar em comportamentos indefinidos, incluindo orquestrações ficando inesperadamente "presas" no `Pending` `Running` ou no estado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com a versão de orquestração](durable-functions-versioning.md)
