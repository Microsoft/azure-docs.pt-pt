---
title: Ligação de entrada de armazenamento Azure Blob para funções Azure
description: Saiba como fornecer dados de ligação de entrada de armazenamento Azure Blob a uma Função Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 191722d02b493cfe0197c3e45771543fd8c5926a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961052"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Ligação de entrada de armazenamento Azure Blob para funções Azure

A ligação de entrada permite-lhe ler os dados de armazenamento de bolhas como entrada para uma Função Azure.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir é uma [função C#](functions-dotnet-class-library.md) que utiliza um gatilho de fila e uma encadernação de bolhas de entrada. A mensagem de fila contém o nome da bolha, e a função regista o tamanho da bolha.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

<!--Same example for input and output. -->

O exemplo a seguir mostra as entradas blob e as ligações de saída num *function.jsno* ficheiro e o código de [script C# (.csx)](functions-reference-csharp.md) que utiliza as ligações. A função faz uma cópia de uma bolha de texto. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha chama-se *{originalblobname}-Copy*.

No *function.jsem* ficheiro, a `queueTrigger` propriedade metadados é utilizada para especificar o nome blob nas `path` propriedades:

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código do guião C:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Esta secção contém os seguintes exemplos:

* [HTTP trigger, procure o nome blob a partir da cadeia de consulta](#http-trigger-look-up-blob-name-from-query-string)
* [Gatilho da fila, receber o nome blob da mensagem de fila](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP trigger, procure o nome blob a partir da cadeia de consulta

 O exemplo a seguir mostra uma função Java que usa a `HttpTrigger` anotação para receber um parâmetro que contenha o nome de um ficheiro num recipiente de armazenamento de bolhas. A `BlobInput` anotação, em seguida, lê o ficheiro e transmite o seu conteúdo para a função como um `byte[]` .

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Gatilho da fila, receber o nome blob da mensagem de fila

 O exemplo a seguir mostra uma função Java que usa a `QueueTrigger` anotação para receber uma mensagem contendo o nome de um ficheiro num recipiente de armazenamento de bolhas. A `BlobInput` anotação, em seguida, lê o ficheiro e transmite o seu conteúdo para a função como um `byte[]` .

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

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `@BlobInput` anotação em parâmetros cujo valor viria de uma bolha.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

O exemplo a seguir mostra a entrada de bolhas e as ligações de saída numa *function.jsno* ficheiro e [código JavaScript](functions-reference-node.md) que utiliza as ligações. A função faz uma cópia de uma bolha. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha chama-se *{originalblobname}-Copy*.

No *function.jsem* ficheiro, a `queueTrigger` propriedade metadados é utilizada para especificar o nome blob nas `path` propriedades:

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra uma ligação de entrada de bolhas, definida no _function.jsem_ ficheiro, que coloca os dados blob de entrada disponíveis na função [PowerShell.](functions-reference-powershell.md)

Aqui está a configuração json:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Aqui está o código de função:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

O exemplo a seguir mostra a entrada de bolhas e as ligações de saída numa *function.jsno* ficheiro e [código Python](functions-reference-python.md) que utiliza as ligações. A função faz uma cópia de uma bolha. A função é desencadeada por uma mensagem de fila que contém o nome da bolha para copiar. A nova bolha chama-se *{originalblobname}-Copy*.

No *function.jsem* ficheiro, a `queueTrigger` propriedade metadados é utilizada para especificar o nome blob nas `path` propriedades:

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
      "dataType": "binary",
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

A secção [de configuração](#configuration) explica estas propriedades.

A `dataType` propriedade determina qual a ligação utilizada. Estão disponíveis os seguintes valores para apoiar diferentes estratégias vinculativas:

| Valor vinculativo | Predefinição | Descrição | Exemplo |
| --- | --- | --- | --- |
| `string` | N | Usa ligação genérica e lança o tipo de entrada como um `string` | `def main(input: str)` |
| `binary` | N | Usa ligação genérica e lança a bolha de entrada como `bytes` objeto Python | `def main(input: bytes)` |

Se a `dataType` propriedade não for definida em function.js, o valor padrão é `string` .

Aqui está o código Python:

```python
import logging
import azure.functions as func


# The type func.InputStream is not supported for blob input binding.
# The input binding field inputblob can either be 'bytes' or 'str' depends
# on dataType in function.json, 'binary' or 'string'.
def main(queuemsg: func.QueueMessage, inputblob: bytes) -> bytes:
    logging.info(f'Python Queue trigger function processed {len(inputblob)} bytes')
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

O construtor do atributo toma o caminho para a bolha e um `FileAccess` parâmetro indicando ler ou escrever, como mostra o seguinte exemplo:

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

Pode definir a `Connection` propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

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

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Trigger - atributos e anotações](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

O `@BlobInput` atributo dá-lhe acesso à bolha que desencadeou a função. Se utilizar uma matriz de byte com o atributo, de definido `dataType` para `binary` . Consulte o [exemplo de entrada](#example) para obter mais detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Blob` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `blob` . |
|**direção** | n/a | Deve ser definido para `in` . As exceções são anotados na secção [de utilização.](#usage) |
|**nome** | n/a | O nome da variável que representa a bolha no código de função.|
|**caminho** |**BlobPath** | O caminho para a bolha. |
|**conexão** |**Ligação**| O nome de uma definição de aplicação que contém o [fio de ligação de armazenamento](../storage/common/storage-configure-connection-string.md) para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução de Funções procura uma definição de aplicação que se chama "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .<br><br>O fio de ligação deve destinar-se a uma conta de armazenamento para fins gerais e não a uma [conta de armazenamento apenas com bolhas](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Se estiver a utilizar [a versão 5.x ou superior da extensão](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher), em vez de uma cadeia de ligação, pode fornecer uma referência a uma secção de configuração que defina a ligação. Ver [Ligações](./functions-reference.md#connections).|
|**dataType**| n/a | Para idiomas de tipo dinâmico, especifica o tipo de dados subjacente. Os valores possíveis `string` `binary` são, ou `stream` . Para obter mais detalhes, consulte os [conceitos de gatilhos e encadernações.](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions) |
|n/a | **Acesso** | Indica se vai ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

O `@BlobInput` atributo dá-lhe acesso à bolha que desencadeou a função. Se utilizar uma matriz de byte com o atributo, de definido `dataType` para `binary` . Consulte o [exemplo de entrada](#example) para obter mais detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceder aos dados blob `context.bindings.<NAME>` utilizando o valor definido nafunction.js`<NAME>` *em*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aceda aos dados blob através de um parâmetro que corresponda ao nome designado pelo parâmetro de nome de encadernação no _function.jsem_ ficheiro.

# <a name="python"></a>[Python](#tab/python)

Aceder aos dados do blob através do parâmetro dactilografado como [InputStream](/python/api/azure-functions/azure.functions.inputstream). Consulte o [exemplo de entrada](#example) para obter mais detalhes.

---

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando os dados de armazenamento blob mudam](./functions-bindings-storage-blob-trigger.md)
- [Escreva dados de armazenamento de bolhas a partir de uma função](./functions-bindings-storage-blob-output.md)
