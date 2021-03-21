---
title: Ligação de saída DB Azure Cosmos para funções 2.x e superior
description: Aprenda a utilizar a ligação de saída DB Azure Cosmos em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 779b66412319ec8422977a7e56570a4d16f89aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071549"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Ligação de saída DB Azure Cosmos para Funções Azure 2.x e superior

A ligação de saída DB da Azure Cosmos permite-lhe escrever um novo documento numa base de dados DB da Azure Cosmos utilizando a API SQL.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Esta secção contém os seguintes exemplos:

* [Gatilho de fila, escreva um doc](#queue-trigger-write-one-doc-c)
* [Gatilho de fila, escreva docs usando iAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Os exemplos referem-se a um `ToDoItem` tipo simples:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Gatilho de fila, escreva um doc

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona um documento a uma base de dados, utilizando dados fornecidos na mensagem do armazenamento da fila.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando iAsyncCollector

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona uma recolha de documentos a uma base de dados, utilizando dados fornecidos numa mensagem de fila JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Esta secção contém os seguintes exemplos:

* [Gatilho de fila, escreva um doc](#queue-trigger-write-one-doc-c-script)
* [Gatilho de fila, escreva docs usando iAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Gatilho de fila, escreva um doc

O exemplo a seguir mostra uma ligação de saída DB Azure Cosmos numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função utiliza uma ligação de entrada de fila para uma fila que recebe JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos DB Azure Cosmos no seguinte formato para cada registo:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código do guião C:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando iAsyncCollector

Para criar vários documentos, pode ligar-se `ICollector<T>` ou onde está um dos tipos `IAsyncCollector<T>` `T` suportados.

Este exemplo refere-se a um `ToDoItem` tipo simples:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Aqui está a function.jsarquivada:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está o código do guião C:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

* [Gatilho da fila, guardar mensagem para base de dados através do valor de devolução](#queue-trigger-save-message-to-database-via-return-value-java)
* [Detonador HTTP, guarde um documento para a base de dados através do valor de devolução](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Detonador HTTP, guarde um documento para a base de dados via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Detonador HTTP, guarde vários documentos para a base de dados via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Gatilho da fila, guardar mensagem para base de dados através do valor de devolução

O exemplo a seguir mostra uma função Java que adiciona um documento a uma base de dados com dados de uma mensagem no armazenamento da fila.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Detonador HTTP, guarde um documento para a base de dados através do valor de devolução

O exemplo a seguir mostra uma função Java cuja assinatura é anotada ```@CosmosDBOutput``` e tem valor de retorno do tipo ```String``` . O documento JSON devolvido pela função será automaticamente escrito para a correspondente coleção CosmosDB.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Detonador HTTP, guarde um documento para a base de dados via OutputBinding

O exemplo a seguir mostra uma função Java que escreve um documento ao CosmosDB através de um ```OutputBinding<T>``` parâmetro de saída. Neste exemplo, o ```outputItem``` parâmetro tem de ser anotado com , não com a assinatura da ```@CosmosDBOutput``` função. A utilização ```OutputBinding<T>``` permite que a sua função aproveite a vinculação para escrever o documento ao CosmosDB, permitindo também devolver um valor diferente ao chamador da função, como um documento JSON ou XML.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Detonador HTTP, guarde vários documentos para a base de dados via OutputBinding

O exemplo a seguir mostra uma função Java que escreve vários documentos para o CosmosDB através de um ```OutputBinding<T>``` parâmetro de saída. Neste exemplo, o ```outputItem``` parâmetro é anotado ```@CosmosDBOutput``` com, não a assinatura da função. O parâmetro de ```outputItem``` saída, tem uma lista de ```ToDoItem``` objetos como o seu tipo de parâmetro de modelo. A utilização ```OutputBinding<T>``` permite que a sua função aproveite a vinculação para escrever os documentos para cosmosDB, permitindo também devolver um valor diferente ao chamador da função, como um documento JSON ou XML.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

Na biblioteca de [funções java,](/java/api/overview/azure/functions/runtime)use a `@CosmosDBOutput` anotação em parâmetros que serão escritos para Cosmos DB.  O tipo de parâmetro de anotação deve ser ```OutputBinding<T>``` , onde T é um tipo java nativo ou um POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação de saída DB Azure Cosmos numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função utiliza uma ligação de entrada de fila para uma fila que recebe JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos DB Azure Cosmos no seguinte formato para cada registo:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

Para inserir a granel, primeiro, os objetos e, em seguida, executar a função de stringify. Aqui está o código JavaScript:

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra como escrever dados para a Cosmos DB usando uma ligação de saída. A ligação é declarada no ficheiro de configuração da função _(functions.jsem),_ e retirar dados de uma mensagem de fila e escrever para um documento de Cosmos DB.

```json
{ 
  "name": "EmployeeDocument",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "direction": "out" 
} 
```

No ficheiro _run.ps1,_ o objeto devolvido da função é mapeado para um `EmployeeDocument` objeto, que é persistido na base de dados.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name EmployeeDocument -Value @{ 
    id = $QueueItem.name + '-' + $QueueItem.employeeId 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como escrever um documento para uma base de dados Azure CosmosDB como saída de uma função.

A definição de ligação é definida em *function.jsno* local onde *o tipo* é definido para `cosmosDB` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Para escrever na base de dados, passe um objeto de documento para o `set` método do parâmetro da base de dados.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests)

O construtor do atributo tem o nome da base de dados e o nome da recolha. Para obter informações sobre as configurações e outras propriedades que possa configurar, consulte [saída - configuração](#configuration). Aqui está um `CosmosDB` exemplo de atributo numa assinatura de método:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A `CosmosDBOutput` anotação está disponível para escrever dados para cosmos DB. Pode aplicar a anotação à função ou a um parâmetro de função individual. Quando utilizado no método de função, o valor de retorno da função é o que é escrito para Cosmos DB. Se utilizar a anotação com um parâmetro, o tipo do parâmetro deve ser declarado como `OutputBinding<T>` um tipo de Java nativo ou um `T` POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `CosmosDB` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo**     | n/a | Deve ser definido para `cosmosDB` .        |
|**direção**     | n/a | Deve ser definido para `out` .         |
|**nome**     | n/a | Nome do parâmetro de ligação que representa o documento na função.  |
|**base de dados Nome** | **DatabaseName**|A base de dados que contém a coleção onde o documento é criado.     |
|**coleçãoName** |**CollectionName**  | O nome da coleção onde o documento é criado. |
|**criarIfNotExists**  |**CriarExistsIfNotExists**    | Um valor booleano para indicar se a coleção é criada quando não existe. O padrão é *falso* porque novas coleções são criadas com produção reservada, o que tem implicações de custos. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Quando `CreateIfNotExists` é verdade, define o caminho chave da partição para a coleção criada.|
|**coleçãoThroughput**|**ColeçãoThroughput**| Quando `CreateIfNotExists` é verdade, define o [rendimento](../cosmos-db/set-throughput.md) da coleção criada.|
|**conexãoStringSetting**    |**ConexãoStringSetting** |O nome da definição da aplicação que contém a sua cadeia de ligação Azure Cosmos DB.        |
|**preferênciasLoções**| **PreferênciasLocações**| (Opcional) Define localizações preferenciais (regiões) para contas de base de dados geo-replicadas no serviço DB Azure Cosmos. Os valores devem ser separados por vírgula. Por exemplo, "Leste dos EUA, Centro Sul dos EUA, Norte da Europa". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| (Opcional) Quando definido `true` para, juntamente `PreferredLocations` com, pode aproveitar [as gravações multi-regiões](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) no serviço DB Azure Cosmos. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

Por predefinição, quando escreve para o parâmetro de saída na sua função, é criado um documento na sua base de dados. Este documento tem um GUID gerado automaticamente como o ID do documento. Pode especificar o documento ID do documento de saída especificando a `id` propriedade no objeto JSON passada para o parâmetro de saída.

> [!Note]
> Quando especifica o ID de um documento existente, este é substituído pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Cosmos DB | [Códigos de erro cosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.jsnas definições

Esta secção descreve as definições de configuração global disponíveis para esta ligação na versão 2.x. Para obter mais informações sobre as configurações globais na versão 2.x, consulte [host.jsem referência para a versão 2.x das Funções Azure](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------|
|GatewayMode|Gateway|O modo de ligação utilizado pela função ao ligar-se ao serviço DB Azure Cosmos. As opções são `Direct` e `Gateway`|
|Protocolo|Https|O protocolo de ligação utilizado pela função quando ligado ao serviço DB Azure Cosmos.  Leia [aqui para uma explicação de ambos os modos](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/a|Alugar prefixo para usar em todas as funções numa aplicação.|

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando um documento DB Azure Cosmos é criado ou modificado (Gatilho)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leia um documento DB da Azure Cosmos (ligação à entrada)](./functions-bindings-cosmosdb-v2-input.md)
