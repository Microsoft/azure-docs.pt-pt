---
title: Utilização do valor de retorno de uma Função Azure
description: Aprenda a gerir os valores de retorno para funções Azure
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 58ee9b682bc97dc4044d811392cf4ff5b51a69fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491363"
---
# <a name="using-the-azure-function-return-value"></a>Utilizando o valor de retorno da função Azure

Este artigo explica como os valores de retorno funcionam dentro de uma função.

Em línguas que têm um valor de retorno, pode ligar uma [função de saída que vincula](./functions-triggers-bindings.md#binding-direction) ao valor de retorno:

* Numa biblioteca de classe C' aplique o atributo de encadernação de saída ao valor de retorno do método.
* Em Java, aplique a anotação de ligação de saída ao método da função.
* Em outras línguas, coloque a `name` propriedade em *function.js* para `$return` .

Se existirem múltiplas ligações de saída, utilize o valor de retorno para apenas um deles.

No script C# e C#, as formas alternativas de enviar dados para uma ligação de saída são `out` parâmetros e [objetos de coletor](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C#](#tab/csharp)

Aqui está o código C# que usa o valor de retorno para uma ligação de saída, seguido de um exemplo async:

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

Aqui está a ligação de saída no *function.jsarquivado:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Aqui está o código de script C#, seguido de um exemplo async:

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

Aqui está a ligação de saída no *function.jsarquivado:*

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

Aqui está a ligação de saída no *function.jsarquivado:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

No JavaScript, o valor de retorno vai no segundo parâmetro `context.done` para:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Aqui está a ligação de saída no *function.jsarquivado:*

```json
{
    "name": "Response",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{blobname}"
}
```

Aqui está o código PowerShell que utiliza o valor de devolução para uma saída http:

```powershell
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $blobname
    })
```

# <a name="python"></a>[Python](#tab/python)

Aqui está a ligação de saída no *function.jsarquivado:*

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
> [Lidar com erros de ligação das funções Azure](./functions-bindings-errors.md)
