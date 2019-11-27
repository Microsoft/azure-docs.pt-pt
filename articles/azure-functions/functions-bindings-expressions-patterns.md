---
title: Padrões e expressões de associações de Azure Functions
description: Aprenda a criar diferentes expressões de associação de Azure Functions com base em padrões comuns.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: f00637ff2c8cf39b683056b041fe0e991276a065
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227227"
---
# <a name="azure-functions-binding-expression-patterns"></a>Padrões de expressão de associação de Azure Functions

Um dos recursos mais poderosos de [gatilhos e associações](./functions-triggers-bindings.md) é a *Associação de expressões*. No arquivo *Function. JSON* e em parâmetros de função e código, você pode usar expressões que são resolvidas para valores de várias fontes.

A maioria das expressões é identificada encapsulando-as entre chaves. Por exemplo, em uma função de gatilho de fila, `{queueTrigger}` resolve para o texto da mensagem da fila. Se a propriedade `path` para uma associação de saída de blob for `container/{queueTrigger}` e a função for disparada por uma mensagem de fila `HelloWorld`, um blob chamado `HelloWorld` será criado.

Tipos de expressões de associação

* [Configurações do aplicativo](#binding-expressions---app-settings)
* [Nome do arquivo de gatilho](#trigger-file-name)
* [Metadados de gatilho](#trigger-metadata)
* [Cargas JSON](#json-payloads)
* [Novo GUID](#create-guids)
* [Data e hora atuais](#current-time)

## <a name="binding-expressions---app-settings"></a>Expressões de associação – configurações de aplicativo

Como prática recomendada, os segredos e as cadeias de conexão devem ser gerenciados usando as configurações do aplicativo, em vez dos arquivos de configuração. Isso limita o acesso a esses segredos e torna seguro armazenar arquivos como *Function. JSON* em repositórios de controle do código-fonte público.

As configurações do aplicativo também são úteis sempre que você deseja alterar a configuração com base no ambiente. Por exemplo, em um ambiente de teste, talvez você queira monitorar um contêiner de armazenamento de BLOB ou de fila diferente.

As expressões de associação de configuração de aplicativo são identificadas de forma diferente de outras expressões de associação: são encapsuladas em sinais de porcentagem em vez de chaves. Por exemplo, se o caminho de associação de saída de blob for `%Environment%/newblob.txt` e o valor de configuração do aplicativo `Environment` for `Development`, um blob será criado no contêiner de `Development`.

Quando uma função é executada localmente, os valores de configuração de aplicativo são provenientes do arquivo *local. Settings. JSON* .

Observe que a propriedade `connection` de gatilhos e associações é um caso especial e resolve automaticamente os valores como configurações do aplicativo, sem sinais de porcentagem. 

O exemplo a seguir é um gatilho de armazenamento de filas do Azure que usa uma configuração de aplicativo `%input-queue-name%` para definir a fila na qual disparar.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Você pode usar a mesma abordagem em bibliotecas de classes:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Nome do arquivo de gatilho

O `path` para um gatilho de blob pode ser um padrão que permite que você consulte o nome do blob de gatilho em outras associações e código de função. O padrão também pode incluir critérios de filtragem que especificam quais BLOBs podem disparar uma invocação de função.

Por exemplo, na associação de gatilho de blob a seguir, o padrão de `path` é `sample-images/{filename}`, que cria uma expressão de associação denominada `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

A expressão `filename` pode então ser usada em uma associação de saída para especificar o nome do blob que está sendo criado:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

O código de função tem acesso a esse mesmo valor usando `filename` como um nome de parâmetro:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

A mesma capacidade de usar expressões de associação e padrões se aplica a atributos em bibliotecas de classes. No exemplo a seguir, os parâmetros do construtor de atributo são os mesmos valores de `path` que os exemplos de *Function. JSON* anteriores: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Você também pode criar expressões para partes do nome do arquivo, como a extensão. Para obter mais informações sobre como usar expressões e padrões na cadeia de caracteres de caminho de BLOB, consulte a [referência de associação de blob de armazenamento](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadados de gatilho

Além da carga de dados fornecida por um gatilho (como o conteúdo da mensagem da fila que disparou uma função), muitos disparadores fornecem valores de metadados adicionais. Esses valores podem ser usados como parâmetros de entrada C# nas F# Propriedades e ou no objeto `context.bindings` em JavaScript. 

Por exemplo, um gatilho de armazenamento de filas do Azure dá suporte às seguintes propriedades:

* QueueTrigger – disparando o conteúdo da mensagem se uma cadeia de caracteres válida
* DequeueCount
* expirationTime
* Id
* Inserttime
* NextVisibleTime
* PopReceipt

Esses valores de metadados são acessíveis nas propriedades do arquivo *Function. JSON* . Por exemplo, suponha que você use um gatilho de fila e a mensagem da fila contenha o nome de um blob que você deseja ler. No arquivo *Function. JSON* , você pode usar a propriedade `queueTrigger` Metadata na propriedade blob `path`, conforme mostrado no exemplo a seguir:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Os detalhes das propriedades de metadados de cada gatilho são descritos no artigo de referência correspondente. Para obter um exemplo, consulte [metadados de gatilho de fila](functions-bindings-storage-queue.md#trigger---message-metadata). A documentação também está disponível na guia **integrar** do portal, na seção **documentação** abaixo da área configuração de associação.  

## <a name="json-payloads"></a>Cargas JSON

Quando uma carga de gatilho é JSON, você pode consultar suas propriedades na configuração de outras associações na mesma função e no código de função.

O exemplo a seguir mostra o arquivo *Function. JSON* para uma função webhook que recebe um nome de BLOB em json: `{"BlobName":"HelloWorld.txt"}`. Uma associação de entrada de blob lê o blob e a associação de saída HTTP retorna o conteúdo do blob na resposta HTTP. Observe que a associação de entrada de blob Obtém o nome do blob referindo-se diretamente à propriedade `BlobName` (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Para que isso funcione no C# e F#no, você precisa de uma classe que defina os campos a serem desserializados, como no exemplo a seguir:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

No JavaScript, a desserialização JSON é executada automaticamente.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Notação de ponto

Se algumas das propriedades em seu conteúdo JSON forem objetos com propriedades, você poderá consultá-las diretamente usando a notação de ponto. Por exemplo, suponha que seu JSON tenha a seguinte aparência:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Você pode fazer referência diretamente a `FileName` como `BlobName.FileName`. Com esse formato JSON, aqui está o que a propriedade `path` no exemplo anterior teria a seguinte aparência:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

No C#, você precisaria de duas classes:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Criar GUIDs

A expressão de associação de `{rand-guid}` cria um GUID. O caminho de blob a seguir em um arquivo de `function.json` cria um blob com um nome como *50710cb5-84b9-4d87-9d83-a03d6976a682. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Hora atual

A expressão de associação `DateTime` é resolvida para `DateTime.UtcNow`. O caminho de blob a seguir em um arquivo de `function.json` cria um blob com um nome como *2018-02-16T17-59 -55z. txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Associação em tempo de execução

No C# e em outras linguagens .net, você pode usar um padrão de associação imperativo, em oposição às associações declarativas em *Function. JSON* e atributos. A associação imperativa é útil quando parâmetros de associação precisam ser computados em tempo de execução em vez de tempo de design. Para saber mais, consulte a [ C# referência do desenvolvedor](functions-dotnet-class-library.md#binding-at-runtime) ou a [ C# referência do desenvolvedor de script](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Usando o valor de retorno da função do Azure](./functions-bindings-return-value.md)
