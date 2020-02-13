---
title: Encadernações de armazenamento Azure Blob para funções azure
description: Compreenda como utilizar gatilhos de armazenamento Azure Blob e encadernações em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 5113b59f314a40feb02485b3fdc99fe4df406d88
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168097"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Encadernações de armazenamento Azure Blob para funções azure

Este artigo explica como trabalhar com encadernações de armazenamento Azure Blob em Funções Azure. As Funções Azure suportam as ligações de gatilho, entrada e saída para bolhas. O artigo inclui uma secção para cada encadernação:

* [Gatilho blob](#trigger)
* [Encadernação de entrada blob](#input)
* [Encadernação de saída blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Utilize o gatilho da Grelha de Eventos em vez do gatilho de armazenamento Blob para contas de armazenamento apenas com bolhas, para alta escala ou para reduzir a latência. Para mais informações, consulte a secção [Trigger.](#trigger)

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As encadernações de armazenamento Blob são fornecidas no pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

As encadernações de armazenamento Blob são fornecidas no [microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet pacote, versão 3.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

O gatilho de armazenamento Blob inicia uma função quando é detetada uma bolha nova ou atualizada. O conteúdo blob é fornecido como entrada para a função.

O gatilho da Rede de [Eventos](functions-bindings-event-grid.md) tem suporte incorporado para [eventos de blob](../storage/blobs/storage-blob-event-overview.md) e também pode ser usado para iniciar uma função quando uma bolha nova ou atualizada é detetada. Por exemplo, consulte o [redimensionado da Imagem com](../event-grid/resize-images-on-storage-blob-upload-event.md) tutorial da Grelha de Eventos.

Utilize a Grelha de Eventos em vez do gatilho de armazenamento Blob para os seguintes cenários:

* Contas do Blob Storage
* Alta escala
* Minimizar a latência

### <a name="blob-storage-accounts"></a>Contas do Blob Storage

As contas de [armazenamento blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) são suportadas para as ligações de entrada e saída blob, mas não para os gatilhos blob. Os gatilhos de armazenamento blob requerem uma conta de armazenamento para fins gerais.

### <a name="high-scale"></a>Alta escala

A alta escala pode ser vagamente definida como recipientes que têm mais de 100.000 blobs ou contas de armazenamento que têm mais de 100 atualizações blob por segundo.

### <a name="latency-issues"></a>Problemas de latência

Se a sua aplicação de funções estiver no plano de consumo, pode haver até 10 minutos de atraso no processamento de novas bolhas se uma aplicação de função tiver ficado inativa. Para evitar esta latência, pode mudar para um plano de Serviço de Aplicações com ativado Always On. Também pode utilizar um gatilho da Grelha de [Eventos](functions-bindings-event-grid.md) com a sua conta de armazenamento Blob. Por exemplo, consulte o tutorial da Grelha de [Eventos.](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json) 

### <a name="queue-storage-trigger"></a>Acionador do armazenamento de filas

Além da Grelha de Eventos, outra alternativa para o processamento de bolhas é o gatilho de armazenamento de fila, mas não tem suporte incorporado para eventos de blob. Teria de criar mensagens de fila ao criar ou atualizar bolhas. Por exemplo, que pressupõe que tenha feito isso, veja o exemplo vinculativo da [entrada blob mais tarde neste artigo](#input---example).

## <a name="trigger---example"></a>Acionador - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que escreve um tronco quando uma bolha é adicionada ou atualizada no recipiente `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia `{name}` no caminho do gatilho da bolha `samples-workitems/{name}` cria uma [expressão de ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#trigger---blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre o atributo `BlobTrigger`, consulte [Trigger - atributos](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho blob num ficheiro e código *function.json* que utiliza a encadernação. A função escreve um tronco quando uma bolha é adicionada ou atualizada no [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems` .

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A cadeia `{name}` no caminho do gatilho da bolha `samples-workitems/{name}` cria uma [expressão de ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#trigger---blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre as propriedades do ficheiro *function.json,* consulte a secção [de Configuração](#trigger---configuration) que explica estas propriedades.

Aqui está C# o código de script que se liga a um `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está C# o código de script que se liga a um `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho blob num ficheiro *function.json* e [código JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve um tronco quando uma bolha é adicionada ou atualizada no recipiente `samples-workitems`.

Aqui está o ficheiro *função.json:*

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

A cadeia `{name}` no caminho do gatilho da bolha `samples-workitems/{name}` cria uma [expressão de ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#trigger---blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre as propriedades do ficheiro *function.json,* consulte a secção [de Configuração](#trigger---configuration) que explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho blob num ficheiro *function.json* e [código Python](functions-reference-python.md) que utiliza a ligação. A função escreve um tronco quando uma bolha é adicionada ou atualizada no [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems` .

Aqui está o ficheiro *função.json:*

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

A cadeia `{name}` no caminho do gatilho da bolha `samples-workitems/{name}` cria uma [expressão de ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#trigger---blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre as propriedades do ficheiro *function.json,* consulte a secção [de Configuração](#trigger---configuration) que explica estas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta função escreve um tronco quando uma bolha é adicionada ou atualizada no recipiente `myblob`.

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de bolha:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  O construtor do atributo pega numa corda de caminho que indica o recipiente para observar e opcionalmente um padrão de [nome blob](#trigger---blob-name-patterns). Segue-se um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Pode definir a propriedade `Connection` para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para um exemplo completo, consulte [o exemplo do Gatilho](#trigger---example).

* [ArmazenamentoAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

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

A conta de armazenamento a utilizar é determinada na seguinte ordem:

* A propriedade `Connection` do `BlobTrigger` atributo.
* O atributo `StorageAccount` aplicado ao mesmo parâmetro que o atributo `BlobTrigger`.
* O atributo `StorageAccount` aplicado à função.
* O atributo `StorageAccount` aplicado à classe.
* A conta de armazenamento padrão para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobTrigger` é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#trigger---example) para obter detalhes.

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `BlobTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `blobTrigger`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | deve ser definido para `in`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. As exceções são observadas na secção [de utilização.](#trigger---usage) |
|**nome** | n/d | O nome da variável que representa a bolha no código de função. |
|**caminho** | **BlobPath** |O [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) para monitorizar.  Pode ser um padrão de [nome blob.](#trigger---blob-name-patterns) |
|**conexão** | **Conexão** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada `AzureWebJobsStorage`.<br><br>A cadeia de ligação deve ser para uma conta de armazenamento geral, não para uma conta de [armazenamento Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aceda aos dados blob utilizando `context.bindings.<NAME>` onde `<NAME>` corresponda ao valor definido na *função.json*.

# <a name="pythontabpython"></a>[python](#tab/python)

Aceda aos dados blob através do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o exemplo do [gatilho](#trigger---example) para obter detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobTrigger` é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#trigger---example) para obter detalhes.

---

## <a name="trigger---blob-name-patterns"></a>Gatilho - padrões de nome blob

Pode especificar um padrão de nome blob na propriedade `path` em *função.json* ou no `BlobTrigger` atributo construtor. O padrão de nome pode ser um [filtro ou expressão de encadernação.](./functions-bindings-expressions-patterns.md) As seguintes secções fornecem exemplos.

### <a name="get-file-name-and-extension"></a>Obtenha o nome do ficheiro e a extensão

O exemplo que se segue mostra como se ligar ao nome do ficheiro blob e à extensão separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```

Se a bolha for denominada *original-Blob1.txt,* os valores das variáveis `blobname` e `blobextension` no código de função são *original-Blob1* e *txt*.

### <a name="filter-on-blob-name"></a>Filtro no nome blob

O exemplo seguinte dispara apenas em bolhas no recipiente `input` que começam com a corda "original":

```json
"path": "input/original-{name}",
```

Se o nome blob for *original-Blob1.txt,* o valor da variável `name` no código de função é `Blob1`.

### <a name="filter-on-file-type"></a>Filtro no tipo de ficheiro

O exemplo seguinte dispara apenas em ficheiros *.png:*

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrar em aparelhos encaracolados em nomes de ficheiros

Para procurar aparelhos encaracolados em nomes de ficheiros, escape do aparelho utilizando dois aparelhos. Os filtros de exemplo seguintes para bolhas que tenham aparelhos encaracolados no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se a bolha for denominada *{20140101}-soundfile.mp3*, o `name` valor variável no código de função é *soundfile.mp3*.

## <a name="trigger---metadata"></a>Gatilho - metadados

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[python](#tab/python)

Os metadados não estão disponíveis em Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Os metadados não estão disponíveis em Java.

---

## <a name="trigger---blob-receipts"></a>Gatilho - recibos de bolha

O tempo de funcionamento das Funções Azure garante que nenhuma função de gatilho blob é chamada mais de uma vez para a mesma bolha nova ou atualizada. Para determinar se uma determinada versão blob foi processada, mantém *recibos blob*.

A Azure Functions armazena recibos blob num contentor chamado *azure-webjobs-hosts* na conta de armazenamento Azure para a sua aplicação de funções (definida pela definição de aplicações `AzureWebJobsStorage`). Um recibo blob tem as seguintes informações:

* A função desencadeada ("&lt;nome da *app função>* . Funções.&lt;nome de *função>* ", por exemplo: "MyFunctionApp.Functions.CopyBlob")
* O nome do recipiente
* O tipo de bolha ("BlockBlob" ou "PageBlob")
* O nome blob
* O ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de uma bolha, elimine manualmente o recibo de bolha desse recipiente de anfitriões de *azure-webjobs.* Embora o reprocessamento possa não ocorrer imediatamente, é garantido que ocorra em um ponto posterior no tempo.

## <a name="trigger---poison-blobs"></a>Gatilho - bolhas venenosas

Quando uma função de gatilho blob falha para uma determinada bolha, as funções Azure retestam que funcionam um total de 5 vezes por padrão.

Se todas as 5 tentativas falharem, a Azure Functions adiciona uma mensagem a uma fila de armazenamento chamada *webjobs-blobtrigger-poison*. A mensagem de fila para bolhas venenosas é um objeto JSON que contém as seguintes propriedades:

* FunçãoId (no formato&lt;nome da *app de função>* . Funções.&lt;nome da *função>* )
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Gatilho - concurrency e uso de memória

O gatilho de bolha utiliza uma fila internamente, pelo que o número máximo de invocações de função simultânea é controlado pela configuração das [filas em host.json](functions-host-json.md#queues). As definições predefinidas limitam a moeda a 24 invocações. Este limite aplica-se separadamente a cada função que utiliza um gatilho de bolha.

[O plano de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita uma aplicação de função numa máquina virtual (VM) a 1,5 GB de memória. A memória é utilizada por cada instância de função de execução simultânea e pelas próprias funções. Se uma função desencadeada por bolhas carregar toda a bolha na memória, a memória máxima utilizada por essa função apenas para bolhas é de 24 * tamanho máximo de bolha. Por exemplo, uma aplicação de função com três funções desencadeadas por bolhas e as definições predefinidas teriam uma conmoeda máxima por VM de 3*24 = 72 invocações de função.

As funções JavaScript e Java carregam toda a bolha na memória, e C# as funções fazem isso se se ligar a `string`, `Byte[]`ou POCO.

## <a name="trigger---polling"></a>Gatilho - sondagem

A sondagem funciona como um híbrido entre a inspeção de logs e a execução de verificações de contêiner periódicas. Os BLOBs são verificados em grupos de 10.000 por vez com um token de continuação usado entre intervalos.

> [!WARNING]
> Além disso, os registos de armazenamento são criados numa base de ["melhor esforço".](/rest/api/storageservices/About-Storage-Analytics-Logging) Não há garantias de que todos os eventos sejam capturados. Sob algumas condições, os registos podem ser perdidos.
> 
> Se necessitar de um processamento de bolha mais rápido ou fiável, considere criar uma mensagem de [fila](../storage/queues/storage-dotnet-how-to-use-queues.md) quando criar a bolha. Em seguida, use um gatilho de [fila](functions-bindings-storage-queue.md) em vez de um gatilho de bolha para processar a bolha. Outra opção é utilizar a Grelha de Eventos; ver o tutorial [Automate redimensionando imagens carregadas usando a Grelha de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Input

Utilize uma encadernação de entrada de armazenamento Blob para ler bolhas.

## <a name="input---example"></a>Entrada - exemplo

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

A secção de [configuração](#input---configuration) explica estas propriedades.

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

A secção de [configuração](#input---configuration) explica estas propriedades.

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

A secção de [configuração](#input---configuration) explica estas propriedades.

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

## <a name="input---attributes"></a>Introdução - atributos

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

Pode utilizar o atributo `StorageAccount` para especificar a conta de armazenamento na classe, método ou nível de parâmetro. Para mais informações, consulte [Trigger - atributos](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobInput` dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte com o atributo, coloque `dataType` para `binary`. Consulte o exemplo de [entrada](#input---example) para mais detalhes.

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Blob`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `blob`. |
|**direção** | n/d | deve ser definido para `in`. As exceções são observadas na secção [de utilização.](#input---usage) |
|**nome** | n/d | O nome da variável que representa a bolha no código de função.|
|**caminho** |**BlobPath** | O caminho para a bolha. |
|**conexão** |**Conexão**| O nome de uma definição de aplicação que contém a cadeia de [ligação de armazenamento](../storage/common/storage-configure-connection-string.md) a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada `AzureWebJobsStorage`.<br><br>A cadeia de ligação deve ser para uma conta de armazenamento geral, não para uma [conta de armazenamento apenas para bolhas.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|n/d | **Acesso** | Indica se vai ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aceda aos dados blob utilizando `context.bindings.<NAME>` onde `<NAME>` corresponda ao valor definido na *função.json*.

# <a name="pythontabpython"></a>[python](#tab/python)

Aceda aos dados blob através do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o exemplo de [entrada](#input---example) para mais detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobInput` dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte com o atributo, coloque `dataType` para `binary`. Consulte o exemplo de [entrada](#input---example) para mais detalhes.

---

## <a name="output"></a>Saída

Utilize encadernações de saída de armazenamento Blob para escrever bolhas.

## <a name="output---example"></a>Saída - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte é uma [ C# função](functions-dotnet-class-library.md) que usa um gatilho de bolha e duas ligações de saída. A função é desencadeada pela criação de uma bolha de imagem no recipiente *de imagens de amostra.* Cria cópias de tamanho pequeno e médio da bolha de imagem.

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

A secção de [configuração](#output---configuration) explica estas propriedades.

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

A secção de [configuração](#output---configuration) explica estas propriedades.

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

A secção de [configuração](#output---configuration) explica estas propriedades.

Aqui está o código Python:

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

* [Gatilho HTTP, utilizando OutputBinding](#http-trigger-using-outputbinding-java)
* [Gatilho de fila, utilizando o valor de retorno da função](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Gatilho HTTP, utilizando OutputBinding (Java)

 O exemplo seguinte mostra uma função Java que utiliza a anotação `HttpTrigger` para receber um parâmetro contendo o nome de um ficheiro num recipiente de armazenamento de bolhas. A anotação `BlobInput` lê então o ficheiro e passa o seu conteúdo para a função como `byte[]`. A anotação `BlobOutput` liga-se a `OutputBinding outputItem`, que é então utilizada pela função de escrever o conteúdo da bolha de entrada no recipiente de armazenamento configurado.

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

 O exemplo seguinte mostra uma função Java que utiliza a anotação `QueueTrigger` para receber uma mensagem contendo o nome de um ficheiro num recipiente de armazenamento de bolhas. A anotação `BlobInput` lê então o ficheiro e passa o seu conteúdo para a função como `byte[]`. A anotação `BlobOutput` liga-se ao valor de devolução da função, que é então utilizado pelo tempo de funcionamento para escrever o conteúdo da bolha de entrada no recipiente de armazenamento configurado.

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

 Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a anotação `@BlobOutput` em parâmetros de função cujo valor seria escrito a um objeto no armazenamento de bolhas.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo java nativo ou um POJO.

---

## <a name="output---attributes"></a>Saída - atributos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o [BlobAttribute.](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)

O construtor do atributo segue o caminho para a bolha e um parâmetro `FileAccess` indicando ler ou escrever, como se pode ver no seguinte exemplo:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Pode definir a propriedade `Connection` para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobOutput` dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte com o atributo, coloque `dataType` para `binary`. Consulte o exemplo de [saída](#output---example) para mais detalhes.

---

Para um exemplo completo, consulte o [exemplo da saída](#output---example).

Pode utilizar o atributo `StorageAccount` para especificar a conta de armazenamento na classe, método ou nível de parâmetro. Para mais informações, consulte [Trigger - atributos](#trigger---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Blob`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `blob`. |
|**direção** | n/d | Deve ser definido para `out` para uma ligação de saída. As exceções são observadas na secção [de utilização.](#output---usage) |
|**nome** | n/d | O nome da variável que representa a bolha no código de função.  Definir para `$return` para referenciar o valor de devolução da função.|
|**caminho** |**BlobPath** | O caminho para o recipiente de bolhas. |
|**conexão** |**Conexão**| O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada `AzureWebJobsStorage`.<br><br>A cadeia de ligação deve ser para uma conta de armazenamento geral, não para uma [conta de armazenamento apenas para bolhas.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|n/d | **Acesso** | Indica se vai ler ou escrever. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No JavaScript, aceda aos dados blob utilizando `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[python](#tab/python)

Pode declarar os parâmetros de função como os seguintes tipos para escrever para o armazenamento de bolhas:

* Cordas como `func.Out(str)`
* Riachos como `func.Out(func.InputStream)`

Consulte o exemplo de [saída](#output---example) para mais detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobOutput` dá-lhe acesso à bolha que desencadeou a função. Se utilizar um matriz de byte com o atributo, coloque `dataType` para `binary`. Consulte o exemplo de [saída](#output---example) para mais detalhes.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo |  Referência |
|---|---|
| Blobs | [Códigos de erro blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Mesa, Fila |  [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila |  [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
