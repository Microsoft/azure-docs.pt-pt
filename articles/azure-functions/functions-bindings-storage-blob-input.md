---
title: Encadernação de entrada de armazenamento Azure Blob para funções azure
description: Aprenda a fornecer dados de armazenamento Azure Blob a uma Função Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e074d7d74c0c5f020cb8086124634b25012927db
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202154"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Encadernação de entrada de armazenamento Azure Blob para funções azure

A ligação de entrada permite-lhe ler os dados de armazenamento blob como entrada para uma Função Azure.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte é uma [ C# função](functions-dotnet-class-library.md) que usa um gatilho de fila e uma ligação de entrada blob. A mensagem de fila contém o nome da bolha, e a função regista o tamanho da bolha.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

<!--Same example for input and output. -->

O exemplo seguinte mostra as ligações de entrada e saída blob num ficheiro *fun.json* e [ C# código script (.csx)](functions-reference-csharp.md) que utiliza as encadernações. A função faz uma cópia de uma bolha de texto. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha *chama-se {originalblobname}-Copy*.

No ficheiro *função.json,* a propriedade `queueTrigger` metadados é usada para especificar o nome blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código de script do c#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

O exemplo seguinte mostra as ligações de entrada e saída blob num ficheiro *function.json* e [código JavaScript](functions-reference-node.md) que utiliza as encadernações. A função faz uma cópia de uma bolha. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha *chama-se {originalblobname}-Copy*.

No ficheiro *função.json,* a propriedade `queueTrigger` metadados é usada para especificar o nome blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[python](#tab/python)

<!--Same example for input and output. -->

O exemplo seguinte mostra as ligações de entrada e saída blob num ficheiro *function.json* e [código Python](functions-reference-python.md) que utiliza as encadernações. A função faz uma cópia de uma bolha. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha *chama-se {originalblobname}-Copy*.

No ficheiro *função.json,* a propriedade `queueTrigger` metadados é usada para especificar o nome blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta secção contém os exemplos seguintes:

* [GATILHO HTTP, procure o nome blob da corda de consulta](#http-trigger-look-up-blob-name-from-query-string)
* [Gatilho de fila, receber o nome blob da mensagem da fila](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>GATILHO HTTP, procure o nome blob da corda de consulta

 O exemplo seguinte mostra uma função Java que utiliza a anotação `HttpTrigger` para receber um parâmetro contendo o nome de um ficheiro num recipiente de armazenamento de bolhas. A anotação `BlobInput` lê então o ficheiro e passa o seu conteúdo para a função como `byte[]`.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Gatilho de fila, receber o nome blob da mensagem da fila

 O exemplo seguinte mostra uma função Java que utiliza a anotação `QueueTrigger` para receber uma mensagem contendo o nome de um ficheiro num recipiente de armazenamento de bolhas. A anotação `BlobInput` lê então o ficheiro e passa o seu conteúdo para a função como `byte[]`.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Na biblioteca de [tempo de funcionamento](/java/api/overview/azure/functions/runtime)de Java, utilize a anotação `@BlobInput` em parâmetros cujo valor viria de uma bolha.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores nuníveis usando `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o [BlobAttribute.](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)

O construtor do atributo segue o caminho para a bolha e um parâmetro `FileAccess` indicando ler ou escrever, como se pode ver no seguinte exemplo:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Pode definir a propriedade `Connection` para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Pode utilizar o atributo `StorageAccount` para especificar a conta de armazenamento na classe, método ou nível de parâmetro. Para mais informações, consulte [Trigger - atributos e anotações](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobInput` dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte com o atributo, coloque `dataType` para `binary`. Consulte o exemplo de [entrada](#example) para mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Blob`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `blob`. |
|**direção** | n/d | deve ser definido para `in`. As exceções são observadas na secção [de utilização.](#usage) |
|**nome** | n/d | O nome da variável que representa a bolha no código de função.|
|**caminho** |**BlobPath** | O caminho para a bolha. |
|**conexão** |**Conexão**| O nome de uma definição de aplicação que contém a cadeia de [ligação de armazenamento](../storage/common/storage-configure-connection-string.md) a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada `AzureWebJobsStorage`.<br><br>A cadeia de ligação deve ser para uma conta de armazenamento geral, não para uma [conta de armazenamento apenas para bolhas.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|n/d | **Acesso** | Indica se vai ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aceda aos dados blob utilizando `context.bindings.<NAME>` onde `<NAME>` corresponda ao valor definido na *função.json*.

# <a name="pythontabpython"></a>[python](#tab/python)

Aceda aos dados blob através do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o exemplo de [entrada](#example) para mais detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobInput` dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte com o atributo, coloque `dataType` para `binary`. Consulte o exemplo de [entrada](#example) para mais detalhes.

---

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando os dados de armazenamento blob mudar](./functions-bindings-storage-blob-trigger.md)
- [Escreva dados de armazenamento blob a partir de uma função](./functions-bindings-storage-blob-output.md)
