---
title: Enlaces de armazenamento de Blobs do Azure para as funções do Azure
description: Compreenda como utilizar acionadores do armazenamento de Blobs do Azure e enlaces nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: e18a63892f000eff0f72656082d5e6e1f0ca159b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437480"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Enlaces de armazenamento de Blobs do Azure para as funções do Azure

Este artigo explica como trabalhar com ligações de armazenamento de Blobs do Azure nas funções do Azure. Funções do Azure suporta acionaram, de entrada e saída enlaces de blobs. O artigo inclui uma secção para cada ligação:

* [Acionador de blob](#trigger)
* [Enlace de entrada de BLOBs](#input)
* [Enlace de saída do blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Utilize o acionador do Event Grid em vez do acionador do armazenamento de BLOBs para contas de armazenamento apenas de BLOBs, para uma escala elevada, ou para reduzir a latência. Para obter mais informações, consulte a [acionador](#trigger) secção.

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Os enlaces de armazenamento de BLOBs são fornecidos na [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) pacote NuGet, versão 2.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Os enlaces de armazenamento de BLOBs são fornecidos na [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) pacote NuGet, versão 3.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

O acionador do armazenamento de BLOBs é iniciado uma função quando é detetado um blob de novo ou atualizado. O conteúdo de blob é fornecido como entrada para a função.

O [acionador do Event Grid](functions-bindings-event-grid.md) tem suporte incorporado para [eventos BLOBs](../storage/blobs/storage-blob-event-overview.md) e também pode ser utilizado para iniciar uma função quando é detetado um blob de novo ou atualizado. Por exemplo, veja a [imagem redimensionada com o Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) tutorial.

Utilize o Event Grid, em vez do acionador do armazenamento de BLOBs para os seguintes cenários:

* Contas do Blob Storage
* Alta escalabilidade
* Minimizando a latência

### <a name="blob-storage-accounts"></a>Contas do Blob Storage

[Contas do blob storage](../storage/common/storage-account-overview.md#types-of-storage-accounts) são suportadas para o blob de entrada e saída enlaces, mas não para acionadores de Blobs. Acionadores do armazenamento de BLOBs precisam de uma conta de armazenamento para fins gerais.

### <a name="high-scale"></a>Alta escalabilidade

Alta escalabilidade pode ser definida vagamente como contentores que tenham mais de 100 000 blobs nas mesmas ou contas de armazenamento que têm mais de 100 atualizações de blob por segundo.

### <a name="latency-issues"></a>Problemas de latência

Se a aplicação de funções no plano de consumo, pode haver até um atraso de 10 minutos no processamento novos blobs, se uma aplicação de funções tornou-se inativo. Para evitar esta latência, pode mudar para um plano do serviço de aplicações com o Always On ativado. Também pode utilizar um [acionador do Event Grid](functions-bindings-event-grid.md) com a sua conta de armazenamento de Blobs. Por exemplo, veja a [tutorial do Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Acionador do armazenamento de fila

Para além do Event Grid, outra alternativa para o processamento de blobs é o acionador do armazenamento de fila, mas ele não possui suporte incorporado para eventos de Blobs. Terá de criar a fila de mensagens quando criar ou atualizar blobs. Para obter um exemplo que supõe que fazer isso, consulte a [exemplo de enlace de entrada de BLOBs neste artigo](#input---example).

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de idioma específico:

* [C#](#trigger---c-example)
* [Script do c# (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Acionador - exemplo do c#

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que registra um log quando um blob é adicionado ou atualizado no `samples-workitems` contentor.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia de caracteres `{name}` no caminho de Acionador de blob `samples-workitems/{name}` cria um [enlace expressão](./functions-bindings-expressions-patterns.md) que pode usar no código de função para acessar o nome de ficheiro do blob acionadora. Para obter mais informações, consulte [padrões de nome de Blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre o `BlobTrigger` de atributos, consulte [acionador - atributos](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo seguinte mostra um acionador de blob de enlace num *Function* ficheiro e [código de Python](functions-reference-python.md) que utiliza o enlace. A função registra um log quando um blob é adicionado ou atualizado na `samples-workitems` [contentor](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Eis a vinculação de dados a *Function* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho de Acionador de blob `samples-workitems/{name}` cria um [enlace expressão](./functions-bindings-expressions-patterns.md) que pode usar no código de função para acessar o nome de ficheiro do blob acionadora. Para obter mais informações, consulte [padrões de nome de Blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre *Function* propriedades do ficheiro, consulte a [configuração](#trigger---configuration) seção explica essas propriedades.

Aqui está o script código c# que liga um `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está o script código c# que liga um `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo seguinte mostra um acionador de blob de enlace num *Function* ficheiro e [código JavaScript](functions-reference-node.md) que utiliza o enlace. A função registra um log quando um blob é adicionado ou atualizado no `samples-workitems` contentor.

Aqui está o *Function* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho de Acionador de blob `samples-workitems/{name}` cria um [enlace expressão](./functions-bindings-expressions-patterns.md) que pode usar no código de função para acessar o nome de ficheiro do blob acionadora. Para obter mais informações, consulte [padrões de nome de Blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre *Function* propriedades do ficheiro, consulte a [configuração](#trigger---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Acionador - exemplo de Python

O exemplo seguinte mostra um acionador de blob de enlace num *Function* ficheiro e [código de Python](functions-reference-python.md) que utiliza o enlace. A função registra um log quando um blob é adicionado ou atualizado na `samples-workitems` [contentor](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Aqui está o *Function* ficheiro:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho de Acionador de blob `samples-workitems/{name}` cria um [enlace expressão](./functions-bindings-expressions-patterns.md) que pode usar no código de função para acessar o nome de ficheiro do blob acionadora. Para obter mais informações, consulte [padrões de nome de Blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre *Function* propriedades do ficheiro, consulte a [configuração](#trigger---configuration) seção explica essas propriedades.

Aqui está o código de Python:

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Acionador - exemplo de Java

O exemplo seguinte mostra um acionador de blob de enlace num *Function* ficheiro e [código Java](functions-reference-java.md) que utiliza o enlace. A função registra um log quando um blob é adicionado ou atualizado no `myblob` contentor.

Aqui está o *Function* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Eis o código Java:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Acionador - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize os seguintes atributos para configurar um acionador de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Construtor do atributo precisa de uma cadeia de caminho que indica o contentor para ver e, opcionalmente, um [padrão de nome de blob](#trigger---blob-name-patterns). Segue-se um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo a seguir:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para obter um exemplo completo, consulte [acionador - exemplo do c#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Proporciona outra forma de especificar a conta de armazenamento a utilizar. O construtor obtém o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

A conta de armazenamento a utilizar é determinada pela seguinte ordem:

* O `BlobTrigger` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `BlobTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` aplicado à classe de atributo.
* A conta de armazenamento predefinida para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `BlobTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `blobTrigger`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `in`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. Exceções estão apontadas na [utilização](#trigger---usage) secção. |
|**name** | n/d | O nome da variável que representa o blob no código de função. |
|**path** | **BlobPath** |O [contentor](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) para monitorizar.  Pode ser um [padrão de nome de blob](#trigger---blob-name-patterns). |
|**ligação** | **ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para essa ligação. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação com o nome `AzureWebJobsStorage`.<br><br>A cadeia de ligação tem de ser para uma conta de armazenamento para fins gerais, não uma [conta de armazenamento de BLOBs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

No c# e no c# script, pode utilizar os seguintes tipos de parâmetro para o blob de acionamento:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Um POCO serializável como JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requer "inout" enlace `direction` na *Function* ou `FileAccess.ReadWrite` numa biblioteca de classes do c#.

Se tentar ligar a um dos tipos de SDK de armazenamento e receber uma mensagem de erro, certifique-se de que tem uma referência a [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Fazendo a ligação `string`, `Byte[]`, ou POCO só é recomendada se o tamanho do blob for pequeno, como o blob de todo o conteúdo é carregado na memória. Geralmente, é preferível utilizar um `Stream` ou `CloudBlockBlob` tipo. Para obter mais informações, consulte [utilização da memória e simultaneidade](#trigger---concurrency-and-memory-usage) mais adiante neste artigo.

No JavaScript, acessar os dados de BLOBs de entrada usando `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Acionador - padrões de nome de blob

Pode especificar um padrão de nome de blob na `path` propriedade na *Function* ou no `BlobTrigger` construtor de atributo. O padrão de nome pode ser um [expressão de filtro ou enlace](./functions-bindings-expressions-patterns.md). As secções seguintes fornecem exemplos.

### <a name="get-file-name-and-extension"></a>Obter o nome de ficheiro e extensão

O exemplo seguinte mostra como vincular separadamente para o nome de ficheiro de BLOBs e a extensão:

```json
"path": "input/{blobname}.{blobextension}",
```
Se o blob com o nome *original Blob1.txt*, os valores da `blobname` e `blobextension` variáveis no código de função são *original Blob1* e *txt*.

### <a name="filter-on-blob-name"></a>Filtrar no nome do blob

Os acionadores de exemplo seguintes apenas nos blobs do `input` contentor que começa com a cadeia de caracteres "original-":

```json
"path": "input/original-{name}",
```

Se o nome do blob é *original Blob1.txt*, o valor da `name` variável no código de função é `Blob1`.

### <a name="filter-on-file-type"></a>Filtre por tipo de ficheiro

O exemplo seguinte apenas no aciona *. PNG* ficheiros:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtre por chavetas em nomes de ficheiros

Para procurar por chavetas em nomes de ficheiros, de escape as chavetas utilizando duas chaves. Os seguintes filtros de exemplo para blobs que têm chaves no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se o blob com o nome  *{20140101}-soundfile.mp3*, o `name` é o valor da variável no código de função *soundfile.mp3*.

## <a name="trigger---metadata"></a>Acionador - metadados

O acionador de blob fornece várias propriedades de metadados. Essas propriedades podem ser utilizadas como parte das expressões de associação nas outras associações, ou como parâmetros no seu código. Estes valores têm a mesma semântica de como o [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) tipo.

|Propriedade  |Tipo  |Descrição  |
|---------|---------|---------|
|`BlobTrigger`|`string`|O caminho para o blob acionadora.|
|`Uri`|`System.Uri`|O blob URI para a localização principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Propriedades do sistema do blob. |
|`Metadata` |`IDictionary<string,string>`|Os metadados definidos pelo utilizador para o blob.|

Por exemplo, o seguinte script do c# e exemplos de JavaScript registo o caminho para o blob acionadora, incluindo o contentor:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Acionador - recibos de blob

O runtime das funções do Azure garante que nenhuma função de Acionador de blob é chamada de mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de determinado blob foi processada, ela mantém *recibos de BLOBs*.

Arquivos de funções do Azure de BLOBs num contentor com o nome do Editor *anfitriões de webjobs do azure* na conta de armazenamento do Azure para a sua aplicação de função (definido pela definição da aplicação `AzureWebJobsStorage`). Um recibo de blob tem as seguintes informações:

* A função acionada ("*&lt;nome da aplicação >*. Funções.  *&lt;nome da função >*", por exemplo: "MyFunctionApp.Functions.CopyBlob")
* O nome do contentor
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, elimine o recebimento de BLOBs para esse blob a partir da *anfitriões de webjobs do azure* contentor manualmente.

## <a name="trigger---poison-blobs"></a>Acionador - blobs não processáveis

Quando uma função de Acionador de blob falha para um determinado blob, tentará novamente as funções do Azure que funcionam um total de 5 vezes por predefinição.

Se todas as tentativas de 5 falharem, as funções do Azure adiciona uma mensagem numa fila de armazenamento com o nome *webjobs-blobtrigger-veneno*. A mensagem de fila para blobs não processáveis é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato  *&lt;nome da aplicação >*. Funções.  *&lt;nome da função >*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Acionador - a utilização de memória e simultaneidade

O acionador de blob utiliza uma fila internamente, para que o número máximo de invocações de função simultâneos é controlado pelos [configuração de filas no Host. JSON](functions-host-json.md#queues). As predefinições de limitam de simultaneidade para invocações de 24. Este limite aplica-se em separado para cada função que utiliza um acionador de blob.

[O plano de consumo](functions-scale.md#how-the-consumption-plan-works) limita uma aplicação de funções numa máquina virtual (VM) a 1,5 GB de memória. A memória é utilizada por cada instância de função em execução em simultâneo e o runtime das funções em si. Se uma função acionada por blob carrega o blob inteiro na memória, o máximo de memória utilizado por essa função apenas para blobs é 24 * tamanho máximo de Blobs. Por exemplo, uma aplicação de funções com três funções acionadas por BLOBs e as configurações padrão seria ter uma simultaneidade de máximo por VM, de 3 * 24 = 72 invocações de função.

As funções JavaScript e Java carregar o blob inteiro na memória, e C# funções de fazer isso, se ligar `string`, `Byte[]`, ou POCO.

## <a name="trigger---polling"></a>Acionar - consulta

Se o contentor de BLOBs a ser monitorizado contém mais de 10 000 blobs (em todos os contentores), as verificações de tempo de execução de funções ficheiros para deteção de blobs novas ou alteradas de registo. Este processo pode resultar em atrasos. Uma função pode não obter acionada por até vários minutos ou mais depois de criar o blob. Além disso, [registos de armazenamento são criados num "melhor esforço"](/rest/api/storageservices/About-Storage-Analytics-Logging) base. Não há nenhuma garantia que todos os eventos são capturados. Em algumas condições, os registos podem ser perdidos.

Se necessitar de processamento de blob mais rápida ou mais fiáveis, considere criar um [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) quando cria o blob. Em seguida, utilizar um [acionador de fila](functions-bindings-storage-queue.md) em vez de um acionador de blob para processar o blob. Outra opção consiste em utilizar o Event Grid; Veja o tutorial [automatizar o redimensionamento de imagens com o Event Grid carregadas](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Input

Utilize um enlace de entrada de armazenamento de BLOBs para ler blobs.

## <a name="input---example"></a>Entrada - exemplo

Veja o exemplo de idioma específico:

* [C#](#input---c-example)
* [Script do c# (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Introdução - exemplo do c#

O exemplo seguinte é um [função c#](functions-dotnet-class-library.md) que utiliza um acionador de fila e um enlace de blob de entrada. A mensagem de fila contém o nome do blob e a função registra o tamanho do blob.

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

### <a name="input---c-script-example"></a>Introdução - exemplo de script do c#

<!--Same example for input and output. -->

O exemplo seguinte mostra o blob de entrada e saída de ligações num *Function* ficheiro e [script c# (. csx)](functions-reference-csharp.md) código que utiliza as ligações. A função faz uma cópia de um blob de texto. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O blob novo com o nome *{originalblobname}-cópia*.

Na *Function* arquivo, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

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

O [configuração](#input---configuration) seção explica essas propriedades.

Aqui está o código de script do c#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Introdução - exemplo de JavaScript

<!--Same example for input and output. -->

O exemplo seguinte mostra o blob de entrada e saída de ligações num *Function* ficheiro e [código JavaScript](functions-reference-node.md) que utiliza as ligações. A função faz uma cópia de um blob. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O blob novo com o nome *{originalblobname}-cópia*.

Na *Function* arquivo, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

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

O [configuração](#input---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---python-example"></a>Introdução - exemplo de Python

<!--Same example for input and output. -->

O exemplo seguinte mostra o blob de entrada e saída de ligações num *Function* ficheiro e [código de Python](functions-reference-python.md) que utiliza as ligações. A função faz uma cópia de um blob. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O blob novo com o nome *{originalblobname}-cópia*.

Na *Function* arquivo, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

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

O [configuração](#input---configuration) seção explica essas propriedades.

Aqui está o código de Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Introdução - exemplos de Java

Esta secção contém os exemplos seguintes:

* [Acionador HTTP, pesquisa de nome de blob de cadeia de caracteres de consulta](#http-trigger-look-up-blob-name-from-query-string-java)
* [Acionador da fila, receber o nome do blob da mensagem de fila](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>Acionador HTTP, pesquisa de nome do blob da cadeia de consulta (Java)

 O exemplo seguinte mostra uma função de Java que usa o ```HttpTrigger``` anotação para receber um parâmetro que contém o nome de um arquivo num contentor de armazenamento de Blobs. O ```BlobInput``` anotação, em seguida, lê o arquivo e passa o respetivo conteúdo para a função como um ```byte[]```.

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Acionador da fila, receber o nome do blob da mensagem de fila (Java)

 O exemplo seguinte mostra uma função de Java que usa o ```QueueTrigger``` anotação para receber uma mensagem que contém o nome de um arquivo num contentor de armazenamento de Blobs. O ```BlobInput``` anotação, em seguida, lê o arquivo e passa o respetivo conteúdo para a função como um ```byte[]```.

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

Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@BlobInput` anotação em parâmetros cujo valor deve ser proveniente de um blob.  Esta anotação pode ser utilizada com tipos nativos de Java, POJOs ou valores anuláveis usando `Optional<T>`.

## <a name="input---attributes"></a>Introdução - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Construtor do atributo aceita o caminho para o blob e um `FileAccess` parâmetro que indica de leitura ou escrita, conforme mostrado no exemplo a seguir:

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

Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo a seguir:

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

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento ao nível de classe, método ou parâmetro. Para obter mais informações, consulte [acionador - atributos](#trigger---attributes).

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Blob` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `blob`. |
|**direção** | n/d | Tem de ser definido como `in`. Exceções estão apontadas na [utilização](#input---usage) secção. |
|**name** | n/d | O nome da variável que representa o blob no código de função.|
|**path** |**BlobPath** | O caminho para o blob. |
|**ligação** |**ligação**| O nome de uma definição de aplicação que contém o [cadeia de ligação de armazenamento](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account) a utilizar para essa ligação. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação com o nome `AzureWebJobsStorage`.<br><br>A cadeia de ligação tem de ser para uma conta de armazenamento para fins gerais, não uma [conta de armazenamento apenas de BLOBs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Acesso** | Indica se será possível ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

No c# e no c# script, pode utilizar os seguintes tipos de parâmetro para o enlace de entrada de BLOBs:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requer "inout" enlace `direction` na *Function* ou `FileAccess.ReadWrite` numa biblioteca de classes do c#.

Se tentar ligar a um dos tipos de SDK de armazenamento e receber uma mensagem de erro, certifique-se de que tem uma referência a [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Fazendo a ligação `string` ou `Byte[]` só é recomendada se o tamanho do blob é pequeno, como o conteúdo de blob inteiro é carregado na memória. Geralmente, é preferível utilizar um `Stream` ou `CloudBlockBlob` tipo. Para obter mais informações, consulte [utilização da memória e simultaneidade](#trigger---concurrency-and-memory-usage) no início deste artigo.

No JavaScript, acessar os dados de Blobs com `context.bindings.<name from function.json>`.

## <a name="output"></a>Saída

Utilize enlaces de saída de armazenamento de BLOBs para escrever blobs.

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo de idioma específico:

* [C#](#output---c-example)
* [Script do c# (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Saída - exemplo do c#

O exemplo seguinte é um [função c#](functions-dotnet-class-library.md) que utiliza um acionador de blob e dois enlaces de BLOBs de saída. A função é acionada pela criação de um blob de imagem no *imagens de exemplo* contentor. Cria cópias de tamanho de pequeno e médio do blob de imagem.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

<!--Same example for input and output. -->

O exemplo seguinte mostra o blob de entrada e saída de ligações num *Function* ficheiro e [script c# (. csx)](functions-reference-csharp.md) código que utiliza as ligações. A função faz uma cópia de um blob de texto. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O blob novo com o nome *{originalblobname}-cópia*.

Na *Function* arquivo, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

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

O [configuração](#output---configuration) seção explica essas propriedades.

Aqui está o código de script do c#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

<!--Same example for input and output. -->

O exemplo seguinte mostra o blob de entrada e saída de ligações num *Function* ficheiro e [código JavaScript](functions-reference-node.md) que utiliza as ligações. A função faz uma cópia de um blob. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O blob novo com o nome *{originalblobname}-cópia*.

Na *Function* arquivo, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

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

O [configuração](#output---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---python-example"></a>Saída - exemplo de Python

<!--Same example for input and output. -->

O exemplo seguinte mostra o blob de entrada e saída de ligações num *Function* ficheiro e [código de Python](functions-reference-python.md) que utiliza as ligações. A função faz uma cópia de um blob. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O blob novo com o nome *{originalblobname}-cópia*.

Na *Function* arquivo, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

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

O [configuração](#output---configuration) seção explica essas propriedades.

Aqui está o código de Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Saída - exemplos de Java

Esta secção contém os exemplos seguintes:

* [Acionador HTTP, usando OutputBinding](#http-trigger-using-outputbinding-java)
* [Acionador de fila, com o valor de retorno da função](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Acionador HTTP, usando OutputBinding (Java)

 O exemplo seguinte mostra uma função de Java que usa o ```HttpTrigger``` anotação para receber um parâmetro que contém o nome de um arquivo num contentor de armazenamento de Blobs. O ```BlobInput``` anotação, em seguida, lê o arquivo e passa o respetivo conteúdo para a função como um ```byte[]```. O ```BlobOutput``` anotação vincula a ```OutputBinding outputItem```, em seguida, que é utilizado pela função para escrever o conteúdo do blob de entrada para o contentor de armazenamento configurados.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Acionador de fila, com o valor de retorno de função (Java)

 O exemplo seguinte mostra uma função de Java que usa o ```QueueTrigger``` anotação para receber uma mensagem que contém o nome de um arquivo num contentor de armazenamento de Blobs. O ```BlobInput``` anotação, em seguida, lê o arquivo e passa o respetivo conteúdo para a função como um ```byte[]```. O ```BlobOutput``` anotação liga para o valor de retorno de função, que, em seguida, é utilizado pelo tempo de execução para escrever o conteúdo do blob de entrada para o contentor de armazenamento configurados.

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

 Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@BlobOutput` anotação nos parâmetros de função, seria escrito cujo valor a um objeto no armazenamento de Blobs.  O tipo de parâmetro deve ser `OutputBinding<T>`, em que T é qualquer tipo de Java nativo ou um POJO.

## <a name="output---attributes"></a>Saída - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Construtor do atributo aceita o caminho para o blob e um `FileAccess` parâmetro que indica de leitura ou escrita, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída - exemplo do c#](#output---c-example).

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento ao nível de classe, método ou parâmetro. Para obter mais informações, consulte [acionador - atributos](#trigger---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Blob` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `blob`. |
|**direção** | n/d | Tem de ser definido como `out` para um enlace de saída. Exceções estão apontadas na [utilização](#output---usage) secção. |
|**name** | n/d | O nome da variável que representa o blob no código de função.  Definido como `$return` para referenciar o valor de retorno da função.|
|**path** |**BlobPath** | O caminho para o blobco. |
|**ligação** |**ligação**| O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para essa ligação. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação com o nome `AzureWebJobsStorage`.<br><br>A cadeia de ligação tem de ser para uma conta de armazenamento para fins gerais, não uma [conta de armazenamento apenas de BLOBs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Acesso** | Indica se será possível ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

Em c# e de script c#, é possível ligar para os seguintes tipos de blobs de escrever:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> requer "em" enlace `direction` na *Function* ou `FileAccess.Read` numa biblioteca de classes do c#. No entanto, pode usar o objeto de contentor que o tempo de execução fornece para operações de escrita, como o carregamento de blobs para o contentor.

<sup>2</sup> requer "inout" enlace `direction` na *Function* ou `FileAccess.ReadWrite` numa biblioteca de classes do c#.

Se tentar ligar a um dos tipos de SDK de armazenamento e receber uma mensagem de erro, certifique-se de que tem uma referência a [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Nas funções assíncronas, utilize o valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

Fazendo a ligação `string` ou `Byte[]` só é recomendada se o tamanho do blob é pequeno, como o conteúdo de blob inteiro é carregado na memória. Geralmente, é preferível utilizar um `Stream` ou `CloudBlockBlob` tipo. Para obter mais informações, consulte [utilização da memória e simultaneidade](#trigger---concurrency-and-memory-usage) no início deste artigo.


No JavaScript, acessar os dados de Blobs com `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo |  Referência |
|---|---|
| Blobs | [Códigos de erro de blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabela, fila |  [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabela, fila |  [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
