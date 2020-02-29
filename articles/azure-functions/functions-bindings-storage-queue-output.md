---
title: Ligação de saída de armazenamento de fila Azure para funções Azure
description: Aprenda a criar mensagens de armazenamento de fila Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164649"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Encadernações de saída de armazenamento de fila Azure para funções azure

As Funções Azure podem criar novas mensagens de armazenamento de fila Azure através da criação de uma ligação de saída.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que cria uma mensagem de fila para cada pedido http recebido.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho HTTP num ficheiro *function.json* e [ C# código script (.csx)](functions-reference-csharp.md) que utiliza a ligação. A função cria um item de fila com uma carga de objeto **CustomQueueMessage** para cada pedido http recebido.

Aqui está o ficheiro *função.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está C# o código do script que cria uma única mensagem de fila:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Pode enviar várias mensagens ao mesmo tempo utilizando um `ICollector` ou `IAsyncCollector` parâmetro. Aqui está C# o código script que envia várias mensagens, uma com os dados de pedido http e outra com valores codificados:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de gatilho HTTP num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função cria um item de fila para cada pedido http recebido.

Aqui está o ficheiro *função.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Pode enviar várias mensagens ao mesmo tempo, definindo um conjunto de mensagens para a ligação de saída `myQueueItem`. O seguinte código JavaScript envia duas mensagens de fila com valores codificados para cada pedido http recebido.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[python](#tab/python)

O exemplo seguinte demonstra como obter valores únicos e múltiplos para as filas de armazenamento. A configuração necessária para *função.json* é a mesma de qualquer forma.

Uma ligação de fila de armazenamento é definida na *função.json* onde o *tipo* está definido para `queue`.

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Para definir uma mensagem individual na fila, passa um único valor para o método `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Para criar várias mensagens na fila, declare um parâmetro como o tipo de lista apropriado e passe uma série de valores (que correspondam ao tipo de lista) ao método `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 O exemplo seguinte mostra uma função Java que cria uma mensagem de fila para quando desencadeada por um pedido HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a anotação `@QueueOutput` em parâmetros cujo valor seria escrito para armazenamento de fila.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde `T` é qualquer tipo de Java nativo de um POJO.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

O atributo aplica-se a um parâmetro `out` ou ao valor de retorno da função. O construtor do atributo tem o nome da fila, como mostra o seguinte exemplo:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Pode definir a propriedade `Connection` para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte o [exemplo da saída](#example).

Pode utilizar o atributo `StorageAccount` para especificar a conta de armazenamento na classe, método ou nível de parâmetro. Para mais informações, consulte Trigger - atributos.

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A anotação `QueueOutput` permite escrever uma mensagem como saída de uma função. O exemplo seguinte mostra uma função desencadeada pelo HTTP que cria uma mensagem de fila.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é ativada, o valor deste parâmetro tem o conteúdo da mensagem de fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para o fio de ligação da conta de armazenamento. |

O parâmetro associado à anotação `QueueOutput` é dactilografado como um\<[T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) exemplo.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Queue`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `queue`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | deve ser definido para `out`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a fila no código de função. Definir para `$return` para referenciar o valor de devolução da função.|
|**queueName** |**Nome da fila** | O nome da fila. |
|**conexão** | **Conexão** |O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName`. Pode utilizar o tipo de devolução do método em vez de um parâmetro `out`, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se a `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Dentro C# C# e script, escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName`. O `paramName` é o valor especificado na propriedade `name` *função.json*. Pode utilizar o tipo de devolução do método em vez de um parâmetro `out`, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se a `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Dentro C# C# e script, escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O item da fila de saída está disponível através `context.bindings.<NAME>` onde `<NAME>` corresponde ao nome definido na *função.json*. Pode utilizar uma corda ou um objeto json-serializável para a carga útil do item da fila.

# <a name="python"></a>[python](#tab/python)

Existem duas opções para distribuir uma mensagem do Event Hub a partir de uma função:

- **Valor de devolução**: Coloque a propriedade `name` em *função.json* para `$return`. Com esta configuração, o valor de retorno da função é persistido como uma mensagem de armazenamento de fila.

- **Imperativo**: Passe um valor para o método [definido](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) O valor passado para `set` é persistiu como uma mensagem de armazenamento de fila.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para eliminar uma mensagem do Event Hub a partir de uma função, utilizando a anotação [QueueOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Valor de devolução**: Aplicando a anotação à própria função, o valor de retorno da função é persinedo como mensagem Do Centro de Eventos.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`, ](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)onde `T` seja um POJO ou qualquer tipo de Java nativo. Com esta configuração, passar um valor para o método `setValue` persiste o valor como mensagem Do Hub do Evento.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo |  Referência |
|---|---|
| Filas | [Códigos de erro de fila](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Mesa, Fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila |  [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x e superiores. O ficheiro exemplo host.json abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais de configuração nas versões 2.x e além, consulte a [referência host.json para funções azure](functions-host-json.md).

> [!NOTE]
> Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|maxPollingInterval|00:00:01|O intervalo máximo entre as sondagens. O mínimo é 00:00:100 (100 ms) e incrementos até 00:01:00 (1 min).  Em 1.x o tipo de dados é milissegundos, e em 2.x e superior é um TimeSpan.|
|visibilidadeTimeout|00:00:00|O intervalo de tempo entre as tentativas de repetição ao processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens de fila que as Funções executam o tempo de funcionamento recupera simultaneamente e processa em paralelo. Quando o número que está a ser processado chega ao `newBatchThreshold`, o tempo de execução recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas que são processadas por função é `batchSize` mais `newBatchThreshold`. Este limite aplica-se separadamente a cada função acionada pela fila. <br><br>Se quiser evitar a execução paralela de mensagens recebidas numa fila, pode definir `batchSize` para 1. No entanto, esta definição elimina a conmoeda apenas enquanto a sua aplicação de função funcionar numa única máquina virtual (VM). Se a aplicação de função se dimensionar para vários VMs, cada VM pode executar uma instância de cada função desencadeada pela fila.<br><br>O máximo `batchSize` é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila do veneno.|
|newBatchThreshold|loteTamanho/2|Sempre que o número de mensagens que estão a ser processadas simultaneamente chega a este número, o tempo de execução recupera outro lote.|

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função como alterações de dados de armazenamento de fila (Gatilho)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
