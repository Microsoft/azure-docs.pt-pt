---
title: Utilizar o valor de retorno de uma função do Azure
description: Saiba como gerir os valores de retorno das funções do Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739916"
---
# <a name="using-the-azure-function-return-value"></a>Utilizar o valor de retorno da função do Azure

Este artigo explica como o trabalho de valores de retorno dentro de uma função.

Em idiomas que têm um valor de retorno, é possível vincular uma função [enlace de saída](./functions-triggers-bindings.md#binding-direction) para o valor de retorno:

* No c# biblioteca de classes, aplica o atributo de enlace de saída para o valor de retorno do método.
* Em outros idiomas, defina o `name` propriedade na *Function* para `$return`.

Se existirem vários enlaces de saída, utilize o valor de retorno para apenas um deles.

No c# e de script c#, maneiras alternativas de enviar dados para um enlace de saída são `out` parâmetros e [objetos de coletor](functions-reference-csharp.md#writing-multiple-output-values).

Veja o exemplo de idioma específico, que mostra a utilização de valor de retorno:

* [C#](#c-example)
* [Script do c# (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>Exemplo do c#

O código c# aqui está que utiliza o valor de retorno para um enlace de saída, seguido de um exemplo de async:

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

## <a name="c-script-example"></a>Exemplo de script do c#

Este é o enlace de saída no *Function* ficheiro:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Aqui está o script código c#, seguido de um exemplo de async:

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

## <a name="f-example"></a>F#exemplo

Este é o enlace de saída no *Function* ficheiro:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Aqui está o F# código:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>Exemplo de JavaScript

Este é o enlace de saída no *Function* ficheiro:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

No JavaScript, o valor de retorno vai no segundo parâmetro para `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Exemplo de Python

Este é o enlace de saída no *Function* ficheiro:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Aqui está o código de Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Lidar com erros de ligação de funções do Azure](./functions-bindings-errors.md)
