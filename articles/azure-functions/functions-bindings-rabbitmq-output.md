---
title: Ligações de saída RabbitMQ para Funções Azure
description: Aprenda a enviar mensagens RabbitMQ das Funções Azure.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: d9e575d68fe4fef607bdf443ece1ddd04f085533
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746461"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Ligação de saída RabbitMQ para visão geral das funções do Azure

> [!NOTE]
> As ligações RabbitMQ só são totalmente suportadas em planos **Premium e Dedicados.** O consumo não é suportado.

Utilize a ligação de saída RabbitMQ para enviar mensagens para uma fila RabbitMQ.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem RabbitMQ quando ativada por um TimerTrigger a cada 5 minutos utilizando o valor de retorno do método como saída:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo a seguir mostra como utilizar a interface IAsyncCollector para enviar mensagens.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

O exemplo a seguir mostra como enviar as mensagens como POCOs.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação de saída RabbitMQ numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função lê na mensagem a partir de um gatilho HTTP e a output para a fila RabbitMQ.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Aqui está o código do guião C:

```C#
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação de saída RabbitMQ numa *function.jsem* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função lê na mensagem a partir de um gatilho HTTP e a output para a fila RabbitMQ.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação de saída RabbitMQ numa *function.jsem* ficheiro e uma função Python que utiliza a ligação. A função lê na mensagem a partir de um gatilho HTTP e a output para a fila RabbitMQ.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Init *_\__ \_ .py:*

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que envia uma mensagem para a fila RabbitMQ quando acionada por um TimerTrigger a cada 5 minutos.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Aqui está um `RabbitMQAttribute` atributo numa assinatura de método:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte o [exemplo](#example)C# .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A `RabbitMQOutput` anotação permite-lhe criar uma função que funciona ao enviar uma mensagem RabbitMQ. As opções de configuração disponíveis incluem nome de fila e nome de cadeia de ligação. Para mais detalhes sobre parâmetros, visite as [anotações de Java RabbitMQOutput](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

Consulte o [exemplo](#example) de ligação de saída para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `RabbitMQ` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para "RabbitMQ".|
|**direção** | n/a | Deve ser definido para "fora". |
|**nome** | n/a | O nome da variável que representa a fila no código de função. |
|**nome de fila**|**Nome da fila**| Nome da fila para enviar mensagens. |
|**nome hospedeiro**|**Nome anfitrião**|(ignorado se utilizar o ConnectStringSetting) <br>Nome anfitrião da fila (Ex: 10.26.45.210)|
|**nome de utilizador**|**Nome de utilizador**|(ignorado se utilizar o ConnectionStringSetting) <br>Nome da definição da aplicação que contém o nome de utilizador para aceder à fila. Por exemplo: UserNameSetting: "< UserNameFromSettings >"|
|**palavra-passe**|**Palavra-passe**|(ignorado se utilizar o ConnectionStringSetting) <br>Nome da definição da aplicação que contém a palavra-passe para aceder à fila. Por exemplo: UserNameSetting: "< UserNameFromSettings >"|
|**conexãoStringSetting**|**ConexãoStringSetting**|O nome da definição da aplicação que contém a cadeia de ligação da fila da mensagem RabbitMQ. Por favor, note que se especificar diretamente o fio de ligação e não através de uma definição de aplicação no local.settings.jsligado, o gatilho não funcionará. (Ex: Em *function.jssobre:* connectionStringSetting: "rabbitMQConnection" <br> Em *local.settings.jsem*: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**porto**|**Porta**|(ignorado se utilizar o ConnectionStringSetting) Recebe ou define o Porto usado. Predefinições a 0 que aponta para a definição padrão da porta do cliente rabbitmq: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `byte[]` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `string` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `POCO` - Se o valor do parâmetro não for formatado como objeto C#, será recebido um erro. Para um exemplo completo, consulte o [exemplo](#example)C# .

Ao trabalhar com funções C#:

* As funções async precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `byte[]` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `string` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `POCO` - Se o valor do parâmetro não for formatado como objeto C#, será recebido um erro. Para um exemplo completo, consulte o [exemplo](#example)do script C# .

Ao trabalhar com as funções de script C# :

* As funções async precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A mensagem de fila está disponível através de contexto.encadernações.<NAME> onde <NAME> corresponde ao nome definido em function.js. Se a carga útil for JSON, o valor é deseralizado num objeto.

# <a name="python"></a>[Python](#tab/python)

Consulte o [exemplo](#example)Python .

# <a name="java"></a>[Java](#tab/java)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `byte[]` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `string` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `POJO` - Se o valor do parâmetro não for formatado como objeto Java, será recebido um erro.

---

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando uma mensagem RabbitMQ é criada (Gatilho)](./functions-bindings-rabbitmq-trigger.md)
