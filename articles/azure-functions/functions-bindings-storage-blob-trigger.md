---
title: Gatilho de armazenamento Azure Blob para funções azure
description: Aprenda a executar uma Função Azure à medida que os dados de armazenamento do Azure Blob mudam.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 61fbaf37577efdab0b147d437ae78fc4df0764cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084962"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Gatilho de armazenamento Azure Blob para funções azure

O gatilho de armazenamento Blob inicia uma função quando é detetada uma bolha nova ou atualizada. O conteúdo da bolha é fornecido como [entrada para a função](./functions-bindings-storage-blob-input.md).

O gatilho de armazenamento Azure Blob requer uma conta de armazenamento de uso geral. Para utilizar uma conta só para blob, ou se a sua aplicação tiver necessidades especializadas, reveja as alternativas à utilização deste gatilho.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternativas

### <a name="event-grid-trigger"></a>Gatilho da Grelha de Eventos

O gatilho da Grelha de [Eventos](functions-bindings-event-grid.md) também tem suporte incorporado para [eventos de blob.](../storage/blobs/storage-blob-event-overview.md) Utilize a Grelha de Eventos em vez do gatilho de armazenamento Blob para os seguintes cenários:

- **Contas de armazenamento apenas**para blob : [As contas de armazenamento apenas blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) são suportadas para encadernações de entrada e saída blob, mas não para gatilhos blob.

- **Alta escala**: A alta escala pode ser vagamente definida como recipientes que têm mais de 100.000 blobs ou contas de armazenamento que têm mais de 100 atualizações blob por segundo.

- **Minimizar a latência**: Se a sua aplicação de funções estiver no plano de consumo, pode haver até 10 minutos de atraso no processamento de novas bolhas se uma aplicação de função tiver ficado inativa. Para evitar esta latência, pode mudar para um plano de Serviço de Aplicações com ativado Always On. Também pode utilizar um gatilho da Grelha de [Eventos](functions-bindings-event-grid.md) com a sua conta de armazenamento Blob. Por exemplo, consulte o tutorial da Grelha de [Eventos.](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)

Consulte o [redimensionado](../event-grid/resize-images-on-storage-blob-upload-event.md) da Imagem com tutorial da Grelha de Eventos de um exemplo da Grelha de Eventos.

### <a name="queue-storage-trigger"></a>Acionador do armazenamento de filas

Outra abordagem para processar bolhas é escrever mensagens de fila que correspondam a bolhas que são criadas ou modificadas e, em seguida, usar um gatilho de armazenamento de [fila](./functions-bindings-storage-queue.md) para começar a processar.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que escreve um tronco `samples-workitems` quando uma bolha é adicionada ou atualizada no recipiente.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A `{name}` cadeia no caminho `samples-workitems/{name}` do gatilho da bolha cria uma expressão de [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais `BlobTrigger` informações sobre o atributo, consulte [atributos e anotações.](#attributes-and-annotations)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho blob num ficheiro e código *function.json* que utiliza a encadernação. A função escreve um tronco quando uma bolha `samples-workitems` é adicionada ou atualizada no [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

A `{name}` cadeia no caminho `samples-workitems/{name}` do gatilho da bolha cria uma expressão de [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre as propriedades do ficheiro *function.json,* consulte a secção [de Configuração](#configuration) que explica estas propriedades.

Aqui está o código de script `Stream`C# que se liga a um:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está o código de script `CloudBlockBlob`C# que se liga a um:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho blob num ficheiro *function.json* e [código JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve um tronco quando uma bolha `samples-workitems` é adicionada ou atualizada no recipiente.

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

A `{name}` cadeia no caminho `samples-workitems/{name}` do gatilho da bolha cria uma expressão de [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre as propriedades do ficheiro *function.json,* consulte a secção [de Configuração](#configuration) que explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho blob num ficheiro *function.json* e [código Python](functions-reference-python.md) que utiliza a ligação. A função escreve um tronco quando uma bolha `samples-workitems` é adicionada ou atualizada no [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

A `{name}` cadeia no caminho `samples-workitems/{name}` do gatilho da bolha cria uma expressão de [ligação](./functions-bindings-expressions-patterns.md) que pode usar no código de função para aceder ao nome de ficheiro da bolha de desencadeamento. Para mais informações, consulte os padrões de [nome Blob](#blob-name-patterns) mais tarde neste artigo.

Para obter mais informações sobre as propriedades do ficheiro *function.json,* consulte a secção [de Configuração](#configuration) que explica estas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Esta função escreve um tronco quando uma bolha `myblob` é adicionada ou atualizada no recipiente.

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

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de bolha:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  O construtor do atributo pega numa corda de caminho que indica o recipiente para observar e opcionalmente um padrão de [nome blob](#blob-name-patterns). Segue-se um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Pode definir `Connection` a propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para um exemplo completo, consulte [o exemplo do Gatilho](#example).

* [ArmazenamentoAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo que se segue mostra o nível de classe e o nível de método:

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

* A `BlobTrigger` propriedade do `Connection` atributo.
* O `StorageAccount` atributo aplicado ao mesmo `BlobTrigger` parâmetro que o atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#example) para obter detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `BlobTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de `blobTrigger`ser definido para. Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/d | Tem de `in`ser definido para. Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. As exceções são observadas na secção [de utilização.](#usage) |
|**nome** | n/d | O nome da variável que representa a bolha no código de função. |
|**caminho** | **BlobPath** |O [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) para monitorizar.  Pode ser um padrão de [nome blob.](#blob-name-patterns) |
|**conexão** | **Conexão** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se `connection` definir para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza `AzureWebJobsStorage`a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada .<br><br>A cadeia de ligação deve ser para uma conta de armazenamento geral, não para uma conta de [armazenamento Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda aos `context.bindings.<NAME>` dados `<NAME>` blob utilizando o valor definido na *função.json*.

# <a name="python"></a>[Python](#tab/python)

Aceda aos dados blob através do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o exemplo do [gatilho](#example) para obter detalhes.

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#example) para obter detalhes.

---

## <a name="blob-name-patterns"></a>Padrões de nome blob

Pode especificar um padrão de `path` nome blob na propriedade em `BlobTrigger` *função.json* ou no construtor de atributos. O padrão de nome pode ser um [filtro ou expressão de encadernação.](./functions-bindings-expressions-patterns.md) As seguintes secções fornecem exemplos.

### <a name="get-file-name-and-extension"></a>Obtenha o nome do ficheiro e a extensão

O exemplo que se segue mostra como se ligar ao nome do ficheiro blob e à extensão separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```

Se a bolha for denominada *original-Blob1.txt,* os valores `blobname` do código de função e variáveis `blobextension` são *originais-Blob1* e *txt*.

### <a name="filter-on-blob-name"></a>Filtro no nome blob

O exemplo seguinte dispara apenas em `input` bolhas no recipiente que começam com a corda "original":

```json
"path": "input/original-{name}",
```

Se o nome blob for *original-Blob1.txt,* o valor da `name` variável no código de função é `Blob1`.

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

Se a bolha for denominada `name` * {20140101}-soundfile.mp3,* o valor variável no código de função é *soundfile.mp3*.

## <a name="metadata"></a>Metadados

# <a name="c"></a>[C #](#tab/csharp)

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

O tempo de funcionamento das Funções Azure garante que nenhuma função de gatilho blob é chamada mais de uma vez para a mesma bolha nova ou atualizada. Para determinar se uma determinada versão blob foi processada, mantém *recibos blob*.

A Azure Functions armazena recibos blob num contentor chamado *azure-webjobs-hosts* na conta `AzureWebJobsStorage`de armazenamento Azure para a sua aplicação de funções (definida pela definição da aplicação). Um recibo blob tem as seguintes informações:

* A função desencadeada*&lt; *(" nome da aplicação de função>. Funções. *nome de função>", por exemplo: "MyFunctionApp.Functions.CopyBlob") &lt; *
* O nome do recipiente
* O tipo de bolha ("BlockBlob" ou "PageBlob")
* O nome blob
* O ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de uma bolha, elimine manualmente o recibo de bolha desse recipiente de anfitriões de *azure-webjobs.* Embora o reprocessamento possa não ocorrer imediatamente, é garantido que ocorra em um ponto posterior no tempo. Para reprocessar imediatamente, a blob *scaninfo* em *azure-webjobs-hosts/blobscaninfo* pode ser atualizada. Qualquer bolha com um último carimbo de `LatestScan` tempo modificado após a propriedade será digitalizada novamente.

## <a name="poison-blobs"></a>Bolhas venenosas

Quando uma função de gatilho blob falha para uma determinada bolha, as funções Azure retestam que funcionam um total de 5 vezes por padrão.

Se todas as 5 tentativas falharem, a Azure Functions adiciona uma mensagem a uma fila de armazenamento chamada *webjobs-blobtrigger-poison*. O número máximo de repetições é configurável. A mesma definição MaxDequeueCount é usada para manuseamento de bolhas venenosas e tratamento de mensagens de fila venenosa. A mensagem de fila para bolhas venenosas é um objeto JSON que contém as seguintes propriedades:

* FunçãoId (no nome da * &lt;função *de formato>. Funções. *nome da função>) &lt; *
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Utilização de moeda sintetária e de memória

O gatilho de bolha utiliza uma fila internamente, pelo que o número máximo de invocações de função simultânea é controlado pela configuração das [filas em host.json](functions-host-json.md#queues). As definições predefinidas limitam a moeda a 24 invocações. Este limite aplica-se separadamente a cada função que utiliza um gatilho de bolha.

[O plano de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita uma aplicação de função numa máquina virtual (VM) a 1,5 GB de memória. A memória é utilizada por cada instância de função de execução simultânea e pelas próprias funções. Se uma função desencadeada por bolhas carregar toda a bolha na memória, a memória máxima utilizada por essa função apenas para bolhas é de 24 * tamanho máximo de bolha. Por exemplo, uma aplicação de função com três funções desencadeadas por bolhas e as definições predefinidas teriam uma conmoeda máxima por VM de 3*24 = 72 invocações de função.

As funções JavaScript e Java carregam toda a bolha na memória, `string` `Byte[]`e as funções De C# fazem isso se se ligar a , ou POCO.

## <a name="polling"></a>Sondagem

As sondagens funcionam como um híbrido entre a inspeção dos registos e a realização de varreduras periódicas de contentores. As bolhas são digitalizadas em grupos de 10.000 de cada vez com um token de continuação usado entre intervalos.

> [!WARNING]
> Além disso, os registos de armazenamento são criados numa base de ["melhor esforço".](/rest/api/storageservices/About-Storage-Analytics-Logging) Não há garantias de que todos os eventos sejam capturados. Sob algumas condições, os registos podem ser perdidos.
> 
> Se necessitar de um processamento de bolha mais rápido ou fiável, considere criar uma mensagem de [fila](../storage/queues/storage-dotnet-how-to-use-queues.md) quando criar a bolha. Em seguida, use um gatilho de [fila](functions-bindings-storage-queue.md) em vez de um gatilho de bolha para processar a bolha. Outra opção é utilizar a Grelha de Eventos; ver o tutorial [Automate redimensionando imagens carregadas usando a Grelha de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Passos seguintes

- [Leia os dados de armazenamento de blob quando uma função funciona](./functions-bindings-storage-blob-input.md)
- [Escreva dados de armazenamento blob a partir de uma função](./functions-bindings-storage-blob-output.md)
