---
title: Utilização do valor de retorno de uma Função Azure
description: Aprenda a gerir os valores de retorno para funções azure
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74480581"
---
# <a name="using-the-azure-function-return-value"></a>Utilização do valor de retorno da Função Azure

Este artigo explica como os valores de retorno funcionam dentro de uma função.

Em idiomas que tenham um valor de retorno, pode ligar uma [função](./functions-triggers-bindings.md#binding-direction) de saída que ligue ao valor de retorno:

* Numa biblioteca de classe C#, aplique o atributo de ligação de saída ao valor de retorno do método.
* Em Java, aplique a anotação de ligação de saída ao método da função.
* Em outras línguas, coloque a `name` propriedade em *função.json* para `$return`.

Se existirem várias encadernações de saída, utilize o valor de devolução para apenas uma delas.

No script C# e C#, formas alternativas `out` de enviar dados para uma ligação de saída são parâmetros e [objetos de coleção](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C #](#tab/csharp)

Aqui está o código C# que usa o valor de retorno para uma ligação de saída, seguido de um exemplo de sincronização:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aqui está a ligação de saída no ficheiro *função.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Aqui está o código de script C#, seguido por um exemplo asincronizado:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F#](#tab/fsharp)

Aqui está a ligação de saída no ficheiro *função.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Aqui está o código F#:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui está a ligação de saída no ficheiro *função.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

No JavaScript, o valor de devolução `context.done`vai no segundo parâmetro para:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Pitão](#tab/python)

Aqui está a ligação de saída no ficheiro *função.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Aqui está o código Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Aqui está o código Java que usa o valor de devolução para uma ligação de saída:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Manipular funções azure erros de ligação](./functions-bindings-errors.md)
