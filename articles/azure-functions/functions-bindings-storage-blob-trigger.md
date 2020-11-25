---
title: Gatilho de armazenamento Azure Blob para Funções Azure
description: Saiba como executar uma Função Azure à medida que os dados de armazenamento do Azure Blob mudam.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 45393f116149f6cf16763d2d7033f8425df235bf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998852"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Gatilho de armazenamento Azure Blob para Funções Azure

O gatilho de armazenamento Blob inicia uma função quando é detetada uma bolha nova ou atualizada. O conteúdo do blob é fornecido como [entrada para a função](./functions-bindings-storage-blob-input.md).

O gatilho de armazenamento Azure Blob requer uma conta de armazenamento para fins gerais. As contas V2 de armazenamento com [espaços hierárquicos](../storage/blobs/data-lake-storage-namespace.md) também são suportadas. Para utilizar uma conta apenas blob, ou se a sua aplicação tiver necessidades especializadas, reveja as alternativas à utilização deste gatilho.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="polling"></a>Consultas

As sondagens funcionam como um híbrido entre a inspeção de registos e a execução de verificações periódicas de contentores. As bolhas são digitalizadas em grupos de 10.000 de cada vez com um símbolo de continuação usado entre intervalos.

> [!WARNING]
> Além disso, [os registos de armazenamento são criados numa](/rest/api/storageservices/About-Storage-Analytics-Logging) base de "melhor esforço". Não há garantias de que todos os eventos sejam capturados. Em algumas condições, podem ser perdidos registos.
> 
> Se necessitar de um processamento de bolhas mais rápido ou fiável, considere criar uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) quando criar a bolha. Em seguida, use um gatilho de [fila](functions-bindings-storage-queue.md) em vez de um gatilho blob para processar a bolha. Outra opção é utilizar a Grade de Eventos; ver o tutorial [Automatizar redimensionar imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="alternatives"></a>Alternativas

### <a name="event-grid-trigger"></a>Gatilho da grelha de evento

O [gatilho da Grade de Eventos](functions-bindings-event-grid.md) também tem suporte incorporado para [eventos blob.](../storage/blobs/storage-blob-event-overview.md) Utilize a Grelha de Eventos em vez do gatilho de armazenamento Blob para os seguintes cenários:

- **Contas de armazenamento apenas blob**: [As contas de armazenamento apenas blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) são suportadas para entradas blob e encadernações de saída, mas não para gatilhos blob.

- **Alta escala**: A escala alta pode ser definida vagamente como recipientes que têm mais de 100.000 bolhas ou contas de armazenamento que têm mais de 100 atualizações de bolhas por segundo.

- **Minimizar a latência**: Se a sua aplicação de função estiver no plano de Consumo, pode haver até um atraso de 10 minutos no processamento de novas bolhas se uma aplicação de função tiver ficado inativa. Para evitar esta latência, pode mudar para um plano de Serviço de Aplicações com Sempre Ativado. Também pode utilizar um [gatilho de Grade de Eventos](functions-bindings-event-grid.md) com a sua conta de armazenamento Blob. Por exemplo, consulte o [tutorial da Grelha de Eventos.](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)

Consulte o [redimensionar](../event-grid/resize-images-on-storage-blob-upload-event.md) a Imagem com tutorial de Grade de Eventos de um exemplo de Grade de Eventos.

### <a name="queue-storage-trigger"></a>Acionador do armazenamento de filas

Outra abordagem para processar bolhas é escrever mensagens de fila que correspondam a bolhas sendo criadas ou modificadas e, em seguida, usar um [gatilho de armazenamento de fila](./functions-bindings-storage-queue.md) para começar o processamento.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que escreve um tronco quando uma bolha é adicionada ou atualizada no `samples-workitems` recipiente.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia `{name}` no caminho do gatilho da bolha cria uma expressão de `samples-workitems/{name}` [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome do ficheiro da bolha de desencadeamento. Para mais informações, consulte [os padrões de nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre o `BlobTrigger` atributo, consulte [atributos e anotações](#attributes-and-annotations).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho de uma *function.jsno* ficheiro e no código que utiliza a ligação. A função escreve um registo quando uma bolha é adicionada ou atualizada no `samples-workitems` [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

A cadeia `{name}` no caminho do gatilho da bolha cria uma expressão de `samples-workitems/{name}` [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome do ficheiro da bolha de desencadeamento. Para mais informações, consulte [os padrões de nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre *function.jssobre* propriedades de ficheiros, consulte a secção [de Configuração](#configuration) que explica estas propriedades.

Aqui está o código de script C# que se liga `Stream` a:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está o código de script C# que se liga `CloudBlockBlob` a:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho blob numa *function.jsno* ficheiro e [código JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve um registo quando uma bolha é adicionada ou atualizada no `samples-workitems` recipiente.

Aqui está a *function.jsarquivada:*

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

A cadeia `{name}` no caminho do gatilho da bolha cria uma expressão de `samples-workitems/{name}` [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome do ficheiro da bolha de desencadeamento. Para mais informações, consulte [os padrões de nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre *function.jssobre* propriedades de ficheiros, consulte a secção [de Configuração](#configuration) que explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação do gatilho de uma *function.jsno* ficheiro e o [código Python](functions-reference-python.md) que utiliza a ligação. A função escreve um registo quando uma bolha é adicionada ou atualizada no `samples-workitems` [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Aqui está a *function.jsarquivada:*

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

A cadeia `{name}` no caminho do gatilho da bolha cria uma expressão de `samples-workitems/{name}` [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome do ficheiro da bolha de desencadeamento. Para mais informações, consulte [os padrões de nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre *function.jssobre* propriedades de ficheiros, consulte a secção [de Configuração](#configuration) que explica estas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Esta função escreve um registo quando uma bolha é adicionada ou atualizada no `myblob` recipiente.

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

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  O construtor do atributo toma uma corda de caminho que indica o recipiente para observar e opcionalmente um [padrão de nome blob](#blob-name-patterns). Eis um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Pode definir a `Connection` propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para obter um exemplo completo, consulte [o exemplo do Gatilho](#example).

* [ArmazenamentoCotaçãotribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado ao nível do parâmetro, método ou classe. O exemplo a seguir mostra o nível de classe e o nível do método:

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

* Propriedade `BlobTrigger` do `Connection` atributo.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro que o `BlobTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento predefinida para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#example) para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `BlobTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `blobTrigger` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/a | Deve ser definido para `in` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. As exceções são anotados na secção [de utilização.](#usage) |
|**nome** | n/a | O nome da variável que representa a bolha no código de função. |
|**caminho** | **BlobPath** |O [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) para monitorar.  Pode ser um [padrão de nome blob](#blob-name-patterns). |
|**conexão** | **Ligação** | O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` "MyStorage", o tempo de execução de Funções procura uma definição de aplicação que se chama "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .<br><br>O fio de ligação deve destinar-se a uma conta de armazenamento para fins gerais e não a uma [conta de armazenamento Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceder aos dados blob `context.bindings.<NAME>` utilizando o valor definido nafunction.js`<NAME>` *em*.

# <a name="python"></a>[Python](#tab/python)

Aceder aos dados do blob através do parâmetro dactilografado como [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o exemplo do [gatilho](#example) para obter mais detalhes.

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#example) para obter mais detalhes.

---

## <a name="blob-name-patterns"></a>Padrões de nome de blob

Pode especificar um padrão de nome blob na `path` propriedade emfunction.js *sobre* ou no construtor `BlobTrigger` de atributos. O padrão de nome pode ser um [filtro ou expressão de ligação](./functions-bindings-expressions-patterns.md). As seguintes secções dão exemplos.

### <a name="get-file-name-and-extension"></a>Obtenha nome e extensão de ficheiros

O exemplo a seguir mostra como ligar-se ao nome do ficheiro blob e à extensão separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```

Se a bolha for nomeada *original-Blob1.txt,* os valores do código de `blobname` função e `blobextension` variáveis são *original-Blob1* e *txt*.

### <a name="filter-on-blob-name"></a>Filtro no nome blob

O exemplo a seguir dispara apenas em bolhas no `input` recipiente que começam com a corda "original-":

```json
"path": "input/original-{name}",
```

Se o nome da bolha for *original-Blob1.txt,* o valor da `name` variável no código de função é `Blob1.txt` .

### <a name="filter-on-file-type"></a>Filtro no tipo de ficheiro

O exemplo a seguir dispara apenas em ficheiros *.png:*

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrar em chaves encaracoladas em nomes de ficheiros

Para procurar aparelhos encaracolados em nomes de ficheiros, escape o aparelho utilizando dois aparelhos. Os seguintes filtros de exemplo para bolhas que tenham aparelhos encaracolados no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se a bolha for nomeada *{20140101}-soundfile.mp3,* o `name` valor variável no código de função é *soundfile.mp3*.

## <a name="metadata"></a>Metadados

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Os metadados não estão disponíveis em Python.

# <a name="java"></a>[Java](#tab/java)

Os metadados não estão disponíveis em Java.

---

## <a name="blob-receipts"></a>Recibos blob

O tempo de funcionamento das funções Azure garante que nenhuma função de gatilho blob seja chamada mais de uma vez para a mesma bolha nova ou atualizada. Para determinar se uma determinada versão blob foi processada, mantém *recibos blob*.

A Azure Functions armazena recibos de bolhas num recipiente chamado *azure-webjobs-hosts* na conta de armazenamento Azure para a sua aplicação de função (definida pela definição da `AzureWebJobsStorage` aplicação). Um recibo blob tem as seguintes informações:

* A função desencadeada ("*&lt; nome da aplicação de função>*. Funções. *&lt; nome da função>*", por exemplo: "MyFunctionApp.Functions.CopyBlob")
* O nome do recipiente
* O tipo de bolha ("BlockBlob" ou "PageBlob")
* O nome blob
* O ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de uma bolha, elimine o recibo de bolha para a bolha do recipiente *azure-webjobs-hosts* manualmente. Embora o reprocessamento possa não ocorrer imediatamente, é garantido que ocorra num momento posterior. Para reprocessar imediatamente, a bolha *scaninfo* em *azure-webjobs-hosts/blobscaninfo* pode ser atualizada. Quaisquer bolhas com um último tempo de tempo modificado após a `LatestScan` propriedade serão novamente digitalizadas.

## <a name="poison-blobs"></a>Bolhas venenosas

Quando uma função de gatilho blob falha para uma determinada bolha, o Azure Functions recauchutagem que funcionam um total de 5 vezes por defeito.

Se todas as 5 tentativas falharem, a Azure Functions adiciona uma mensagem a uma fila de armazenamento chamada *webjobs-blobtrigger-poison*. O número máximo de retrós assim questão é configurável. A mesma definição MaxDequeueCount é usada para o manuseamento de bolhas venenosas e manuseamento de mensagens de fila venenosas. A mensagem de fila para bolhas venenosas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no nome da aplicação da *&lt; função de* formato>. Funções. *&lt; nome da função>*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* Nome Blob
* ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Concurrency e utilização da memória

O gatilho blob utiliza uma fila internamente, pelo que o número máximo de invocações de função simultânea é controlado pela [configuração](functions-host-json.md#queues)de filas em host.jsem . As definições predefinitórias limitam a concordância a 24 invocações. Este limite aplica-se separadamente a cada função que utiliza um gatilho de bolha.

[O plano de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita uma aplicação de função numa máquina virtual (VM) a 1,5 GB de memória. A memória é utilizada por cada instância de execução simultânea e pelo tempo de funcionamento das funções em si. Se uma função com o gatilho de bolhas colocar toda a bolha na memória, a memória máxima utilizada por essa função apenas para bolhas é de 24 * tamanho máximo de bolha. Por exemplo, uma aplicação de função com três funções acionadas por bolhas e as definições predefinidas teria uma concordância máxima por VM de 3*24 = 72 invocações de função.

As funções JavaScript e Java carregam toda a bolha na memória, e as funções C# fazem-no se se ligar a `string` , ou `Byte[]` . .

## <a name="next-steps"></a>Passos seguintes

- [Leia os dados de armazenamento de bolhas quando uma função é executado](./functions-bindings-storage-blob-input.md)
- [Escreva dados de armazenamento de bolhas a partir de uma função](./functions-bindings-storage-blob-output.md)
