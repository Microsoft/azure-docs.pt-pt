---
title: Ligação de saída Azure Cosmos DB para funções 2.x
description: Aprenda a utilizar a ligação de saída azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277768"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Ligação de saída Azure Cosmos DB para funções Azure 2.x

A ligação de saída do Azure Cosmos DB permite-lhe escrever um novo documento para uma base de dados Azure Cosmos DB utilizando a API SQL.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Esta secção contém os seguintes exemplos:

* [Gatilho de fila, escrever um médico](#queue-trigger-write-one-doc-c)
* [Gatilho de fila, escreva docs usando IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Os exemplos referem-se a um tipo simples: `ToDoItem`

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

### <a name="queue-trigger-write-one-doc"></a>Gatilho de fila, escrever um médico

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que adiciona um documento a uma base de dados, utilizando dados fornecidos na mensagem a partir do armazenamento da fila.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando IAsyncCollector

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que adiciona uma recolha de documentos a uma base de dados, utilizando dados fornecidos numa mensagem de fila JSON.

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

* [Gatilho de fila, escrever um médico](#queue-trigger-write-one-doc-c-script)
* [Gatilho de fila, escreva docs usando IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Gatilho de fila, escrever um médico

O exemplo seguinte mostra uma ligação de saída Azure Cosmos DB num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função utiliza um encadernação de entrada de fila para uma fila que recebe JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos Azure Cosmos DB no seguinte formato para cada registo:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código de script C#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando IAsyncCollector

Para criar vários documentos, `IAsyncCollector<T>` `T` pode ligar-se ou `ICollector<T>` onde é um dos tipos suportados.

Este exemplo refere-se `ToDoItem` a um tipo simples:

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

Aqui está o ficheiro função.json:

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

Aqui está o código de script C#:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de saída Azure Cosmos DB num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função utiliza um encadernação de entrada de fila para uma fila que recebe JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos Azure Cosmos DB no seguinte formato para cada registo:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#configuration) explica estas propriedades.

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

# <a name="python"></a>[Pitão](#tab/python)

O exemplo seguinte demonstra como escrever um documento a uma base de dados Azure CosmosDB como a saída de uma função.

A definição de encadernação *type* é definida `cosmosDB`na *função.json* onde o tipo está definido para .

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

Para escrever na base de dados, `set` passe um objeto de documento ao método do parâmetro de base de dados.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Gatilho de fila, guardar mensagem para base de dados através do valor de retorno](#queue-trigger-save-message-to-database-via-return-value-java)
* [Gatilho HTTP, guarde um documento para a base de dados através do valor de devolução](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Gatilho HTTP, guarde um documento para base de dados via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [GATILHO HTTP, guarde vários documentos para base de dados via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Gatilho de fila, guardar mensagem para base de dados através do valor de retorno

O exemplo seguinte mostra uma função Java que adiciona um documento a uma base de dados com dados de uma mensagem no armazenamento da fila.

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Gatilho HTTP, guarde um documento para a base de dados através do valor de devolução

O exemplo seguinte mostra uma função Java ```@CosmosDBOutput``` cuja assinatura é ```String```anotada e tem valor de retorno de tipo . O documento JSON devolvido pela função será automaticamente escrito à correspondente coleção CosmosDB.

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

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Gatilho HTTP, guarde um documento para base de dados via OutputBinding

O exemplo seguinte mostra uma função Java que escreve ```OutputBinding<T>``` um documento para cosmosDB através de um parâmetro de saída. Neste exemplo, ```outputItem``` o parâmetro precisa de ser ```@CosmosDBOutput```anotado com , não a assinatura de função. A ```OutputBinding<T>``` utilização permite que a sua função aproveite a ligação para escrever o documento à CosmosDB, permitindo também devolver um valor diferente ao autor da função, como um documento JSON ou XML.

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

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>GATILHO HTTP, guarde vários documentos para base de dados via OutputBinding

O exemplo seguinte mostra uma função Java que escreve ```OutputBinding<T>``` vários documentos para cosmosDB através de um parâmetro de saída. Neste exemplo, ```outputItem``` o parâmetro é anotado ```@CosmosDBOutput```com , não a assinatura da função. O parâmetro de ```outputItem``` saída tem ```ToDoItem``` uma lista de objetos como o tipo de parâmetro de modelo. A ```OutputBinding<T>``` utilização permite que a sua função aproveite a ligação para escrever os documentos à CosmosDB, permitindo também devolver um valor diferente ao autor da função, como um documento JSON ou XML.

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

Na biblioteca de [tempo de funcionamento](/java/api/overview/azure/functions/runtime)de Java, utilize a `@CosmosDBOutput` anotação em parâmetros que serão escritos ao Cosmos DB.  O tipo de parâmetro de ```OutputBinding<T>```anotação deve ser, onde T é um tipo java nativo ou um POJO.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da coleção. Para obter informações sobre essas definições e outras propriedades que possa configurar, consulte [A Saída - configuração](#configuration). Aqui está `CosmosDB` um exemplo de atributo numa assinatura de método:

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

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A `CosmosDBOutput` anotação está disponível para escrever dados para cosmos DB. Pode aplicar a anotação à função ou a um parâmetro de função individual. Quando utilizado no método da função, o valor de retorno da função é o que é escrito para Cosmos DB. Se utilizar a anotação com um parâmetro, o tipo de `OutputBinding<T>` parâmetro `T` deve ser declarado como um tipo de Java nativo ou um POJO.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `CosmosDB` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     | n/d | Tem de `cosmosDB`ser definido para.        |
|**direção**     | n/d | Tem de `out`ser definido para.         |
|**nome**     | n/d | Nome do parâmetro de ligação que representa o documento na função.  |
|**nome da base de dados** | **DatabaseName**|A base de dados que contém a coleção onde o documento é criado.     |
|**coleçãoNome** |**CollectionName**  | O nome da coleção onde o documento é criado. |
|**criarIfNotexists**  |**Criarifnotexist**    | Um valor booleano para indicar se a coleção é criada quando não existe. O padrão é *falso* porque novas coleções são criadas com entrada reservada, que tem implicações de custos. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partiçãoChave**|**PartitionKey** |Quando `CreateIfNotExists` é verdade, define o caminho-chave da partilha para a coleção criada.|
|**coleçãoThroughput**|**ColeçãoThroughput**| Quando `CreateIfNotExists` é verdade, define a [entrada](../cosmos-db/set-throughput.md) da coleção criada.|
|**conexãoStringSetting**    |**Definição de cordas de ligação** |O nome da configuração da aplicação que contém a sua cadeia de ligação Azure Cosmos DB.        |
|**preferredLocalizações**| **Localizações Preferenciais**| (Opcional) Define localizações preferenciais (regiões) para contas de base de dados geo-replicadas no serviço Azure Cosmos DB. Os valores devem ser separados pela vírposta. Por exemplo, "Leste dos EUA, Centro-Sul dos EUA, Norte da Europa". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| (Opcional) Quando `true` definido, `PreferredLocations`pode aproveitar as [escritas multi-regiões](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) no serviço Azure Cosmos DB. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

Por predefinição, quando escreve para o parâmetro de saída na sua função, é criado um documento na sua base de dados. Este documento tem um GUID gerado automaticamente como id do documento. Pode especificar o documento de identificação do `id` documento de saída especificando a propriedade no objeto JSON passada para o parâmetro de saída.

> [!Note]
> Quando especifica a identificação de um documento existente, este é substituído pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| CosmosDB | [Códigos de Erro CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>configurações host.json

Esta secção descreve as definições de configuração global disponíveis para esta ligação na versão 2.x. Para obter mais informações sobre as configurações globais de configuração na versão 2.x, consulte a [referência host.json para a versão 2.x das Funções Azure](functions-host-json.md).

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

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|GatewayMode|Gateway|O modo de ligação utilizado pela função ao ligar-se ao serviço Azure Cosmos DB. As `Direct` opções são e`Gateway`|
|Protocolo|Https|O protocolo de ligação utilizado pela função quando a ligação ao serviço Azure Cosmos DB.  Leia [aqui para uma explicação de ambos os modos](../cosmos-db/performance-tips.md#networking)|
|arrendamentoPrefix|n/d|Alugar prefixo para usar em todas as funções numa aplicação.|

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando um documento Azure Cosmos DB é criado ou modificado (Gatilho)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leia um documento Azure Cosmos DB (encadernação de entrada)](./functions-bindings-cosmosdb-v2-input.md)