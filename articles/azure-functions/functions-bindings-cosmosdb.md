---
title: Ligações DB Azure Cosmos para funções 1.x
description: Entenda como usar os gatilhos e encadernações DB do Azure Cosmos em Azure Functions 1.x.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 524df7805207ce517c7ae805fb17de1b041a2248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002120"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Ligações DB Azure Cosmos para Funções Azure 1.x

> [!div class="op_single_selector" title1="Selecione a versão do tempo de execução das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este artigo explica como trabalhar com as ligações [DB da Azure Cosmos](../cosmos-db/serverless-computing-database.md) em Funções Azure. As funções Azure suportam o gatilho, a entrada e as ligações de saída para a Azure Cosmos DB.

> [!NOTE]
> Este artigo é para Funções Azure 1.x. Para obter informações sobre como utilizar estas ligações nas funções 2.x e superiores, consulte [as ligações DB do Azure Cosmos para as Funções Azure 2.x](functions-bindings-cosmosdb-v2.md).
>
>Esta ligação foi originalmente chamada DocumentDB. Em Funções versão 1.x, apenas o gatilho foi renomeado Cosmos DB; a ligação de entrada, a ligação de saída e o pacote NuGet mantêm o nome DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Os enlaces do Cosmos DB do Azure só são suportados para utilização com a API de SQL. Para todas as outras APIs Azure Cosmos DB, você deve aceder à base de dados a partir da sua função usando o cliente estático para a sua API, incluindo [API da Azure Cosmos DB para MongoDB,](../cosmos-db/mongodb-introduction.md) [Cassandra API,](../cosmos-db/cassandra-introduction.md) [Gremlin API,](../cosmos-db/graph-introduction.md)e [Tabela API.](../cosmos-db/table-introduction.md)

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações DB do Azure Cosmos para funções versão 1.x são fornecidas no [ pacoteMicrosoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet, versão 1.x. O código-fonte para as ligações está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Acionador

O Azure Cosmos DB Trigger utiliza o [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) para ouvir inserções e atualizações através de divisórias. O feed de alteração publica inserções e atualizações, não eliminações.

## <a name="trigger---example"></a>Gatilho - exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é invocada quando há inserções ou atualizações na base de dados e recolha especificadas.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho Cosmos DB numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos de DB do Cosmos são modificados.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código do guião C:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho Cosmos DB numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos de DB do Cosmos são modificados.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código JavaScript:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Gatilho - atributos

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da recolha. Para obter informações sobre as configurações e outras propriedades que possa configurar, consulte [a configuração Trigger -](#trigger---configuration). Aqui está um `CosmosDBTrigger` exemplo de atributo numa assinatura de método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Para um exemplo completo, consulte [o exemplo do Trigger - C#](#trigger).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

---

## <a name="trigger---configuration"></a>Gatilho - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `CosmosDBTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `cosmosDBTrigger` . |
|**direção** | n/a | Deve ser definido para `in` . Este parâmetro é definido automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome variável usado no código de função que representa a lista de documentos com alterações. |
|**conexãoStringSetting**|**ConexãoStringSetting** | O nome de uma definição de aplicação que contém a cadeia de ligação usada para ligar à conta DB Azure Cosmos que está a ser monitorizada. |
|**base de dados Nome**|**DatabaseName**  | O nome da base de dados DB Azure Cosmos com a recolha a ser monitorizada. |
|**coleçãoName** |**CollectionName** | O nome da coleção está a ser monitorizado. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcional) O nome de uma definição de aplicação que contém a cadeia de ligação ao serviço que detém a coleção de arrendamento. Quando não definido, o `connectionStringSetting` valor é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. A cadeia de ligação para a coleção de arrendamentos deve ter permissões de escrita.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) O nome da base de dados que detém a coleção usada para armazenar arrendamentos. Quando não estiver definido, o valor da `databaseName` definição é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. |
|**leaseCollectionName** | **Contrato de ArrendamentoCollectionName** | (Opcional) O nome da coleção usada para armazenar arrendamentos. Quando não definido, o valor `leases` é utilizado. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcional) Quando definido `true` para, a coleção de arrendamentos é criada automaticamente quando já não existe. O valor predefinido é `false`. |
|**locaçõesCollectionThroughput**| **LocaçõesCollectionThroughput**| (Opcional) Define o valor das Unidades de Pedido a atribuir quando a coleção de arrendamentos é criada. Esta definição só é utilizada Quando `createLeaseCollectionIfNotExists` estiver definida para `true` . Este parâmetro é automaticamente definido quando a ligação é criada utilizando o portal.
|**leaseCollectionPrefix**| **Contrato de Arrendamento**| (Opcional) Quando definido, adiciona um prefixo aos arrendamentos criados na coleção Lease para esta Função, permitindo efetivamente que duas Funções Azure separadas partilhem a mesma coleção De Arrendamento utilizando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| (Opcional) Quando definido, define, em milissegundos, o atraso entre a votação de uma divisão para novas alterações no feed, depois de todas as alterações atuais serem drenadas. O padrão é de 5000 (5 segundos).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcional) Quando definido, define, em milissegundos, o intervalo para iniciar uma tarefa de cálculo se as divisórias forem distribuídas uniformemente entre instâncias de anfitrião conhecidas. O padrão é de 13000 (13 segundos).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcional) Quando definido, define, em milissegundos, o intervalo para o qual o arrendamento é tomado em um arrendamento que representa uma partição. Se o arrendamento não for renovado dentro deste intervalo, fará com que expire e a propriedade da partição passará para outro caso. O padrão é de 60000 (60 segundos).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcional) Quando definido, define, em milissegundos, o intervalo de renovação de todos os arrendamentos para divisórias atualmente detidos por exemplo. O padrão é de 17000 (17 segundos).
|**checkpointFrequência**| **CheckpointFrequência**| (Opcional) Quando definido, define, em milissegundos, o intervalo entre os pontos de verificação do arrendamento. O padrão é sempre após cada chamada de Função.
|**maxitemsPerInvocation**| **MaxItemsPerInvocation**| (Opcional) Quando definido, personaliza a quantidade máxima de itens recebidos por chamada de Função.
|**inícioFromBeginning**| **Início DoBeginning**| (Opcional) Quando definido, diz ao Gatilho para começar a ler alterações desde o início da história da coleção em vez da hora atual. Isto funciona apenas da primeira vez que o Gatilho começa, pois em execuções posteriores, os pontos de verificação já estão armazenados. Definir isto para `true` quando há arrendamentos já criados não tem efeito.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - utilização

O gatilho requer uma segunda coleção que usa para armazenar _arrendamentos_ sobre as divisórias. Tanto a recolha que está a ser monitorizada como a coleção que contém os contratos de arrendamento devem estar disponíveis para o gatilho funcionar.

>[!IMPORTANT]
> Se várias funções forem configuradas para usar um gatilho Cosmos DB para a mesma coleção, cada uma das funções deve usar uma coleção de locação dedicada ou especificar uma diferente `LeaseCollectionPrefix` para cada função. Caso contrário, apenas uma das funções será ativada. Para obter informações sobre o prefixo, consulte a [secção Configuração](#trigger---configuration).

O gatilho não indica se um documento foi atualizado ou inserido, apenas fornece o documento em si. Se precisar de lidar com atualizações e inserções de forma diferente, poderá fazê-lo implementando campos de tempotamp para inserção ou atualização.

## <a name="input"></a>Entrada

O enlace de entrada do Azure Cosmos DB utiliza a API do SQL para obter um ou mais documentos do Azure Cosmos DB e passa-os para o parâmetro de entrada da função. Os parâmetros de consulta ou o ID do documento podem ser determinados com base no acionador que invoca a função.

## <a name="input---example"></a>Entrada - exemplo

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
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, procure o ID de JSON

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por uma mensagem de fila que contém um objeto JSON. O gatilho da fila analisa o JSON num objeto chamado `ToDoItemLookup` , que contém o ID para olhar para cima. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP trigger, procure o ID a partir da cadeia de consulta

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o ID para procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Salte exemplos de entrada](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Detonador HTTP, procure o ID a partir de dados de rota, usando SqlQuery

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o ID para procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Salte exemplos de entrada](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Detonador HTTP, obtenha vários docs, usando SqlQuery

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. A consulta é especificada na `SqlQuery` propriedade do atributo.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Salte exemplos de entrada](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Detonador HTTP, obtenha vários docs, utilizando o DocumentClient (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. O código utiliza uma `DocumentClient` instância fornecida pela ligação Azure Cosmos DB para ler uma lista de documentos. O `DocumentClient` caso também pode ser utilizado para operações de escrita.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
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
namespace CosmosDBSamplesV1
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

A secção [de configuração](#input---configuration) explica estas propriedades.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A secção [de configuração](#input---configuration) explica estas propriedades.

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

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Aqui está o código do guião C:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP trigger, procure o ID a partir de dados de rota

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é desencadeada por um pedido HTTP que utiliza dados de rota para especificar o ID para procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Aqui está o código do guião C:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Esta secção contém os seguintes exemplos:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção [de configuração](#input---configuration) explica estas propriedades.

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

O exemplo a seguir mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
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

O exemplo a seguir mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. Essa identificação é usada para recuperar um `ToDoItem` documento da base de dados e recolha especificadas.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A secção [de configuração](#input---configuration) explica estas propriedades.

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

---

## <a name="input---attributes"></a>Entrada - atributos

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da recolha. Para obter informações sobre as configurações e outras propriedades que possa configurar, consulte [a seguinte secção de configuração](#input---configuration).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `DocumentDB` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo**     | n/a | Deve ser definido para `documentdb` .        |
|**direção**     | n/a | Deve ser definido para `in` .         |
|**nome**     | n/a | Nome do parâmetro de ligação que representa o documento na função.  |
|**base de dados Nome** |**DatabaseName** |A base de dados que contém o documento.        |
|**coleçãoName** |**CollectionName** | O nome da coleção que contém o documento. |
|**id**    | **ID** | A identificação do documento para recuperar. Esta propriedade suporta [expressões vinculativas.](./functions-bindings-expressions-patterns.md) Não desconte as propriedades **de id** e **sqlQuery.** Se não definir nenhum dos dois, toda a coleção será recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta Azure Cosmos DB SQL usada para recuperar vários documentos. A propriedade suporta encadernações de tempo de execução, como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}` . Não desconte as propriedades **de id** e **sqlQuery.** Se não definir nenhum dos dois, toda a coleção será recuperada.|
|**conexão**     |**ConexãoStringSetting**|O nome da definição da aplicação que contém a sua cadeia de ligação Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Especifica o valor da chave de partição para a procura. Pode incluir parâmetros de ligação.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - utilização

# <a name="c"></a>[C#](#tab/csharp)

Quando a função sai com sucesso, quaisquer alterações efetuadas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistiu.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Quando a função sai com sucesso, quaisquer alterações efetuadas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistiu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

As atualizações não são efetuadas automaticamente após a saída da função. Em vez disso, use `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` faça atualizações. Veja o [exemplo de entrada](#input).

---

## <a name="output"></a>Saída

A ligação de saída DB da Azure Cosmos permite-lhe escrever um novo documento numa base de dados DB da Azure Cosmos utilizando a API SQL.

## <a name="output---example"></a>Saída - exemplo

# <a name="c"></a>[C#](#tab/csharp)

Esta secção contém os seguintes exemplos:

* Gatilho de fila, escreva um doc
* Gatilho de fila, escrever docs usando `IAsyncCollector`

Os exemplos referem-se a um `ToDoItem` tipo simples:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Gatilho de fila, escreva um doc

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona um documento a uma base de dados, utilizando dados fornecidos na mensagem do armazenamento da fila.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando iAsyncCollector

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona uma recolha de documentos a uma base de dados, utilizando dados fornecidos numa mensagem de fila JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Esta secção contém os seguintes exemplos:

* Gatilho de fila, escreva um doc
* Gatilho de fila, escrever docs usando `IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção [de configuração](#output---configuration) explica estas propriedades.

Aqui está o código do guião C:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando iAsyncCollector

Para criar vários documentos, pode ligar-se `ICollector<T>` ou onde está um dos tipos `IAsyncCollector<T>` `T` suportados.

Este exemplo refere-se a um `ToDoItem` tipo simples:

```cs
namespace CosmosDBSamplesV1
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
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está o código do guião C:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção [de configuração](#output---configuration) explica estas propriedades.

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

---

## <a name="output---attributes"></a>Saída - atributos

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da recolha. Para obter informações sobre as configurações e outras propriedades que possa configurar, consulte [saída - configuração](#output---configuration). Aqui está um `DocumentDB` exemplo de atributo numa assinatura de método:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Para um exemplo completo, consulte [a Saída](#output).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `DocumentDB` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo**     | n/a | Deve ser definido para `documentdb` .        |
|**direção**     | n/a | Deve ser definido para `out` .         |
|**nome**     | n/a | Nome do parâmetro de ligação que representa o documento na função.  |
|**base de dados Nome** | **DatabaseName**|A base de dados que contém a coleção onde o documento é criado.     |
|**coleçãoName** |**CollectionName**  | O nome da coleção onde o documento é criado. |
|**criarIfNotExists**  |**CriarExistsIfNotExists**    | Um valor booleano para indicar se a coleção é criada quando não existe. O padrão é *falso* porque novas coleções são criadas com produção reservada, o que tem implicações de custos. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Quando `CreateIfNotExists` é verdade, define o caminho chave da partição para a coleção criada.|
|**coleçãoThroughput**|**ColeçãoThroughput**| Quando `CreateIfNotExists` é verdade, define o [rendimento](../cosmos-db/set-throughput.md) da coleção criada.|
|**conexão**    |**ConexãoStringSetting** |O nome da definição da aplicação que contém a sua cadeia de ligação Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

Por predefinição, quando escreve para o parâmetro de saída na sua função, é criado um documento na sua base de dados. Este documento tem um GUID gerado automaticamente como o ID do documento. Pode especificar o documento ID do documento de saída especificando a `id` propriedade no objeto JSON passada para o parâmetro de saída.

> [!Note]
> Quando especifica o ID de um documento existente, este é substituído pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Cosmos DB | [Códigos de erro cosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre computação de bases de dados sem servidor com a Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
