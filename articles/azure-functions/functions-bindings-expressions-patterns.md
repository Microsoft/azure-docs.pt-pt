---
title: Funções Azure encaderna expressões e padrões
description: Aprenda a criar diferentes funções azure expressões de ligação baseadas em padrões comuns.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485510"
---
# <a name="azure-functions-binding-expression-patterns"></a>Funções Azure padrões de expressão de ligação

Uma das características mais poderosas dos [gatilhos e encadernações](./functions-triggers-bindings.md) são as *expressões vinculativas.* No ficheiro *função.json* e nos parâmetros de função e código, pode utilizar expressões que resolvem valores de várias fontes.

A maioria das expressões são identificadas envolvendo-as em aparelhos encaracolados. Por exemplo, numa função de gatilho de fila, `{queueTrigger}` resolve-se com o texto da mensagem de fila. Se a propriedade `path` para uma ligação de saída blob for `container/{queueTrigger}` e a função for desencadeada por uma mensagem de fila `HelloWorld`, é criado um blob chamado `HelloWorld`.

Tipos de expressões vinculativas

* [Definições de aplicativos](#binding-expressions---app-settings)
* [Nome do ficheiro do gatilho](#trigger-file-name)
* [Desencadear metadados](#trigger-metadata)
* [Cargas json](#json-payloads)
* [Novo GUID](#create-guids)
* [Data e hora atuais](#current-time)

## <a name="binding-expressions---app-settings"></a>Expressões vinculativas - definições de aplicativos

Como uma melhor prática, os segredos e as cordas de ligação devem ser geridos usando as definições da aplicação, em vez de configurar ficheiros. Isto limita o acesso a estes segredos e torna seguro armazenar ficheiros como *fun.json* em repositórios de controlo de fontes públicas.

As definições da aplicação também são úteis sempre que quiser alterar a configuração com base no ambiente. Por exemplo, num ambiente de teste, é melhor monitorizar uma fila diferente ou um recipiente de armazenamento de bolhas.

As expressões de definição de aplicações são identificadas de forma diferente de outras expressões vinculativas: estão embrulhadas em sinais por cento em vez de aparelhos encaracolados. Por exemplo, se o caminho de ligação à saída da bolha for `%Environment%/newblob.txt` e o valor de definição de aplicações `Environment` for `Development`, será criada uma bolha no recipiente `Development`.

Quando uma função está a funcionar localmente, os valores de definição de aplicações vêm do ficheiro *local.settings.json.*

Note que a propriedade `connection` de gatilhos e encadernações é um caso especial e resolve automaticamente valores como configurações da app, sem sinais percentuais. 

O exemplo seguinte é um gatilho de Armazenamento de Fila Azure que usa uma definição de aplicação `%input-queue-name%` para definir a fila para ativar.

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

## <a name="trigger-file-name"></a>Nome do ficheiro do gatilho

O `path` para um gatilho Blob pode ser um padrão que permite-lhe consultar o nome da bolha de desencadeamento em outras encadernações e código de função. O padrão também pode incluir critérios de filtragem que especificam quais as bolhas que podem desencadear uma invocação de função.

Por exemplo, na seguinte ligação do gatilho blob, o padrão `path` é `sample-images/{filename}`, o que cria uma expressão vinculativa chamada `filename`:

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

A expressão `filename` pode então ser utilizada numa ligação de saída para especificar o nome da bolha que está a ser criada:

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

A mesma capacidade de usar expressões e padrões vinculativos aplica-se a atributos nas bibliotecas de classes. No exemplo seguinte, os parâmetros do construtor de atributos são os mesmos valores `path` que os exemplos da *função anterior.json:* 

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

Para obter mais informações sobre como usar expressões e padrões na cadeia do caminho Blob, consulte a [referência de ligação](functions-bindings-storage-blob.md)da bolha de armazenamento .

## <a name="trigger-metadata"></a>Desencadear metadados

Além da carga útil de dados fornecida por um gatilho (como o conteúdo da mensagem de fila que desencadeou uma função), muitos gatilhos fornecem valores adicionais de metadados. Estes valores podem ser usados C# F# como parâmetros de entrada dentro e ou propriedades no objeto `context.bindings` no JavaScript. 

Por exemplo, um gatilho de armazenamento de fila Azure suporta as seguintes propriedades:

* QueueTrigger - desencadear o conteúdo da mensagem se uma cadeia válida
* DequeueCount
* Tempo de validade
* ID
* Tempo de inserção
* NextVisibleTime
* PopReceipt

Estes valores de metadados são acessíveis nas propriedades dos *ficheiros function.json.* Por exemplo, suponha que use um gatilho de fila e a mensagem de fila contém o nome de uma bolha que quer ler. No ficheiro *função.json,* pode utilizar `queueTrigger` propriedade de metadados na propriedade blob `path`, como mostra o seguinte exemplo:

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

Os detalhes das propriedades dos metadados para cada gatilho são descritos no artigo de referência correspondente. Por exemplo, consulte [metadados](functions-bindings-storage-queue-trigger.md#message-metadata)de gatilho de fila . A documentação também está disponível no separador **Integrado** do portal, na secção **documentação** abaixo da área de configuração de encadernação.  

## <a name="json-payloads"></a>Cargas json

Quando uma carga útil do gatilho é JSON, pode consultar as suas propriedades na configuração para outras ligações na mesma função e no código de função.

O exemplo seguinte mostra o ficheiro *função.json* para uma função webhook que recebe um nome blob em JSON: `{"BlobName":"HelloWorld.txt"}`. Uma encadernação de entrada Blob lê a bolha, e a ligação de saída HTTP devolve o conteúdo da bolha na resposta HTTP. Note que a ligação de entrada Blob obtém o nome blob, referindo-se diretamente à propriedade `BlobName` (`"path": "strings/{BlobName}"`)

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

Para que isto C# F#funcione e, precisa de uma classe que defina os campos a desserializar, como no exemplo seguinte:

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

No JavaScript, a desserialização da JSON é executada automaticamente.

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

Se algumas das propriedades da sua carga útil JSON forem objetos com propriedades, pode referir-se diretamente a essas utilizando notação de pontos. Por exemplo, suponha que o seu JSON se pareça com este:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Pode consultar-se diretamente para `FileName` como `BlobName.FileName`. Com este formato JSON, aqui está o que a propriedade `path` no exemplo anterior seria:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

Dentro, C#precisaria de duas aulas:

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

A expressão `{rand-guid}` vinculativa cria um GUID. O seguinte caminho blob em um ficheiro `function.json` cria uma bolha com um nome como *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Tempo atual

A expressão vinculativa `DateTime` resolve-se a `DateTime.UtcNow`. O seguinte caminho blob em um ficheiro `function.json` cria uma bolha com um nome como *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Em C# e outras línguas .NET, você pode usar um padrão de ligação imperativo, em oposição às ligações declarativas em *função.json* e atributos. A ligação imperativa é útil quando os parâmetros de ligação precisam de ser calculados no tempo de execução em vez de tempo de conceção. Para saber mais, consulte a [ C# referência](functions-dotnet-class-library.md#binding-at-runtime) do desenvolvedor ou a referência do desenvolvedor de [ C# scripts](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Utilização do valor de retorno da Função Azure](./functions-bindings-return-value.md)
