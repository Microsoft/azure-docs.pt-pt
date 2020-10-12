---
title: Funções Azure encaderna expressões e padrões
description: Aprenda a criar diferentes expressões de ligação de funções Azure Com base em padrões comuns.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: b4e2b5afd7742791218394422d00ee8ee46cb23a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212614"
---
# <a name="azure-functions-binding-expression-patterns"></a>Padrões de expressão de ligação Azure Functions

Uma das características mais poderosas dos [gatilhos e encadernações](./functions-triggers-bindings.md) são *expressões vinculativas.* No *function.jsno* ficheiro e nos parâmetros e código de função, pode utilizar expressões que se resolvem a valores de várias fontes.

A maioria das expressões são identificadas encapsulando-as em chavetas. Por exemplo, numa função de gatilho de fila, `{queueTrigger}` resolve-se o texto da mensagem de fila. Se a `path` propriedade para uma ligação de saída de bolha é `container/{queueTrigger}` e a função é desencadeada por uma mensagem de fila `HelloWorld` , uma bolha nomeada é `HelloWorld` criada.

Tipos de expressões de enlace

* [Definições da aplicação](#binding-expressions---app-settings)
* [Nome de ficheiro do acionador](#trigger-file-name)
* [Metadados do acionador](#trigger-metadata)
* [Payloads JSON](#json-payloads)
* [Novo GUID](#create-guids)
* [Data e hora atuais](#current-time)

## <a name="binding-expressions---app-settings"></a>Expressões de encadernação - configurações de aplicativos

Como uma boa prática, os segredos e as cadeias de conexão devem ser geridos usando as definições de aplicações, em vez de ficheiros de configuração. Isto limita o acesso a estes segredos e torna seguro armazenar ficheiros como *function.jsem* repositórios de controlo de fontes públicas.

As definições de aplicações também são úteis sempre que pretender alterar a configuração com base no ambiente. Por exemplo, num ambiente de teste, é melhor monitorizar uma fila ou um recipiente de armazenamento de bolhas diferentes.

As expressões de ligação à definição de aplicações são identificadas de forma diferente das outras expressões de ligação: são embrulhadas em sinais por cento em vez de aparelhos encaracolados. Por exemplo, se o caminho de ligação à saída do blob for `%Environment%/newblob.txt` e o valor de definição da `Environment` aplicação `Development` for, será criada uma bolha no `Development` recipiente.

Quando uma função está a ser executada localmente, os valores de definição de aplicação vêm do *local.settings.jsno* ficheiro.

> [!NOTE]
> A `connection` propriedade de gatilhos e encadernações é um caso especial e resolve automaticamente valores como configurações de aplicações, sem sinais por cento. 

O exemplo a seguir é um gatilho de armazenamento de fila Azure que utiliza uma definição de aplicação `%input-queue-name%` para definir a fila para ativar.

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

## <a name="trigger-file-name"></a>Nome de ficheiro do acionador

O `path` gatilho para um Blob pode ser um padrão que permite referir-se ao nome da bolha de desencadeamento em outras encadernações e código de função. O padrão também pode incluir critérios de filtragem que especificam quais as bolhas que podem desencadear uma invocação de função.

Por exemplo, na seguinte ligação ao gatilho Blob, o `path` padrão é `sample-images/{filename}` , que cria uma expressão vinculativa chamada `filename` :

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

A expressão `filename` pode então ser usada numa ligação de saída para especificar o nome da bolha que está a ser criada:

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

O código de função tem acesso a este mesmo valor utilizando `filename` como nome de parâmetro:

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

A mesma capacidade de usar expressões e padrões de ligação aplica-se a atributos nas bibliotecas de classes. No exemplo seguinte, os parâmetros do construtor de atributos são os `path` mesmos valores que os *function.jsanteriores em* exemplos: 

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

Também pode criar expressões para partes do nome do ficheiro. No exemplo seguinte, a função é desencadeada apenas em nomes de ficheiros que correspondam a um padrão: `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Para obter mais informações sobre como utilizar expressões e padrões na cadeia de caminho Blob, consulte a [referência de encadernação](functions-bindings-storage-blob.md)do blob de armazenamento .

## <a name="trigger-metadata"></a>Metadados do acionador

Além da carga útil de dados fornecida por um gatilho (como o conteúdo da mensagem de fila que desencadeou uma função), muitos gatilhos fornecem valores de metadados adicionais. Estes valores podem ser utilizados como parâmetros de entrada em C# e F# ou propriedades no `context.bindings` objeto em JavaScript. 

Por exemplo, um gatilho de armazenamento da Fila Azure suporta as seguintes propriedades:

* QueueTrigger - desencadeando o conteúdo da mensagem se uma cadeia válida
* DequeueCount
* ExpirationTime
* Id
* Inserção Tempo
* NextVisibleTime
* PopReceipt

Estes valores de metadados estão acessíveis em *function.jsnas* propriedades dos ficheiros. Por exemplo, suponha que use um gatilho de fila e a mensagem da fila contenha o nome de uma bolha que pretende ler. Na *function.jsem* ficheiro, pode utilizar `queueTrigger` a propriedade metadados na propriedade blob, como mostra o seguinte `path` exemplo:

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

Os detalhes das propriedades dos metadados para cada gatilho são descritos no artigo de referência correspondente. Por exemplo, consulte [os metadados do gatilho da fila](functions-bindings-storage-queue-trigger.md#message-metadata). A documentação também está disponível no **separador Integrado** do portal, na secção **documentação** abaixo da área de configuração de encadernação.  

## <a name="json-payloads"></a>Payloads JSON

Quando uma carga útil do gatilho é JSON, pode consultar as suas propriedades na configuração para outras encadernações na mesma função e no código de função.

O exemplo a seguir mostra a *function.jsno* ficheiro para uma função webhook que recebe um nome blob em JSON: `{"BlobName":"HelloWorld.txt"}` . Uma ligação de entrada Blob lê a bolha e a ligação de saída HTTP devolve o conteúdo blob na resposta HTTP. Note que a ligação de entrada Blob obtém o nome blob referindo-se diretamente à `BlobName` propriedade `"path": "strings/{BlobName}"` ()

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

Para que isto funcione em C# e F#, é necessário uma aula que defina os campos a serem desseeriados, como no seguinte exemplo:

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

No JavaScript, a deserialização JSON é executada automaticamente.

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

### <a name="dot-notation"></a>Notação do ponto

Se algumas das propriedades da sua carga JSON forem objetos com propriedades, pode consultar-se diretamente através da notação de pontos. Por exemplo, suponha que o seu JSON se pareça com isto:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Pode referir-se diretamente a `FileName` `BlobName.FileName` . . Com este formato JSON, aqui está o que `path` a propriedade no exemplo anterior seria:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

Em C#, precisaria de duas aulas:

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

A `{rand-guid}` expressão de ligação cria um GUID. O caminho da bolha que se segue num `function.json` ficheiro cria uma bolha com um nome como *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Tempo atual

A expressão vinculativa `DateTime` `DateTime.UtcNow` resolve-se a . O caminho da bolha que se segue num `function.json` ficheiro cria uma bolha com um nome como *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Em C# e outras línguas .NET, pode utilizar um padrão de ligação imperativo, em oposição às encadernações declarativas em *function.js* e atributos. A ligação imperativa é útil quando os parâmetros de ligação precisam de ser calculados em tempo de execução em vez de tempo de conceção. Para saber mais, consulte a referência do [desenvolvedor C#](functions-dotnet-class-library.md#binding-at-runtime) ou a referência do [desenvolvedor de scripts C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Utilizando o valor de retorno da função Azure](./functions-bindings-return-value.md)
