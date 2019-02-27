---
title: Padrões e as expressões de enlaces de funções do Azure
description: Aprenda a criar expressões de associação diferentes funções do Azure com base nos padrões comuns.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 0c1dbbae5e4be965f195b5ea4fc88b1bc5fb4f87
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887187"
---
# <a name="azure-functions-binding-expression-patterns"></a>Funções do Azure, padrões de expressões de enlace

Um dos recursos mais poderosos de [acionadores e enlaces](./functions-triggers-bindings.md) é *expressões de enlace*. Na *Function* de ficheiros e nos parâmetros da função e código, pode usar expressões que resolva para valores de várias origens.

A maioria das expressões são identificadas, encapsulando-as chavetas. Por exemplo, numa função de Acionador de fila, `{queueTrigger}` é resolvido para o texto da mensagem de fila. Se o `path` propriedade para um blob de saída é de enlace `container/{queueTrigger}` e a função é acionada por uma mensagem de fila `HelloWorld`, um blob com o nome `HelloWorld` é criado.

Tipos de expressões de associação

* [Definições da aplicação](#binding-expressions---app-settings)
* [Nome de ficheiro de Acionador](#trigger-file-name)
* [Metadados de Acionador](#trigger-metadata)
* [Conteúdo JSON](#json-payloads)
* [Novo GUID](#create-guids)
* [Data e hora atuais](#current-time)

## <a name="binding-expressions---app-settings"></a>Expressões de enlace - definições da aplicação

Como melhor prática, segredos e as cadeias de ligação devem ser geridas utilizando as definições da aplicação, em vez de arquivos de configuração. Isso limita o acesso a estes segredos e torna segura armazenar os ficheiros, tal como *Function* nos repositórios de controlo de origem pública.

Definições da aplicação também são úteis sempre que quiser alterar a configuração com base no ambiente. Por exemplo, num ambiente de teste, pode querer monitorizar um contentor de armazenamento de BLOBs ou filas diferente.

Expressões de associação de definição de aplicação são identificadas de forma diferente de outras expressões de enlace: eles são encapsulados em percentagem, em vez de chavetas. Por exemplo, se o caminho de enlace de saída do blob for `%Environment%/newblob.txt` e o `Environment` é o valor de definição de aplicação `Development`, será criado um blob a `Development` contentor.

Quando uma função é executada localmente, valores de definição de aplicação são provenientes do *Settings* ficheiro.

Tenha em atenção que o `connection` é um caso especial de propriedade de acionadores e enlaces e resolve automaticamente os valores como definições da aplicação, sem percentagem. 

O exemplo seguinte é um acionador do armazenamento de filas do Azure que utiliza uma definição de aplicação `%input-queue-name%` para definir a fila para acionar.

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

Pode utilizar a mesma abordagem nas bibliotecas de classes:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Nome de ficheiro de Acionador

O `path` para um Blob acionador pode ser um padrão que permite que fizer referência ao nome do blob acionadora em outros enlaces e código de função. O padrão também pode incluir os critérios de filtragem que especificam qual blobs, podem acionar uma invocação de função.

Por exemplo, no acionador de Blob seguinte ligação, o `path` padrão é `sample-images/{filename}`, que cria uma expressão de vinculação com o nome `filename`:

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

A expressão `filename` , em seguida, pode ser utilizado num enlace de saída para especificar o nome do blob que está sendo criado:

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

Código de função tem acesso a este valor mesmo utilizando `filename` como um nome de parâmetro:

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

A mesma capacidade de utilizar expressões de associação e padrões de se aplica a atributos nas bibliotecas de classes. No exemplo a seguir, os parâmetros do construtor de atributo são os mesmos `path` valores como precedente *Function* exemplos: 

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

Também pode criar expressões para partes do nome do ficheiro como a extensão. Para obter mais informações sobre como utilizar expressões e padrões na cadeia de caminho de Blob, consulte a [referência de ligação de blob de armazenamento](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadados de Acionador

O payload de dados fornecido por um acionador (por exemplo, o conteúdo da mensagem de fila que uma função acionada por um), além de muitos acionadores fornecem os valores de metadados adicionais. Estes valores podem ser utilizados como parâmetros de entrada no C# e F# ou as propriedades a `context.bindings` objeto em JavaScript. 

Por exemplo, um acionador do armazenamento de filas do Azure suporta as seguintes propriedades:

* QueueTrigger - conteúdo da mensagem a acionar se uma cadeia válida
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Estes valores de metadados são acessíveis no *Function* propriedades do ficheiro. Por exemplo, suponha que usar um acionador de fila e a mensagem de fila contém o nome de um blob que pretende ler. Na *Function* arquivo, pode usar `queueTrigger` propriedade de metadados no blob `path` propriedade, conforme mostrado no exemplo a seguir:

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

Detalhes das propriedades de metadados para cada acionador são descritos no artigo de referência correspondente. Por exemplo, veja [metadados de Acionador de fila](functions-bindings-storage-queue.md#trigger---message-metadata). Documentação também está disponível na **integrar** separador do portal, na **documentação** seção a seguir a área de configuração de ligação.  

## <a name="json-payloads"></a>Conteúdo JSON

Quando um payload de Acionador é um JSON, pode consultar as respetivas propriedades na configuração para outros enlaces na mesma função e no código de função.

A exemplo a seguir mostra a *Function* ficheiro para uma função de webhook que recebe um nome de blob em JSON: `{"BlobName":"HelloWorld.txt"}`. Um enlace de entrada de BLOBs lê o blob e o HTTP enlace devolve os conteúdos do blob na resposta HTTP de saída. Tenha em atenção que o enlace de entrada do BLOBs obtém o nome de blob por consultar diretamente para o `BlobName` propriedade (`"path": "strings/{BlobName}"`)

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

Para isso funcionar C# e F#, terá de uma classe que define os campos a ser desserializado, como no exemplo seguinte:

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

No JavaScript, desserialização de JSON é executada automaticamente.

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

Se algumas das propriedades no payload JSON são objetos com propriedades, pode consultar às diretamente, usando a notação de ponto. Por exemplo, suponha que o seu JSON é semelhante a este:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Pode consultar diretamente à `FileName` como `BlobName.FileName`. Com esse formato JSON, eis o que o `path` propriedade no exemplo anterior teria o seguinte aspeto:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

No c#, precisaria duas classes:

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

O `{rand-guid}` enlace expressão cria um GUID. O seguinte caminho de blob num `function.json` arquivo cria um blob com um nome como *50710cb5-84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Hora atual

A expressão de vinculação `DateTime` é resolvido para `DateTime.UtcNow`. O seguinte caminho de blob num `function.json` arquivo cria um blob com um nome como *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Ligação em tempo de execução

No c# e de outras linguagens .NET, pode usar um padrão de enlace imperativa, em oposição as ligações declarativas na *Function* e atributos. Ligação de imperativa é útil quando os parâmetros de ligação tem de ser computado em tempo de tempo de execução, em vez de design. Para obter mais informações, consulte a [referência para programadores sobre c#](functions-dotnet-class-library.md#binding-at-runtime) ou o [referência para programadores script c#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Utilizar o valor de retorno da função do Azure](./functions-bindings-return-value.md)
