---
title: Ligação de entrada Azure Cosmos DB para funções 2.x
description: Aprenda a utilizar a ligação de entrada Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: eabcf40e28927919215979ccc46fa029d19adbfe
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943429"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Ligação de entrada Azure Cosmos DB para funções Azure 2.x

O enlace de entrada do Azure Cosmos DB utiliza a API de SQL para obter um ou mais documentos do Azure Cosmos DB e os passa para o parâmetro de entrada da função. Os parâmetros de consulta ou ID do documento podem ser determinados com base no acionador que invoca a função.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Se a recolha estiver [dividida,](../cosmos-db/partition-data.md#logical-partitions)as operações de procuração devem também especificar o valor-chave da partilha.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Esta secção contém os exemplos seguintes:

* [Gatilho de fila, procure identificação da JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP gatilho, procure o ID da corda de consulta](#http-trigger-look-up-id-from-query-string-c)
* [HTTP gatilho, procure id a partir de dados de rota](#http-trigger-look-up-id-from-route-data-c)
* [GATILHO HTTP, procure id a partir de dados de rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [GATILHO HTTP, obtenha vários docs, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [GATILHO HTTP, obtenha vários docs, usando o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Os exemplos referem-se a um tipo de `ToDoItem` simples:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho de fila, procure identificação da JSON 

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é acionada por uma mensagem de fila que contém um objeto JSON. O gatilho da fila analisa o JSON num objeto de tipo `ToDoItemLookup`, que contém o id e o valor da chave da divisória para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP gatilho, procure o ID da corda de consulta

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de identificação e divisória para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

>[!NOTE]
>O parâmetro de corda http consulta é sensível a casos.
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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido http que utiliza dados de rota para especificar o valor da chave de id e partição para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>GATILHO HTTP, procure id a partir de dados de rota, usando SqlQuery

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza encaminhar os dados para especificar o ID para procurar. Esse ID é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificadas.

O exemplo mostra como usar uma expressão de ligação no parâmetro `SqlQuery`. Pode passar os dados da rota para o parâmetro `SqlQuery`, como mostrado, mas atualmente [não pode passar os valores](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)das cordas de consulta.

> [!NOTE]
> Se precisar de consultar apenas o ID, recomenda-se utilizar um look up, como os [exemplos anteriores](#http-trigger-look-up-id-from-query-string-c), uma vez que consumirá menos [unidades](../cosmos-db/request-units.md)de pedido . As operações de leitura de pontos (GET) são [mais eficientes](../cosmos-db/optimize-cost-queries.md) do que as consultas por ID.
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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>GATILHO HTTP, obtenha vários docs, usando SqlQuery

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é acionada por um pedido HTTP. A consulta é especificada na propriedade de atributo `SqlQuery`.

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>GATILHO HTTP, obtenha vários docs, usando o DocumentClient

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é acionada por um pedido HTTP. O código utiliza uma `DocumentClient` instância fornecida pelo Azure Cosmos DB vinculando-se a ler uma lista de documentos. A `DocumentClient` instância também poderia ser utilizada para as operações de escrita.

> [!NOTE]
> Também pode utilizar a interface [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) para facilitar o teste.

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

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Esta secção contém os exemplos seguintes:

* [Gatilho de fila, procure identificação da corda](#queue-trigger-look-up-id-from-string-c-script)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP gatilho, procure o ID da corda de consulta](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP gatilho, procure id a partir de dados de rota](#http-trigger-look-up-id-from-route-data-c-script)
* [GATILHO HTTP, obtenha vários docs, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [GATILHO HTTP, obtenha vários docs, usando o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Os exemplos do gatilho HTTP referem-se a um tipo simples `ToDoItem`:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Gatilho de fila, procure identificação da corda

O exemplo seguinte mostra uma ligação de entrada Cosmos DB num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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
A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código de script do c#:

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

O exemplo seguinte mostra uma ligação de entrada Azure Cosmos DB num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação. A função obtém vários documentos especificados por uma consulta SQL, com um acionador de fila para personalizar os parâmetros de consulta.

O gatilho da fila fornece um parâmetro `departmentId`. Uma mensagem de `{ "departmentId" : "Finance" }` devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código de script do c#:

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP gatilho, procure o ID da corda de consulta

O exemplo seguinte mostra uma [ C# função de script](functions-reference-csharp.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de identificação e divisória para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

Aqui está o ficheiro *função.json:*

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

Aqui está o código de script do c#:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo seguinte mostra uma [ C# função de script](functions-reference-csharp.md) que recupera um único documento. A função é desencadeada por um pedido http que utiliza dados de rota para especificar o valor da chave de id e partição para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

Aqui está o ficheiro *função.json:*

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

Aqui está o código de script do c#:

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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>GATILHO HTTP, obtenha vários docs, usando SqlQuery

O exemplo seguinte mostra uma [ C# função de script](functions-reference-csharp.md) que recupera uma lista de documentos. A função é acionada por um pedido HTTP. A consulta é especificada na propriedade de atributo `SqlQuery`.

Aqui está o ficheiro *função.json:*

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

Aqui está o código de script do c#:

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>GATILHO HTTP, obtenha vários docs, usando o DocumentClient

O exemplo seguinte mostra uma [ C# função de script](functions-reference-csharp.md) que recupera uma lista de documentos. A função é acionada por um pedido HTTP. O código utiliza uma `DocumentClient` instância fornecida pelo Azure Cosmos DB vinculando-se a ler uma lista de documentos. A `DocumentClient` instância também poderia ser utilizada para as operações de escrita.

Aqui está o ficheiro *função.json:*

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

Aqui está o código de script do c#:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Esta secção contém os seguintes exemplos que lêem um único documento especificando um valor de id de várias fontes:

* [Gatilho de fila, procure identificação da JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP gatilho, procure o ID da corda de consulta](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP gatilho, procure id a partir de dados de rota](#http-trigger-look-up-id-from-route-data-javascript)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho de fila, procure identificação da JSON

O exemplo seguinte mostra uma ligação de entrada Cosmos DB num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#configuration) explica estas propriedades.

Eis o código JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP gatilho, procure o ID da corda de consulta

O exemplo seguinte mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de identificação e divisória para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

Aqui está o ficheiro *função.json:*

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

Eis o código JavaScript:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo seguinte mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é desencadeada por um pedido http que utiliza dados de rota para especificar o valor da chave de id e partição para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

Aqui está o ficheiro *função.json:*

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
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Eis o código JavaScript:

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

O exemplo seguinte mostra uma ligação de entrada Azure Cosmos DB num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função obtém vários documentos especificados por uma consulta SQL, com um acionador de fila para personalizar os parâmetros de consulta.

O gatilho da fila fornece um parâmetro `departmentId`. Uma mensagem de `{ "departmentId" : "Finance" }` devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#configuration) explica estas propriedades.

Eis o código JavaScript:

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

# <a name="python"></a>[python](#tab/python)

Esta secção contém os seguintes exemplos que lêem um único documento especificando um valor de id de várias fontes:

* [Gatilho de fila, procure identificação da JSON](#queue-trigger-look-up-id-from-json-python)
* [HTTP gatilho, procure o ID da corda de consulta](#http-trigger-look-up-id-from-query-string-python)
* [HTTP gatilho, procure id a partir de dados de rota](#http-trigger-look-up-id-from-route-data-python)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho de fila, procure identificação da JSON

O exemplo seguinte mostra uma ligação de entrada Cosmos DB num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que usa a ligação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#configuration) explica estas propriedades.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP gatilho, procure o ID da corda de consulta

O exemplo que se segue mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de identificação e divisória para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

Aqui está o ficheiro *função.json:*

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
  "disabled": true,
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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo que se segue mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é desencadeada por um pedido http que utiliza dados de rota para especificar o valor da chave de id e partição para procurar. Esse valor chave de identificação e partição é usado para recuperar um documento `ToDoItem` da base de dados e recolha especificada.

Aqui está o ficheiro *função.json:*

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

O exemplo seguinte mostra uma ligação de entrada Azure Cosmos DB num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que utiliza a ligação. A função obtém vários documentos especificados por uma consulta SQL, com um acionador de fila para personalizar os parâmetros de consulta.

O gatilho da fila fornece um parâmetro `departmentId`. Uma mensagem de `{ "departmentId" : "Finance" }` devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Esta secção contém os exemplos seguintes:

* [GATILHO HTTP, procure o ID da corda de consulta - Parâmetro de corda](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [GATILHO HTTP, procure id da corda de consulta - parâmetro POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP gatilho, procure id a partir de dados de rota](#http-trigger-look-up-id-from-route-data-java)
* [GATILHO HTTP, procure id a partir de dados de rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [GATILHO HTTP, obtenha vários docs a partir de dados de rota, usando SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Os exemplos referem-se a um tipo de `ToDoItem` simples:

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

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>GATILHO HTTP, procure o ID da corda de consulta - Parâmetro de corda

O exemplo seguinte mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de identificação e divisória para procurar. Este id e o valor chave da divisória são usados para recuperar um documento da base de dados e recolha especificada, em forma de String.

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

Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)use a anotação `@CosmosDBInput` em parâmetros de função cujo valor viria de Cosmos DB.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores nuníveis usando `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>GATILHO HTTP, procure id da corda de consulta - parâmetro POJO

O exemplo seguinte mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o valor da chave de identificação e divisória para procurar. Esse valor de identificação e divisória usado para recuperar um documento da base de dados e recolha especificadas. O documento é então convertido para um caso do ```ToDoItem``` POJO anteriormente criado, e passado como um argumento para a função.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo seguinte mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido http que utiliza um parâmetro de rota para especificar o id e o valor da chave de partição para procurar. Esse valor chave de identificação e partição é utilizado para recuperar um documento da base de dados e recolha especificadas, devolvendo-o como um ```Optional<String>```.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>GATILHO HTTP, procure id a partir de dados de rota, usando SqlQuery

O exemplo seguinte mostra uma função Java que recupera um único documento. A função é desencadeada por um pedido http que utiliza um parâmetro de rota para especificar o ID para procurar. Este ID é utilizado para recuperar um documento da base de dados e recolha especificadas, convertendo o resultado definido para um ```ToDoItem[]```, uma vez que muitos documentos podem ser devolvidos, dependendo dos critérios de consulta.

> [!NOTE]
> Se precisar de consultar apenas o ID, recomenda-se utilizar um look up, como os [exemplos anteriores](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), uma vez que consumirá menos [unidades](../cosmos-db/request-units.md)de pedido . As operações de leitura de pontos (GET) são [mais eficientes](../cosmos-db/optimize-cost-queries.md) do que as consultas por ID.
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

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>GATILHO HTTP, obtenha vários docs a partir de dados de rota, usando SqlQuery

O exemplo que se segue mostra uma função Java que recupera vários documentos. A função é desencadeada por um pedido http que utiliza um parâmetro de rota ```desc``` especificar a cadeia para procurar no campo ```description```. O termo de pesquisa é usado para recuperar uma coleção de documentos da base de dados e recolha especificadas, convertendo o resultado definido para um ```ToDoItem[]``` e passando-o como um argumento para a função.

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

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [CosmosDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs)

Construtor do atributo pega o nome de base de dados e o nome da coleção. Para obter informações sobre essas definições e outras propriedades que possa configurar, consulte a seguinte secção de [configuração](#configuration).

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A partir da biblioteca de tempo de funcionamento das [funções java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)use a anotação `@CosmosDBOutput` em parâmetros que escrevem para cosmos DB. O tipo de parâmetro de anotação deve ser `OutputBinding<T>`, onde `T` seja do tipo Java nativo ou de UM POJO.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `CosmosDB`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     | n/d | Deve ser definido para `cosmosDB`.        |
|**direção**     | n/d | Deve ser definido para `in`.         |
|**nome**     | n/d | Nome do parâmetro de enlace que representa o documento na função.  |
|**nome da base de dados** |**Nome da base de dados** |A base de dados que contém o documento.        |
|**coleçãoNome** |**Nome de coleção** | O nome da coleção que contém o documento. |
|**id**    | **Id** | O ID do documento a obter. Esta propriedade suporta [expressões vinculativas.](./functions-bindings-expressions-patterns.md) Não detete as propriedades `id` e **sqlQuery.** Se não definir individualmente, toda a coleção é recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta de SQL do Azure Cosmos DB usada para recuperar vários documentos. A propriedade suporta encadernações de tempo de execução, como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`. Não detete as propriedades de `id` e `sqlQuery`. Se não definir individualmente, toda a coleção é recuperada.|
|**conexãoStringSetting**     |**Definição de cordas de ligação**|O nome da definição de aplicação que contém a cadeia de ligação do Azure Cosmos DB. |
|**partiçãoChave**|**Chave da Partilha**|Especifica o valor de chave de partição para a pesquisa. Pode incluir parâmetros de ligação. É necessário para procuras em coleções [divididas.](../cosmos-db/partition-data.md#logical-partitions)|
|**preferredLocalizações**| **Localizações Preferenciais**| (Opcional) Define localizações preferenciais (regiões) para contas de base de dados geo-replicadas no serviço Azure Cosmos DB. Os valores devem ser separados pela vírposta. Por exemplo, "Leste dos EUA, Centro-Sul dos EUA, Norte da Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Quando a função sai com sucesso, quaisquer alterações feitas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistidas.

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Quando a função sai com sucesso, quaisquer alterações feitas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistidas.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

As atualizações não são feitas automaticamente após a saída da função. Em vez disso, utilize `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para fazer atualizações. Consulte o exemplo JavaScript.

# <a name="python"></a>[python](#tab/python)

Os dados são disponibilizados à função através de um parâmetro `DocumentList`. As alterações feitas ao documento não são automaticamente persistiu.

# <a name="java"></a>[Java](#tab/java)

Da biblioteca de tempo de funcionamento das [funções java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)a [anotação@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) expõe os dados do Cosmos DB à função. Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores nuníveis usando `Optional<T>`.

---

## <a name="next-steps"></a>Passos Seguintes

- [Executar uma função quando um documento Azure Cosmos DB é criado ou modificado (Gatilho)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Guardar alterações num documento Azure Cosmos DB (Encadernação de saída)](./functions-bindings-cosmosdb-v2-output.md)