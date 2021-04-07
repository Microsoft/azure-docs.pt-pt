---
title: Ligação de saída de armazenamento da fila Azure para funções Azure
description: Aprenda a criar mensagens de armazenamento da Fila Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 5d94625e3eb121e556b28038cf59626be1332966
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455810"
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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os seguintes exemplos de código demonstram como descodificá-lo a partir de uma função desencadeada por HTTP. A secção de configuração com o `type` de `queue` define a ligação de saída.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Utilizando esta configuração de ligação, uma função PowerShell pode criar uma mensagem de fila utilizando `Push-OutputBinding` . Neste exemplo, uma mensagem é criada a partir de uma cadeia de consulta ou parâmetro corporal.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Para enviar várias mensagens de uma só vez, defina um conjunto de mensagens e use `Push-OutputBinding` para enviar mensagens para a ligação de saída da fila.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Queue` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `queue` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/a | Deve ser definido para `out` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome da variável que representa a fila no código de função. Definir `$return` para referenciar o valor de retorno da função.|
|**nome de fila** |**Nome da fila** | O nome da fila. |
|**conexão** | **Ligação** |O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui.<br><br>Por exemplo, se definir `connection` "MyStorage", o tempo de execução de Funções procura uma definição de app chamada "MyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .<br><br>Se estiver a utilizar [a versão 5.x ou superior da extensão](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher), em vez de uma cadeia de ligação, pode fornecer uma referência a uma secção de configuração que defina a ligação. Ver [Ligações](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Predefinição

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName` . Pode utilizar o tipo de devolução do método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

No script C# e C# escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipos adicionais

As aplicações que utilizam a [versão 5.0.0 ou superior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também podem utilizar tipos do [Azure SDK para .NET](/dotnet/api/overview/azure/storage.queues-readme). Esta versão deixa cair o suporte para o legado `CloudQueue` e tipos a favor dos `CloudQueueMessage` seguintes tipos:

- [FilaMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [FilaCient](/dotnet/api/azure.storage.queues.queueclient) para escrever várias mensagens de fila

Por exemplo, utilizando estes tipos, consulte [o repositório GitHub para a extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>Predefinição

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName` . O `paramName` valor especificado na `name` propriedade de *function.jsem*. Pode utilizar o tipo de devolução do método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

No script C# e C# escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipos adicionais

As aplicações que utilizam a [versão 5.0.0 ou superior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também podem utilizar tipos do [Azure SDK para .NET](/dotnet/api/overview/azure/storage.queues-readme). Esta versão deixa cair o suporte para o legado `CloudQueue` e tipos a favor dos `CloudQueueMessage` seguintes tipos:

- [FilaMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [FilaCient](/dotnet/api/azure.storage.queues.queueclient) para escrever várias mensagens de fila

Por exemplo, utilizando estes tipos, consulte [o repositório GitHub para a extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para a saída de uma mensagem de fila a partir de uma função utilizando a anotação [QueueOutput:](/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Valor de retorno**: Aplicando a anotação à função em si, o valor de retorno da função é persistido como uma mensagem de fila.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , onde se encontra um `T` POJO ou qualquer tipo de Java nativo. Com esta configuração, passar um valor para o `setValue` método persiste o valor como uma mensagem de fila.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O item da fila de saída está disponível através do `context.bindings.<NAME>` local onde corresponde ao nome definido emfunction.js`<NAME>` *em*. Pode utilizar uma corda ou um objeto serializável JSON para a carga útil do item da fila.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A saída para a mensagem de fila está disponível através do `Push-OutputBinding` qual você passa argumentos que correspondem ao nome designado pelo parâmetro de encadernação nofunction.js`name` *no* ficheiro.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para a saída de uma mensagem de fila a partir de uma função:

- **Valor de retorno**: Desa estafunction.js`name` o *imóvel.* `$return` Com esta configuração, o valor de retorno da função é persistido como uma mensagem de armazenamento de fila.

- **Imperativo**: Passe um valor ao método [definido](/python/api/azure-functions/azure.functions.out#set-val--t-----none) do parâmetro declarado como um tipo [out.](/python/api/azure-functions/azure.functions.out) O valor passado `set` é persistido como uma mensagem de armazenamento de fila.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace |  Referência |
|---|---|
| Fila | [Códigos de erro de fila](/rest/api/storageservices/queue-service-error-codes) |
| Blob, Mesa, Fila | [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila |  [Resolução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função como alterações de dados de armazenamento de fila (Gatilho)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
