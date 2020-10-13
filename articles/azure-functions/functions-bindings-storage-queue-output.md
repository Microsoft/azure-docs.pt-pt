---
title: Ligação de saída de armazenamento da fila Azure para funções Azure
description: Aprenda a criar mensagens de armazenamento da Fila Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 1d86009d593ef7e594ec2981132bcfb856569c31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317230"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Encadernações de saída de armazenamento da fila Azure para funções Azure

As Funções Azure podem criar novas mensagens de armazenamento da Fila Azure, configurando uma ligação de saída.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que cria uma mensagem de fila para cada pedido HTTP recebido.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação http num *function.jsno* ficheiro e o código de [script C# (.csx)](functions-reference-csharp.md) que utiliza a ligação. A função cria um item de fila com uma carga de objeto **CustomQueueMessage** para cada pedido HTTP recebido.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código de script C# que cria uma única mensagem de fila:

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

Pode enviar várias mensagens de uma só vez utilizando um `ICollector` parâmetro ou `IAsyncCollector` parâmetro. Aqui está o código de script C# que envia várias mensagens, uma com os dados de pedido HTTP e outra com valores codificados:

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

O exemplo a seguir mostra uma ligação do gatilho HTTP numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função cria um item de fila para cada pedido HTTP recebido.

Aqui está a *function.jsarquivada:*

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
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Pode enviar várias mensagens de uma só vez definindo um conjunto de mensagens para a `myQueueItem` ligação de saída. O seguinte código JavaScript envia duas mensagens de fila com valores codificados por código rígido para cada pedido HTTP recebido.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como desau o resultado de valores únicos e múltiplos para as filas de armazenamento. A configuração necessária para *function.jsé* a mesma de qualquer forma.

Uma ligação de fila de armazenamento é definida em *function.jsno* local onde *o tipo* está definido para `queue` .

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

Para definir uma mensagem individual na fila, passa-se um único valor ao `set` método.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Para criar várias mensagens na fila, declare um parâmetro como o tipo de lista apropriado e passe uma matriz de valores (que correspondam ao tipo de lista) ao `set` método.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 O exemplo a seguir mostra uma função Java que cria uma mensagem de fila para quando desencadeada por um pedido HTTP.

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

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `@QueueOutput` anotação em parâmetros cujo valor seria escrito para o armazenamento da fila.  O tipo de parâmetro deve ser `OutputBinding<T>` , onde está qualquer tipo nativo java de um `T` POJO.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize a [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

O atributo aplica-se a um `out` parâmetro ou ao valor de devolução da função. O construtor do atributo tem o nome da fila, como mostra o seguinte exemplo:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Pode definir a `Connection` propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [o exemplo de Saída](#example).

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte o Trigger - atributos.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A `QueueOutput` anotação permite-lhe escrever uma mensagem como saída de uma função. O exemplo a seguir mostra uma função acionada por HTTP que cria uma mensagem de fila.

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
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é desencadeada, o valor deste parâmetro tem o conteúdo da mensagem de fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para o fio de ligação da conta de armazenamento. |

O parâmetro associado à `QueueOutput` anotação é dactilografado como uma instância [de Fuga \<T\> de Saída.](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Queue` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `queue` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/a | Deve ser definido para `out` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome da variável que representa a fila no código de função. Definir `$return` para referenciar o valor de retorno da função.|
|**nome de fila** |**Nome da fila** | O nome da fila. |
|**conexão** | **Ligação** |O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` "MyStorage", o tempo de execução de Funções procura uma definição de app chamada "MyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName` . Pode utilizar o tipo de devolução do método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

No script C# e C# escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName` . O `paramName` valor especificado na `name` propriedade de *function.jsem*. Pode utilizar o tipo de devolução do método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

No script C# e C# escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O item da fila de saída está disponível através do `context.bindings.<NAME>` local onde corresponde ao nome definido emfunction.js`<NAME>` * em*. Pode utilizar uma corda ou um objeto serializável JSON para a carga útil do item da fila.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para a saída de uma mensagem de fila a partir de uma função:

- **Valor de retorno**: Desa estafunction.js`name` o *imóvel.* `$return` Com esta configuração, o valor de retorno da função é persistido como uma mensagem de armazenamento de fila.

- **Imperativo**: Passe um valor ao método [definido](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](/python/api/azure-functions/azure.functions.out?view=azure-python) O valor passado `set` é persistido como uma mensagem de armazenamento de fila.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para a saída de uma mensagem de fila a partir de uma função utilizando a anotação [QueueOutput:](/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Valor de retorno**: Aplicando a anotação à função em si, o valor de retorno da função é persistido como uma mensagem de fila.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , onde se encontra um `T` POJO ou qualquer tipo de Java nativo. Com esta configuração, passar um valor para o `setValue` método persiste o valor como uma mensagem de fila.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace |  Referência |
|---|---|
| Fila | [Códigos de erro de fila](/rest/api/storageservices/queue-service-error-codes) |
| Blob, Mesa, Fila | [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila |  [Resolução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnas definições

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x ou superiores. O exemplo host.jsno ficheiro abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais nas versões 2.x e não só, consulte [host.jsem referência para Funções Azure](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.jsem Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](functions-host-json-v1.md).

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
|maxPollingInterval|00:00:01|O intervalo máximo entre as sondagens. Mínimo é 00:00:00.100 (100 ms) e incrementos até 00:01:00 (1 min).  Em 1.x o tipo de dados é milissegundos, e em 2.x e superior é um TimeSpan.|
|visibilidadeTimeout|00:00:00|O intervalo de tempo entre as recauchutagens ao processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens de fila que o tempo de funcionamento das Funções recupera simultaneamente e processa em paralelo. Quando o número que está a ser processado chega ao `newBatchThreshold` , o tempo de funcionamento recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas a serem processadas por função é `batchSize` mais `newBatchThreshold` . Este limite aplica-se separadamente a cada função acionada pela fila. <br><br>Se quiser evitar a execução paralela das mensagens recebidas numa fila, pode definir `batchSize` para 1. No entanto, esta definição elimina a conusncy apenas desde que a sua aplicação de função seja executado numa única máquina virtual (VM). Se a aplicação de função se dimensionar para vários VMs, cada VM pode executar uma instância de cada função desencadeada pela fila.<br><br>O máximo `batchSize` é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de veneno.|
|newBatchThreshold|lotEsize/2|Sempre que o número de mensagens a ser processadas se descientemente a este número, o tempo de funcionamento recupera outro lote.|

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função como alterações de dados de armazenamento de fila (Gatilho)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
