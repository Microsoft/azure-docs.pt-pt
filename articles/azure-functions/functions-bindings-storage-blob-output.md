---
title: Ligação de saída de armazenamento Azure Blob para funções Azure
description: Saiba como fornecer dados de armazenamento Azure Blob a uma Função Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-python
ms.openlocfilehash: d089cb1844356032b956110a7475d839031ef006
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849833"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Ligação de saída de armazenamento Azure Blob para funções Azure

A ligação de saída permite modificar e eliminar dados de armazenamento de bolhas numa Função Azure.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir é uma [função C#](functions-dotnet-class-library.md) que utiliza um gatilho blob e duas ligações de bolhas de saída. A função é desencadeada pela criação de uma bolha de imagem no recipiente *de imagens de amostragem.* Cria cópias de tamanho pequeno e médio da bolha de imagem.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Esta secção contém os seguintes exemplos:

* [Disparador HTTP, utilizando OutputBinding](#http-trigger-using-outputbinding-java)
* [Gatilho de fila, utilizando o valor de retorno da função](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Disparador HTTP, utilizando OutputBinding (Java)

 O exemplo a seguir mostra uma função Java que usa a `HttpTrigger` anotação para receber um parâmetro que contenha o nome de um ficheiro num recipiente de armazenamento de bolhas. A `BlobInput` anotação, em seguida, lê o ficheiro e transmite o seu conteúdo para a função como um `byte[]` . A `BlobOutput` anotação liga-se a `OutputBinding outputItem` , que é então utilizada pela função para escrever o conteúdo da bolha de entrada no recipiente de armazenamento configurado.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Gatilho de fila, utilizando o valor de retorno da função (Java)

 O exemplo a seguir mostra uma função Java que usa a `QueueTrigger` anotação para receber uma mensagem contendo o nome de um ficheiro num recipiente de armazenamento de bolhas. A `BlobInput` anotação, em seguida, lê o ficheiro e transmite o seu conteúdo para a função como um `byte[]` . A `BlobOutput` anotação liga-se ao valor de retorno da função, que é depois utilizado pelo tempo de execução para escrever o conteúdo da bolha de entrada no recipiente de armazenamento configurado.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `@BlobOutput` anotação em parâmetros de função cujo valor seria escrito a um objeto no armazenamento de bolhas.  O tipo de parâmetro deve ser `OutputBinding<T>` , onde T é qualquer tipo nativo java ou um POJO.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

O construtor do atributo toma o caminho para a bolha e um `FileAccess` parâmetro indicando ler ou escrever, como mostra o seguinte exemplo:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Pode definir a `Connection` propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

O `@BlobOutput` atributo dá-lhe acesso à bolha que desencadeou a função. Se utilizar uma matriz de byte com o atributo, de definido `dataType` para `binary` . Consulte o [exemplo de saída](#example) para obter mais detalhes.

---

Para um exemplo completo, consulte [o exemplo de Saída](#example).

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Trigger - atributos](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Blob` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `blob` . |
|**direção** | n/a | Deve ser definido `out` para uma ligação de saída. As exceções são anotados na secção [de utilização.](#usage) |
|**nome** | n/a | O nome da variável que representa a bolha no código de função.  Definir `$return` para referenciar o valor de retorno da função.|
|**caminho** |**BlobPath** | O caminho para o recipiente de bolhas. |
|**conexão** |**Ligação**| O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` "MyStorage", o tempo de execução de Funções procura uma definição de aplicação que se chama "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .<br><br>O fio de ligação deve destinar-se a uma conta de armazenamento para fins gerais e não a uma [conta de armazenamento apenas com bolhas](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/a | **Acesso** | Indica se vai ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

No JavaScript, aceda aos dados blob utilizando `context.bindings.<name from function.json>` .

# <a name="python"></a>[Python](#tab/python)

Pode declarar os parâmetros de função como os seguintes tipos para escrever para o armazenamento de bolhas:

* Cordas como`func.Out(str)`
* Riachos como`func.Out(func.InputStream)`

Consulte o [exemplo de saída](#example) para obter mais detalhes.

# <a name="java"></a>[Java](#tab/java)

O `@BlobOutput` atributo dá-lhe acesso à bolha que desencadeou a função. Se utilizar uma matriz de byte com o atributo, de definido `dataType` para `binary` . Consulte o [exemplo de saída](#example) para obter mais detalhes.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace |  Referência |
|---|---|
| Blob | [Códigos de erro blob](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Mesa, Fila |  [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila |  [Resolução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando os dados de armazenamento blob mudam](./functions-bindings-storage-blob-trigger.md)
- [Leia os dados de armazenamento de bolhas quando uma função é executado](./functions-bindings-storage-blob-input.md)
