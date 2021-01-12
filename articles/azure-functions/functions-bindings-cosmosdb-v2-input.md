---
title: Ligação de entrada DB Azure Cosmos para funções 2.x e superior
description: Aprenda a utilizar a ligação de entrada DB Azure Cosmos em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 49762b1844aec85ff55ae2a16243a231414b263f
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071583"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Ligação de entrada DB Azure Cosmos para Funções Azure 2.x e superior

O enlace de entrada do Azure Cosmos DB utiliza a API do SQL para obter um ou mais documentos do Azure Cosmos DB e passa-os para o parâmetro de entrada da função. Os parâmetros de consulta ou o ID do documento podem ser determinados com base no acionador que invoca a função.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Se a recolha for [dividida,](../cosmos-db/partitioning-overview.md#logical-partitions)as operações de procura também devem especificar o valor da chave de partição.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Esta secção contém os seguintes exemplos:

* [Gatilho da fila, procure o ID de JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP trigger, procure o ID a partir da cadeia de consulta](#http-trigger-look-up-id-from-query-string-c)
* [HTTP trigger, procure o ID a partir de dados de rota](#http-trigger-look-up-id-from-route-data-c)
* [Detonador HTTP, procure o ID a partir de dados de rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Detonador HTTP, obtenha vários docs, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Detonador HTTP, obtenha vários docs, utilizando o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Os exemplos referem-se a um `ToDoItem` tipo simples:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, procure o ID de JSON 

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por uma mensagem de fila que contém um objeto JSON. O gatilho da fila analisa o JSON num objeto do `ToDoItemLookup` tipo, que contém o valor de ID e partição para olhar para cima. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP trigger, procure o ID a partir da cadeia de consulta

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

>[!NOTE]
>O parâmetro de cadeia de consulta HTTP é sensível a maiíssícolas.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Detonador HTTP, procure o ID a partir de dados de rota, usando SqlQuery

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o ID para procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

O exemplo mostra como usar uma expressão de ligação no `SqlQuery` parâmetro. Pode passar os dados de rota para o `SqlQuery` parâmetro como mostrado, mas atualmente [não pode passar valores de cadeia de consulta](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Se precisar de consultar apenas o ID, recomenda-se a utilização de um look up, como os [exemplos anteriores,](#http-trigger-look-up-id-from-query-string-c)pois consumirá menos [unidades de pedido](../cosmos-db/request-units.md). As operações de leitura de pontos (GET) são [mais eficientes](../cosmos-db/optimize-cost-reads-writes.md) do que as consultas por ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Detonador HTTP, obtenha vários docs, usando SqlQuery

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. A consulta é especificada na `SqlQuery` propriedade do atributo.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Detonador HTTP, obtenha vários docs, utilizando o DocumentClient

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. O código utiliza uma `DocumentClient` instância fornecida pela ligação Azure Cosmos DB para ler uma lista de documentos. O `DocumentClient` caso também pode ser utilizado para operações de escrita.

> [!NOTE]
> Também pode utilizar a interface [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet&preserve-view=true) para facilitar o teste.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Esta secção contém os seguintes exemplos:

* [Gatilho da fila, procure o ID da corda](#queue-trigger-look-up-id-from-string-c-script)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP trigger, procure o ID a partir da cadeia de consulta](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP trigger, procure o ID a partir de dados de rota](#http-trigger-look-up-id-from-route-data-c-script)
* [Detonador HTTP, obtenha vários docs, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Detonador HTTP, obtenha vários docs, utilizando o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Os exemplos de gatilho HTTP referem-se a um `ToDoItem` tipo simples:

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

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Gatilho da fila, procure o ID da corda

O exemplo a seguir mostra uma ligação de entrada Cosmos DB numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código do guião C:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho de fila, obter vários docs, usando SqlQuery

O exemplo a seguir mostra uma ligação de entrada DB Azure Cosmos numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função recupera vários documentos especificados por uma consulta SQL, utilizando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho da fila fornece um parâmetro `departmentId` . Uma mensagem de fila `{ "departmentId" : "Finance" }` devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código do guião C:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP trigger, procure o ID a partir da cadeia de consulta

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código do guião C:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código do guião C:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Detonador HTTP, obtenha vários docs, usando SqlQuery

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. A consulta é especificada na `SqlQuery` propriedade do atributo.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Aqui está o código do guião C:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Detonador HTTP, obtenha vários docs, utilizando o DocumentClient

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. O código utiliza uma `DocumentClient` instância fornecida pela ligação Azure Cosmos DB para ler uma lista de documentos. O `DocumentClient` caso também pode ser utilizado para operações de escrita.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Aqui está o código do guião C:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="java"></a>[Java](#tab/java)

Esta secção contém os seguintes exemplos:

* [HTTP trigger, procure o ID a partir da cadeia de consulta - Parâmetro de corda](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Http trigger, procure o ID a partir da cadeia de consulta - parâmetro POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP trigger, procure o ID a partir de dados de rota](#http-trigger-look-up-id-from-route-data-java)
* [Detonador HTTP, procure o ID a partir de dados de rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Detonador HTTP, obtenha vários docs a partir de dados de rota, usando SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Os exemplos referem-se a um `ToDoItem` tipo simples:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP trigger, procure o ID a partir da cadeia de consulta - Parâmetro de corda

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um documento da base de dados e recolha especificadas, em forma de corda.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

Na biblioteca de [funções java,](/java/api/overview/azure/functions/runtime)use a `@CosmosDBInput` anotação em parâmetros de função cujo valor viria do Cosmos DB.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Http trigger, procure o ID a partir da cadeia de consulta - parâmetro POJO

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de ID e partição para procurar. Esse valor de identificação e chave de partição usado para recuperar um documento da base de dados e recolha especificadas. O documento é então convertido para uma instância do ```ToDoItem``` POJO anteriormente criado, e passado como um argumento para a função.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza um parâmetro de rota para especificar o valor da chave de id e partição para procurar. Esse valor de identificação e chave de partição são usados para recuperar um documento da base de dados e recolha especificadas, devolvendo-o como ```Optional<String>``` um .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Detonador HTTP, procure o ID a partir de dados de rota, usando SqlQuery

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza um parâmetro de rota para especificar o ID para procurar. Esse ID é utilizado para recuperar um documento da base de dados e recolha especificadas, convertendo o resultado definido para um ```ToDoItem[]``` , uma vez que muitos documentos podem ser devolvidos, dependendo dos critérios de consulta.

> [!NOTE]
> Se precisar de consultar apenas o ID, recomenda-se a utilização de um look up, como os [exemplos anteriores,](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)pois consumirá menos [unidades de pedido](../cosmos-db/request-units.md). As operações de leitura de pontos (GET) são [mais eficientes](../cosmos-db/optimize-cost-reads-writes.md) do que as consultas por ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Detonador HTTP, obtenha vários docs a partir de dados de rota, usando SqlQuery

O exemplo a seguir mostra uma função Java que recupera vários documentos. A função é desencadeada por um pedido HTTP que utiliza um parâmetro de rota ```desc``` para especificar a cadeia a procurar no ```description``` campo. O termo de pesquisa é utilizado para recuperar uma recolha de documentos da base de dados e recolha especificadas, convertendo o resultado definido para um ```ToDoItem[]``` e passando-o como argumento para a função.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Esta secção contém os seguintes exemplos que lêem um único documento especificando um valor de ID de várias fontes:

* [Gatilho da fila, procure o ID de JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP trigger, procure o ID a partir da cadeia de consulta](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP trigger, procure o ID a partir de dados de rota](#http-trigger-look-up-id-from-route-data-javascript)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, procure o ID de JSON

O exemplo a seguir mostra uma ligação de entrada de Cosmos DB numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP trigger, procure o ID a partir da cadeia de consulta

O exemplo a seguir mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho de fila, obter vários docs, usando SqlQuery

O exemplo a seguir mostra uma ligação de entrada DB Azure Cosmos numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função recupera vários documentos especificados por uma consulta SQL, utilizando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho da fila fornece um parâmetro `departmentId` . Uma mensagem de fila `{ "departmentId" : "Finance" }` devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {
  var documents = context.bindings.documents;
  for (var i = 0; i < documents.length; i++) {
    var document = documents[i];
    // operate on each document
  }
  context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [Gatilho da fila, procure o ID de JSON](#queue-trigger-look-up-id-from-json-ps)
* [HTTP trigger, procure o ID a partir da cadeia de consulta](#http-trigger-id-query-string-ps)
* [HTTP trigger, procure o ID a partir de dados de rota](#http-trigger-id-route-data-ps)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-multiple-docs-sqlquery-ps)

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, procure o ID de JSON

O exemplo a seguir demonstra como ler e atualizar um único documento cosmos DB. O identificador único do documento é fornecido através do valor JSON numa mensagem de fila.

A ligação de entrada Cosmos DB é listada em primeiro lugar na lista de encadernações encontradas no ficheiro de configuração da função _(function.jsem_).

<a name="queue-trigger-look-up-id-from-json-ps"></a>

```json
{
  "name": "InputDocumentIn",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger_payload_property}",
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "in"
},
{
  "name": "InputDocumentOut",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": false,
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "out"
}
```

O _ficheirorun.ps1_ tem o código PowerShell que lê o documento de entrada e as alterações de saídas.

```powershell
param($QueueItem, $InputDocumentIn, $TriggerMetadata) 

$Document = $InputDocumentIn 
$Document.text = 'This was updated!' 

Push-OutputBinding -Name InputDocumentOut -Value $Document  
```

<a name="http-trigger-id-query-string-ps"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP trigger, procure o ID a partir da cadeia de consulta

O exemplo a seguir demonstra como ler e atualizar um único documento Cosmos DB a partir de uma API web. O identificador único do documento é fornecido através de um parâmetro de consulta a partir do pedido HTTP, conforme definido na propriedade da `"Id": "{Query.Id}"` ligação.

A ligação de entrada Cosmos DB é listada em primeiro lugar na lista de encadernações encontradas no ficheiro de configuração da função _(function.jsem_).

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{Query.id}", 
      "PartitionKey": "{Query.partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ] 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    },
  ], 
  "disabled": false 
} 
```
  
O ficheiro _run.ps1_ tem o código PowerShell que lê o documento de entrada e as alterações de saídas.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 

    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
 
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
}
```

<a name="http-trigger-id-route-data-ps"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir demonstra como ler e atualizar um único documento Cosmos DB a partir de uma API web. O identificador único do documento é fornecido através de um parâmetro de rota. O parâmetro de rota é definido na propriedade de ligação do pedido HTTP `route` e referenciado na propriedade de ligação Cosmos `"Id": "{Id}"` DB.

A ligação de entrada Cosmos DB é listada em primeiro lugar na lista de encadernações encontradas no ficheiro de configuração da função _(function.jsem_).

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{id}", 
      "PartitionKey": "{partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ], 
      "route": "todoitems/{partitionKeyValue}/{id}" 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    }
  ], 
  "disabled": false 
} 
```

O ficheiro _run.ps1_ tem o código PowerShell que lê o documento de entrada e as alterações de saídas.

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 
    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
  
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
} 
```

<a name="queue-trigger-multiple-docs-sqlquery-ps"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho de fila, obter vários docs, usando SqlQuery

O exemplo a seguir demonstra como ler vários documentos da Cosmos DB. O ficheiro de configuração da função _(function.jsem)_ define as propriedades de encadernação, que inclui a `sqlQuery` . A declaração SQL fornecida à `sqlQuery` propriedade seleciona o conjunto de documentos fornecidos à função.

```json
{ 
  "name": "Documents", 
  "type": "cosmosDB", 
  "direction": "in", 
  "databaseName": "MyDb", 
  "collectionName": "MyCollection", 
  "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}", 
  "connectionStringSetting": "CosmosDBConnection" 
} 
```

O ficheiro _run1.ps_ tem o código PowerShell que lê os documentos que chegam.

```powershell
param($QueueItem, $Documents, $TriggerMetadata) 

foreach ($Document in $Documents) { 
    # operate on each document 
} 
```

# <a name="python"></a>[Python](#tab/python)

Esta secção contém os seguintes exemplos que lêem um único documento especificando um valor de ID de várias fontes:

* [Gatilho da fila, procure o ID de JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP trigger, procure o ID a partir da cadeia de consulta](#http-trigger-look-up-id-from-query-string-python)
* [HTTP trigger, procure o ID a partir de dados de rota](#http-trigger-look-up-id-from-route-data-python)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, procure o ID de JSON

O exemplo a seguir mostra uma ligação de entrada cosmos DB numa *function.jsem* ficheiro e uma [função Python](functions-reference-python.md) que usa a ligação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código Python:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP trigger, procure o ID a partir da cadeia de consulta

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de ID e chave de partição são usados para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

Aqui está a *function.jsarquivada:*

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho de fila, obter vários docs, usando SqlQuery

O exemplo a seguir mostra uma ligação de entrada DB Azure Cosmos numa *function.jsem* ficheiro e uma [função Python](functions-reference-python.md) que utiliza a ligação. A função recupera vários documentos especificados por uma consulta SQL, utilizando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho da fila fornece um parâmetro `departmentId` . Uma mensagem de fila `{ "departmentId" : "Finance" }` devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da recolha. Para obter informações sobre as configurações e outras propriedades que possa configurar, consulte [a seguinte secção de configuração](#configuration).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A partir da [biblioteca de funções java,](/java/api/overview/azure/functions/runtime)use a `@CosmosDBOutput` anotação em parâmetros que escrevem para Cosmos DB. O tipo de parâmetro de anotação deve ser `OutputBinding<T>` , onde é um tipo java nativo ou um `T` POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `CosmosDB` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     | n/a | Deve ser definido para `cosmosDB` .        |
|**direção**     | n/a | Deve ser definido para `in` .         |
|**name**     | n/a | Nome do parâmetro de ligação que representa o documento na função.  |
|**base de dados Nome** |**DatabaseName** |A base de dados que contém o documento.        |
|**coleçãoName** |**CollectionName** | O nome da coleção que contém o documento. |
|**id**    | **ID** | A identificação do documento para recuperar. Esta propriedade suporta [expressões vinculativas.](./functions-bindings-expressions-patterns.md) Não desconte as `id` propriedades e **sqlQuery.** Se não definir nenhum dos dois, toda a coleção será recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta Azure Cosmos DB SQL usada para recuperar vários documentos. A propriedade suporta encadernações de tempo de execução, como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}` . Não coloque as `id` propriedades e `sqlQuery` as propriedades. Se não definir nenhum dos dois, toda a coleção será recuperada.|
|**conexãoStringSetting**     |**ConexãoStringSetting**|O nome da definição da aplicação que contém a sua cadeia de ligação Azure Cosmos DB. |
|**partitionKey**|**PartitionKey**|Especifica o valor da chave de partição para a procura. Pode incluir parâmetros de ligação. É necessário para procurar em coleções [divididas.](../cosmos-db/partitioning-overview.md#logical-partitions)|
|**preferênciasLoções**| **PreferênciasLocações**| (Opcional) Define localizações preferenciais (regiões) para contas de base de dados geo-replicadas no serviço DB Azure Cosmos. Os valores devem ser separados por vírgula. Por exemplo, "Leste dos EUA, Centro Sul dos EUA, Norte da Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Quando a função sai com sucesso, quaisquer alterações efetuadas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistiu.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Quando a função sai com sucesso, quaisquer alterações efetuadas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistiu.

# <a name="java"></a>[Java](#tab/java)

A partir da [biblioteca de funções Java,](/java/api/overview/azure/functions/runtime)a [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) anotação expõe os dados do Cosmos DB à função. Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

As atualizações não são efetuadas automaticamente após a saída da função. Em vez disso, use `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` faça atualizações. Consulte o [exemplo JavaScript](#example) para obter mais detalhes.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

As atualizações aos documentos não são efetuadas automaticamente após a saída da função. Para atualizar os documentos numa função utilize uma [ligação de saída](./functions-bindings-cosmosdb-v2-input.md). Consulte o [exemplo PowerShell](#example) para obter mais detalhes.

# <a name="python"></a>[Python](#tab/python)

Os dados são disponibilizados à função através de um `DocumentList` parâmetro. As alterações introduzidas no documento não são automaticamente persistiu.

---

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando um documento DB Azure Cosmos é criado ou modificado (Gatilho)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Guardar alterações num documento DB do Azure Cosmos (ligação à saída)](./functions-bindings-cosmosdb-v2-output.md)