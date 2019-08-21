---
title: Associações de armazenamento de BLOBs do Azure para Azure Functions
description: Entenda como usar gatilhos e associações do armazenamento de BLOBs do Azure no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 428334c1d1d28f53f3a4007b345bb2fb2249f26b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642217"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Associações de armazenamento de BLOBs do Azure para Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de BLOBs do Azure no Azure Functions. Azure Functions dá suporte a associações de gatilho, entrada e saída para BLOBs. O artigo inclui uma seção para cada associação:

* [Gatilho de BLOB](#trigger)
* [Associação de entrada de BLOB](#input)
* [Associação de saída de BLOB](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Use o gatilho de grade de eventos em vez do gatilho de armazenamento de BLOBs para contas de armazenamento somente BLOB, para alta escala ou para reduzir a latência. Para obter mais informações, consulte a seção [gatilho](#trigger) .

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações de armazenamento de BLOBs são fornecidas no pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

As associações de armazenamento de BLOBs são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , versão 3. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

O gatilho do armazenamento de BLOBs inicia uma função quando um blob novo ou atualizado é detectado. O conteúdo do blob é fornecido como entrada para a função.

O [gatilho de grade de eventos](functions-bindings-event-grid.md) tem suporte interno para [eventos de blob](../storage/blobs/storage-blob-event-overview.md) e também pode ser usado para iniciar uma função quando um blob novo ou atualizado é detectado. Para obter um exemplo, consulte o tutorial [redimensionar imagem com a grade de eventos](../event-grid/resize-images-on-storage-blob-upload-event.md) .

Use a grade de eventos em vez do gatilho do armazenamento de BLOBs para os seguintes cenários:

* Contas do Blob Storage
* Alta escala
* Minimizando a latência

### <a name="blob-storage-accounts"></a>Contas do Blob Storage

[As contas de armazenamento](../storage/common/storage-account-overview.md#types-of-storage-accounts) de BLOBs têm suporte para associações de entrada e saída de BLOB, mas não para gatilhos de BLOB. Os gatilhos do armazenamento de BLOBs exigem uma conta de armazenamento de uso geral.

### <a name="high-scale"></a>Alta escala

A alta escala pode ser definida livremente como contêineres que têm mais de 100.000 BLOBs ou contas de armazenamento que têm mais de 100 atualizações de blob por segundo.

### <a name="latency-issues"></a>Problemas de latência

Se seu aplicativo de funções estiver no plano de consumo, poderá haver um atraso de até 10 minutos no processamento de novos BLOBs se um aplicativo de funções ficar ocioso. Para evitar essa latência, você pode alternar para um plano do serviço de aplicativo com Always On habilitado. Você também pode usar um [gatilho de grade de eventos](functions-bindings-event-grid.md) com sua conta de armazenamento de BLOBs. Para obter um exemplo, consulte o [tutorial de grade de eventos](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Gatilho de armazenamento de filas

Além da grade de eventos, outra alternativa para o processamento de BLOBs é o gatilho de armazenamento de filas, mas não tem suporte interno para eventos de BLOB. Você precisaria criar mensagens da fila ao criar ou atualizar BLOBs. Para obter um exemplo que supõe que você tenha feito isso, consulte o [exemplo de associação de entrada de blob mais adiante neste artigo](#input---example).

## <a name="trigger---example"></a>Acionador - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que grava um log quando um blob é adicionado ou `samples-workitems` atualizado no contêiner.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia `{name}` de caracteres no caminho `samples-workitems/{name}` do gatilho de blob cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome do arquivo do blob de gatilho. Para obter mais informações, consulte [padrões de nome de blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre `BlobTrigger` o atributo, consulte [gatilho-atributos](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de BLOB em um arquivo *Function. JSON* e um código que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

A cadeia `{name}` de caracteres no caminho `samples-workitems/{name}` do gatilho de blob cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome do arquivo do blob de gatilho. Para obter mais informações, consulte [padrões de nome de blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre as propriedades de arquivo *Function. JSON* , consulte a seção de [configuração](#trigger---configuration) explica essas propriedades.

Aqui está C# o código de script que se associa `Stream`a:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está C# o código de script que se associa `CloudBlockBlob`a:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de BLOB em um arquivo *Function. JSON* e [código JavaScript](functions-reference-node.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` contêiner.

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

A cadeia `{name}` de caracteres no caminho `samples-workitems/{name}` do gatilho de blob cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome do arquivo do blob de gatilho. Para obter mais informações, consulte [padrões de nome de blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre as propriedades de arquivo *Function. JSON* , consulte a seção de [configuração](#trigger---configuration) explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho de BLOB em um arquivo *Function. JSON* e [código Python](functions-reference-python.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

A cadeia `{name}` de caracteres no caminho `samples-workitems/{name}` do gatilho de blob cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome do arquivo do blob de gatilho. Para obter mais informações, consulte [padrões de nome de blob](#trigger---blob-name-patterns) mais adiante neste artigo.

Para obter mais informações sobre as propriedades de arquivo *Function. JSON* , consulte a seção de [configuração](#trigger---configuration) explica essas propriedades.

Este é o código Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo a seguir mostra uma associação de gatilho de BLOB em um arquivo *Function. JSON* e [código Java](functions-reference-java.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `myblob` contêiner.

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

---

## <a name="trigger---attributes"></a>Acionador - atributos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  O construtor do atributo usa uma cadeia de caracteres de caminho que indica o contêiner a ser inspecionado e, opcionalmente, um [padrão de nome de blob](#trigger---blob-name-patterns). Segue-se um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para obter um exemplo completo, consulte [exemplo de gatilho](#trigger---example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra maneira de especificar a conta de armazenamento a ser usada. O construtor usa o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

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

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* O `BlobTrigger` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `BlobTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` aplicado à classe de atributo.
* A conta de armazenamento padrão para o aplicativo de funções (configuração de aplicativo "AzureWebJobsStorage").

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para fornecer acesso ao blob que disparou a função. Consulte o [exemplo de gatilho](#trigger---example) para obter detalhes.

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `BlobTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Tem de ser definido como `blobTrigger`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `in`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. As exceções são indicadas na seção [uso](#trigger---usage) . |
|**name** | n/d | O nome da variável que representa o blob no código de função. |
|**path** | **BlobPath** |O [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) a ser monitorado.  Pode ser um [padrão de nome de blob](#trigger---blob-name-patterns). |
|**ligação** | **ligação** | O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyStorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão `AzureWebJobsStorage`na configuração do aplicativo que é nomeada.<br><br>A cadeia de conexão deve ser para uma conta de armazenamento de uso geral, não para uma [conta de armazenamento](../storage/common/storage-account-overview.md#types-of-storage-accounts)de BLOBs.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Acessar dados de blob `context.bindings.<name from function.json>`usando.

# <a name="pythontabpython"></a>[Python](#tab/python)

Acessar dados de blob por meio do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o [exemplo de gatilho](#trigger---example) para obter detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para fornecer acesso ao blob que disparou a função. Consulte o [exemplo de gatilho](#trigger---example) para obter detalhes.

---

## <a name="trigger---blob-name-patterns"></a>Gatilho-padrões de nome de BLOB

Você pode especificar um padrão de nome de blob `path` na propriedade em *Function. JSON* ou no `BlobTrigger` Construtor de atributo. O padrão de nome pode ser uma [expressão de filtro ou de associação](./functions-bindings-expressions-patterns.md). As seções a seguir fornecem exemplos.

### <a name="get-file-name-and-extension"></a>Obter o nome e a extensão do arquivo

O exemplo a seguir mostra como associar o nome e a extensão do arquivo de blob separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```

Se o blob for nomeado *original-blob1. txt*, os valores das `blobname` variáveis e `blobextension` no código de função serão *original-Blob1* e *txt*.

### <a name="filter-on-blob-name"></a>Filtrar no nome do blob

O exemplo a seguir dispara somente em BLOBs no `input` contêiner que começam com a cadeia de caracteres "original-":

```json
"path": "input/original-{name}",
```

Se o nome do blob for *original-blob1. txt*, o valor da `name` variável no código de função `Blob1`será.

### <a name="filter-on-file-type"></a>Filtrar por tipo de arquivo

O exemplo a seguir dispara apenas em arquivos *. png* :

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrar chaves em nomes de arquivo

Para procurar chaves em nomes de arquivo, escape as chaves usando duas chaves. O exemplo a seguir filtra os blobs que têm chaves no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se o blob for nomeado  *{20140101}-soundfile. mp3*, o `name` valor da variável no código de função será *soundfile. mp3*.

## <a name="trigger---metadata"></a>Gatilho-metadados

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Os metadados não estão disponíveis no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Os metadados não estão disponíveis em Java.

---

## <a name="trigger---blob-receipts"></a>Gatilho-recebimentos de BLOB

O tempo de execução de Azure Functions garante que nenhuma função de gatilho de blob é chamada mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma determinada versão do blob foi processada, ela mantém os recebimentos de *blob*.

Azure Functions armazena recibos de BLOB em um contêiner chamado *Azure-webjobs-hosts* na conta de armazenamento do Azure para seu aplicativo de funções (definido `AzureWebJobsStorage`pela configuração do aplicativo). Um recebimento de blob tem as seguintes informações:

* A função disparada (" *&lt;nome do aplicativo de funções >* . Funções. nome da função > ", por exemplo:  *&lt;* "MyFunctionApp.Functions.CopyBlob")
* O nome do contêiner
* O tipo de BLOB ("BlockBlob" ou "PageBlob")
* O nome do blob
* A ETag (um identificador de versão de BLOB, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, exclua o recebimento de BLOB para esse blob do contêiner *Azure-webjobs-hosts* manualmente. Embora o reprocessamento possa não ocorrer imediatamente, é garantido que ocorra em um momento posterior.

## <a name="trigger---poison-blobs"></a>Gatilho-BLOBs inviabilizados

Quando uma função de gatilho de blob falha para um determinado BLOB, Azure Functions repete a função um total de cinco vezes por padrão.

Se todas as cinco tentativas falharem, Azure Functions adicionará uma mensagem a uma fila de armazenamento chamada *webjobs-blobtrigger-suspeita*. A mensagem da fila para BLOBs inviabilizados é um objeto JSON que contém as seguintes propriedades:

* FunctionID (no nome do  *&lt;aplicativo de funções*de formato >. Funções. nome da função >)  *&lt;*
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de BLOB, por exemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Gatilho-uso de memória e simultaneidade

O gatilho de blob usa uma fila internamente, portanto, o número máximo de invocações de função simultâneas é controlado pela [configuração de filas em host. JSON](functions-host-json.md#queues). As configurações padrão limitam a simultaneidade a 24 invocações. Esse limite se aplica separadamente a cada função que usa um gatilho de BLOB.

[O plano de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita um aplicativo de funções em uma VM (máquina virtual) a 1,5 GB de memória. A memória é usada por cada instância de função executando simultaneamente e pelo próprio tempo de execução do functions. Se uma função disparada por blob carregar o blob inteiro na memória, a memória máxima usada por essa função apenas para BLOBs será 24 * tamanho máximo do blob. Por exemplo, um aplicativo de funções com três funções disparadas por blob e as configurações padrão teria uma simultaneidade máxima por VM de 3 * 24 = 72 invocações de função.

As funções JavaScript e Java carregam todo o blob na memória C# , e as funções fazem isso se `string`você `Byte[]`associar a, ou poco.

## <a name="trigger---polling"></a>Gatilho-sondagem

Se o contêiner de BLOB que está sendo monitorado contiver mais de 10.000 BLOBs (em todos os contêineres), o tempo de execução do Functions examinará os arquivos de log para ver se há BLOBs novos ou alterados. Esse processo pode resultar em atrasos. Uma função pode não ser disparada até vários minutos ou mais depois que o blob for criado.

> [!WARNING]
> Além disso, [os logs de armazenamento são criados com base em uma "melhor iniciativa"](/rest/api/storageservices/About-Storage-Analytics-Logging) . Não há nenhuma garantia de que todos os eventos sejam capturados. Em algumas condições, os logs podem ser perdidos.
> 
> Se você precisar de processamento de blob mais rápido ou confiável, considere criar uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, use um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de BLOB para processar o blob. Outra opção é usar a grade de eventos; consulte o tutorial automatizar o redimensionamento de [imagens carregadas usando a grade de eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Input

Use uma associação de entrada de armazenamento de BLOBs para ler BLOBs.

## <a name="input---example"></a>Entrada-exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir é uma [ C# função](functions-dotnet-class-library.md) que usa um gatilho de fila e uma associação de blob de entrada. A mensagem da fila contém o nome do blob e a função registra o tamanho do blob.

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

<!--Same example for input and output. -->

O exemplo a seguir mostra associações de entrada e saída de BLOB em um arquivo *Function. JSON* e [ C# um código de script (. CSX)](functions-reference-csharp.md) que usa as associações. A função faz uma cópia de um blob de texto. A função é disparada por uma mensagem da fila que contém o nome do blob a ser copiado. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *Function. JSON* , a `queueTrigger` Propriedade Metadata é usada para especificar o nome do `path` blob nas propriedades:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

O exemplo a seguir mostra associações de entrada e saída de BLOB em um arquivo *Function. JSON* e [código JavaScript](functions-reference-node.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob a ser copiado. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *Function. JSON* , a `queueTrigger` Propriedade Metadata é usada para especificar o nome do `path` blob nas propriedades:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

O exemplo a seguir mostra associações de entrada e saída de BLOB em um arquivo *Function. JSON* e [código Python](functions-reference-python.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob a ser copiado. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *Function. JSON* , a `queueTrigger` Propriedade Metadata é usada para especificar o nome do `path` blob nas propriedades:

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

Este é o código Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta secção contém os exemplos seguintes:

* [Gatilho HTTP, pesquisar o nome do blob da cadeia de caracteres de consulta](#http-trigger-look-up-blob-name-from-query-string)
* [Gatilho de fila, receber nome de BLOB da mensagem da fila](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Gatilho HTTP, pesquisar o nome do blob da cadeia de caracteres de consulta

 O exemplo a seguir mostra uma função Java que usa `HttpTrigger` a anotação para receber um parâmetro que contém o nome de um arquivo em um contêiner de armazenamento de BLOBs. Em `BlobInput` seguida, a anotação lê o arquivo e passa seu conteúdo para a função `byte[]`como um.

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Gatilho de fila, receber nome de BLOB da mensagem da fila

 O exemplo a seguir mostra uma função Java que usa `QueueTrigger` a anotação para receber uma mensagem que contém o nome de um arquivo em um contêiner de armazenamento de BLOBs. Em `BlobInput` seguida, a anotação lê o arquivo e passa seu conteúdo para a função `byte[]`como um.

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

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a `@BlobInput` anotação nos parâmetros cujo valor seria proveniente de um blob.  Essa anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando `Optional<T>`.

---

## <a name="input---attributes"></a>Introdução - atributos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use [](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)o blobattribute.

O construtor do atributo usa o caminho para o blob e um `FileAccess` parâmetro que indica leitura ou gravação, conforme mostrado no exemplo a seguir:

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

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

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

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [acionador - atributos](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O `@BlobInput` atributo fornece acesso ao blob que disparou a função. Se você usar uma matriz de bytes com o atributo, `dataType` defina `binary`como. Consulte o [exemplo de entrada](#input---example) para obter detalhes.

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Blob` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Tem de ser definido como `blob`. |
|**direction** | n/d | Tem de ser definido como `in`. As exceções são indicadas na seção [uso](#input---usage) . |
|**name** | n/d | O nome da variável que representa o blob no código de função.|
|**path** |**BlobPath** | O caminho para o blob. |
|**ligação** |**ligação**| O nome de uma configuração de aplicativo que contém a [cadeia de conexão de armazenamento](../storage/common/storage-configure-connection-string.md) a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyStorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão `AzureWebJobsStorage`na configuração do aplicativo que é nomeada.<br><br>A cadeia de conexão deve ser para uma conta de armazenamento de uso geral, não para uma [conta de armazenamento somente blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Acesso** | Indica se você estará lendo ou gravando. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Acesse os dados de `context.bindings.<name from function.json>`BLOB usando.

# <a name="pythontabpython"></a>[Python](#tab/python)

Acessar dados de blob por meio do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o [exemplo de entrada](#input---example) para obter detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O `@BlobInput` atributo fornece acesso ao blob que disparou a função. Se você usar uma matriz de bytes com o atributo, `dataType` defina `binary`como. Consulte o [exemplo de entrada](#input---example) para obter detalhes.

---

## <a name="output"></a>Output

Use associações de saída do armazenamento de BLOBs para gravar BLOBs.

## <a name="output---example"></a>Saída - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir é uma [ C# função](functions-dotnet-class-library.md) que usa um gatilho de BLOB e duas associações de blob de saída. A função é disparada pela criação de um blob de imagem no contêiner *Sample-images* . Ele cria cópias de tamanho pequeno e médio do blob de imagem.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
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
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

<!--Same example for input and output. -->

O exemplo a seguir mostra associações de entrada e saída de BLOB em um arquivo *Function. JSON* e [ C# um código de script (. CSX)](functions-reference-csharp.md) que usa as associações. A função faz uma cópia de um blob de texto. A função é disparada por uma mensagem da fila que contém o nome do blob a ser copiado. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *Function. JSON* , a `queueTrigger` Propriedade Metadata é usada para especificar o nome do `path` blob nas propriedades:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

O exemplo a seguir mostra associações de entrada e saída de BLOB em um arquivo *Function. JSON* e [código JavaScript](functions-reference-node.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob a ser copiado. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *Function. JSON* , a `queueTrigger` Propriedade Metadata é usada para especificar o nome do `path` blob nas propriedades:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

O exemplo a seguir mostra associações de entrada e saída de BLOB em um arquivo *Function. JSON* e [código Python](functions-reference-python.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob a ser copiado. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *Function. JSON* , a `queueTrigger` Propriedade Metadata é usada para especificar o nome do `path` blob nas propriedades:

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

Este é o código Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta secção contém os exemplos seguintes:

* [Gatilho HTTP, usando OutputBinding](#http-trigger-using-outputbinding-java)
* [Gatilho de fila, usando valor de retorno de função](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Gatilho HTTP, usando OutputBinding (Java)

 O exemplo a seguir mostra uma função Java que usa `HttpTrigger` a anotação para receber um parâmetro que contém o nome de um arquivo em um contêiner de armazenamento de BLOBs. Em `BlobInput` seguida, a anotação lê o arquivo e passa seu conteúdo para a função `byte[]`como um. A `BlobOutput` anotação é associada `OutputBinding outputItem`, que é usada pela função para gravar o conteúdo do blob de entrada no contêiner de armazenamento configurado.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Gatilho de fila, usando o valor de retorno de função (Java)

 O exemplo a seguir mostra uma função Java que usa `QueueTrigger` a anotação para receber uma mensagem que contém o nome de um arquivo em um contêiner de armazenamento de BLOBs. Em `BlobInput` seguida, a anotação lê o arquivo e passa seu conteúdo para a função `byte[]`como um. A `BlobOutput` anotação é associada ao valor de retorno da função, que é usado pelo tempo de execução para gravar o conteúdo do blob de entrada no contêiner de armazenamento configurado.

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

 Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a `@BlobOutput` anotação nos parâmetros de função cujo valor seria gravado em um objeto no armazenamento de BLOBs.  O tipo de parâmetro deve `OutputBinding<T>`ser, em que T é qualquer tipo Java nativo ou um POJO.

---

## <a name="output---attributes"></a>Saída - atributos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use [](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)o blobattribute.

O construtor do atributo usa o caminho para o blob e um `FileAccess` parâmetro que indica leitura ou gravação, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O `@BlobOutput` atributo fornece acesso ao blob que disparou a função. Se você usar uma matriz de bytes com o atributo, `dataType` defina `binary`como. Consulte o [exemplo de saída](#output---example) para obter detalhes.

---

Para obter um exemplo completo, consulte [exemplo de saída](#output---example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [acionador - atributos](#trigger---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Blob` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Tem de ser definido como `blob`. |
|**direction** | n/d | Deve ser definido como `out` para uma associação de saída. As exceções são indicadas na seção [uso](#output---usage) . |
|**name** | n/d | O nome da variável que representa o blob no código de função.  Defina como `$return` para referenciar o valor de retorno da função.|
|**path** |**BlobPath** | O caminho para o contêiner de BLOB. |
|**ligação** |**ligação**| O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyStorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão `AzureWebJobsStorage`na configuração do aplicativo que é nomeada.<br><br>A cadeia de conexão deve ser para uma conta de armazenamento de uso geral, não para uma [conta de armazenamento somente blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Acesso** | Indica se você estará lendo ou gravando. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No JavaScript, acesse os dados de `context.bindings.<name from function.json>`BLOB usando.

# <a name="pythontabpython"></a>[Python](#tab/python)

Você pode declarar parâmetros de função como os seguintes tipos para gravar no armazenamento de BLOBs:

* Cadeias de caracteres como`func.Out(str)`
* Fluxos como`func.Out(func.InputStream)`

Consulte o [exemplo de saída](#output---example) para obter detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O `@BlobOutput` atributo fornece acesso ao blob que disparou a função. Se você usar uma matriz de bytes com o atributo, `dataType` defina `binary`como. Consulte o [exemplo de saída](#output---example) para obter detalhes.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo |  Referência |
|---|---|
| Blob | [Códigos de erro de BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabela, fila |  [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabela, fila |  [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
