---
title: Ligações Azure Cosmos DB para funções 1.x
description: Compreenda como utilizar gatilhos e encadernações Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: e30b256d9fa43402c3b2c444aa1a0e0dc16cfdcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277547"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Ligações Azure Cosmos DB para funções Azure 1.x

> [!div class="op_single_selector" title1="Selecione a versão do tempo de funcionamento das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este artigo explica como trabalhar com ligações [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) em Funções Azure. A Azure Functions suporta ligações de gatilho, entrada e saída para o Azure Cosmos DB.

> [!NOTE]
> Este artigo é para funções Azure 1.x. Para obter informações sobre como utilizar estas ligações nas Funções 2.x e superiores, consulte [ligações Azure Cosmos DB para Funções Azure 2.x](functions-bindings-cosmosdb-v2.md).
>
>Esta ligação foi originalmente chamada DocumentDB. Em Funções versão 1.x, apenas o gatilho foi renomeado Cosmos DB; a encadernação de entrada, a ligação de saída e o pacote NuGet mantêm o nome DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Os enlaces do Cosmos DB do Azure só são suportados para utilização com a API de SQL. Para todas as outras APIs DB Da Azure Cosmos, deve aceder à base de dados a partir da sua função utilizando o cliente estático para a sua API, incluindo a [API da Azure Cosmos DB para MongoDB,](../cosmos-db/mongodb-introduction.md) [Cassandra API,](../cosmos-db/cassandra-introduction.md) [Gremlin API](../cosmos-db/graph-introduction.md)e [API de Mesa.](../cosmos-db/table-introduction.md)

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações Azure Cosmos DB para funções versão 1.x são fornecidas no [microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet, versão 1.x. O código fonte para as encadernações está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Acionador

O Gatilho DB Azure Cosmos utiliza o [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) para ouvir inserções e atualizações em divisórias. O feed de alteração publica inserções e atualizações, não eliminações.

## <a name="trigger---example"></a>Gatilho - exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo que se segue mostra uma [função C#](functions-dotnet-class-library.md) que é invocada quando há inserções ou atualizações na base de dados e recolha especificadas.

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

O exemplo seguinte mostra uma ligação do gatilho Cosmos DB num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos do Cosmos DB são modificados.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

Aqui está o código de script C#:

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

O exemplo seguinte mostra uma ligação do gatilho Cosmos DB num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos do Cosmos DB são modificados.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da coleção. Para obter informações sobre essas definições e outras propriedades que possa configurar, consulte [O Gatilho - configuração](#trigger---configuration). Aqui está `CosmosDBTrigger` um exemplo de atributo numa assinatura de método:

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

Para um exemplo completo, consulte [o exemplo Trigger - C#](#trigger).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

---

## <a name="trigger---configuration"></a>Gatilho - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `CosmosDBTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de `cosmosDBTrigger`ser definido para. |
|**direção** | n/d | Tem de `in`ser definido para. Este parâmetro é definido automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/d | O nome variável utilizado no código de função que representa a lista de documentos com alterações. |
|**conexãoStringSetting**|**Definição de cordas de ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação utilizada para ligar à conta Azure Cosmos DB está a ser monitorizada. |
|**nome da base de dados**|**DatabaseName**  | O nome da base de dados Azure Cosmos DB com a coleção a ser monitorizada. |
|**coleçãoNome** |**CollectionName** | O nome da coleção está a ser monitorizado. |
|**arrendamentoConnectionStringSetting** | **ArrendamentoConexStringSetting** | (Opcional) O nome de uma definição de aplicação que contém a cadeia de ligação ao serviço que detém a coleção de arrendamento. Quando não definido, o `connectionStringSetting` valor é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. A corda de ligação para a coleção de arrendamentos deve ter permissões de escrita.|
|**arrendamentoNome basede-dados** |**Nome de leasing Database** | (Opcional) O nome da base de dados que detém a coleção utilizada para armazenar arrendamentos. Quando não está definido, `databaseName` o valor da definição é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção costumava armazenar arrendamentos. Quando não definido, `leases` o valor é utilizado. |
|**criarLeaseCollectionIfNotExists** | **CreateLeaseCollectionifNotexists** | (Opcional) Quando definida, `true`a coleção de arrendamentos é automaticamente criada quando já não existe. O valor predefinido é `false`. |
|**arrendamentosCollectionThroughputput**| **LeasesCollectionThroughput**| (Opcional) Define a quantidade de Unidades de Pedido para atribuir quando a coleção de arrendamentos é criada. Esta definição só `createLeaseCollectionIfNotExists` é `true`utilizada Quando estiver definida para . Este parâmetro é automaticamente definido quando a ligação é criada utilizando o portal.
|**arrendamentoCollectionPrefix**| **LeaseCollectionPrefix**| (Opcional) Quando definido, adiciona um prefixo aos contratos de arrendamento criados na coleção Lease para esta Função, permitindo efetivamente que duas funções Azure separadas partilhem a mesma coleção de Arrendamento utilizando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| (Opcional) Quando definido, define, em milissegundos, o atraso entre a sondagem de uma partição para novas alterações no feed, depois de todas as alterações atuais serem drenadas. Predefinido é de 5000 (5 segundos).
|**arrendamentoAdquirirInterval**| **Intervalo de Arrendamento**| (Opcional) Quando definido, define, em milissegundos, o intervalo para iniciar uma tarefa de calcular se as divisórias são distribuídas uniformemente entre as instâncias conhecidas do anfitrião. O padrão é de 13000 (13 segundos).
|**intervalo de validade**| **Intervalo de locação prazo**| (Opcional) Quando definido, define, em milissegundos, o intervalo para o qual o arrendamento é assumido num contrato de arrendamento que representa uma partição. Se o contrato de arrendamento não for renovado dentro deste intervalo, irá fazê-lo expirar e a propriedade da partição passará para outra instância. O padrão é de 60000 (60 segundos).
|**arrendamentoRenewInterval**| **Intervalo de Arrendamento Renovado**| (Opcional) Quando definido, define, em milissegundos, o intervalo de renovação para todos os arrendamentos para divisórias atualmente detidos por exemplo. O padrão é de 17000 (17 segundos).
|**checkpointFrequência**| **CheckpointFrequência**| (Opcional) Quando definido, define, em milissegundos, o intervalo entre os pontos de verificação de locação. O predefinido é sempre posterior a cada chamada de função.
|**maxItemsPerInvocação**| **MaxItemsPerInvocação**| (Opcional) Quando definido, personaliza a quantidade máxima de itens recebidos por chamada de Função.
|**inícioFromBeginning**| **Início do início**| (Opcional) Quando definido, diz ao Gatilho para começar a ler alterações desde o início da história da coleção em vez do tempo atual. Isto só funciona na primeira vez que o Gatilho começa, como em execuções subsequentes, os postos de controlo já estão armazenados. Definir isto `true` para quando há arrendamentos já criados não tem efeito.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - utilização

O gatilho requer uma segunda coleção que utiliza para armazenar _arrendamentos_ sobre as divisórias. Tanto a recolha que está a ser monitorizada como a recolha que contém os contratos de arrendamento devem estar disponíveis para o gatilho funcionar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para utilizar um gatilho Cosmos DB para a mesma recolha, `LeaseCollectionPrefix` cada uma das funções deve utilizar uma coleção de locação dedicada ou especificar um diferente para cada função. Caso contrário, apenas uma das funções será desencadeada. Para obter informações sobre o prefixo, consulte a [secção Configuração](#trigger---configuration).

O gatilho não indica se um documento foi atualizado ou inserido, apenas fornece o documento em si. Se precisar de lidar com atualizações e inserções de forma diferente, poderá fazê-lo implementando campos de carimbos de tempo para inserção ou atualização.

## <a name="input"></a>Input

O enlace de entrada do Azure Cosmos DB utiliza a API do SQL para obter um ou mais documentos do Azure Cosmos DB e passa-os para o parâmetro de entrada da função. Os parâmetros de consulta ou o ID do documento podem ser determinados com base no acionador que invoca a função.

# <a name="c"></a>[C #](#tab/csharp)

Esta secção contém os seguintes exemplos:

* [Gatilho de fila, procure identificação da JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP gatilho, procure o ID da corda de consulta](#http-trigger-look-up-id-from-query-string-c)
* [HTTP gatilho, procure id a partir de dados de rota](#http-trigger-look-up-id-from-route-data-c)
* [GATILHO HTTP, procure id a partir de dados de rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [GATILHO HTTP, obtenha vários docs, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [GATILHO HTTP, obtenha vários docs, usando o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Os exemplos referem-se a um tipo simples: `ToDoItem`

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

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho de fila, procure identificação da JSON

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por uma mensagem de fila que contém um objeto JSON. O gatilho da fila analisa o JSON num objeto chamado `ToDoItemLookup`, que contém o ID para olhar para cima. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP gatilho, procure o ID da corda de consulta

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido http que utiliza dados de rota para especificar o ID para procurar. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

[Ignore exemplos de entrada](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>GATILHO HTTP, procure id a partir de dados de rota, usando SqlQuery

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é desencadeada por um pedido http que utiliza dados de rota para especificar o ID para procurar. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

[Ignore exemplos de entrada](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>GATILHO HTTP, obtenha vários docs, usando SqlQuery

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. A consulta é especificada `SqlQuery` na propriedade do atributo.

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

[Ignore exemplos de entrada](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>GATILHO HTTP, obtenha vários docs, usando O Cliente documental (C#)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. O código `DocumentClient` utiliza uma instância fornecida pelo Azure Cosmos DB vinculando-se para ler uma lista de documentos. A `DocumentClient` instância também poderia ser utilizada para as operações de escrita.

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

* [Gatilho de fila, procure identificação da corda](#queue-trigger-look-up-id-from-string-c-script)
* [Gatilho de fila, obter vários docs, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP gatilho, procure o ID da corda de consulta](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP gatilho, procure id a partir de dados de rota](#http-trigger-look-up-id-from-route-data-c-script)
* [GATILHO HTTP, obtenha vários docs, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [GATILHO HTTP, obtenha vários docs, usando o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Os exemplos do gatilho `ToDoItem` HTTP referem-se a um tipo simples:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Gatilho de fila, procure identificação da corda

O exemplo seguinte mostra uma ligação de entrada Cosmos DB num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#input---configuration) explica estas propriedades.

Aqui está o código de script C#:

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

O exemplo seguinte mostra uma ligação de entrada Azure Cosmos DB num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função recupera vários documentos especificados por uma consulta SQL, utilizando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho da fila `departmentId`fornece um parâmetro . Uma mensagem `{ "departmentId" : "Finance" }` de fila devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#input---configuration) explica estas propriedades.

Aqui está o código de script C#:

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

O exemplo seguinte mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

Aqui está o código de script C#:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo seguinte mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é desencadeada por um pedido http que utiliza dados de rota para especificar o ID para procurar. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

Aqui está o código de script C#:

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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>GATILHO HTTP, obtenha vários docs, usando SqlQuery

O exemplo que se segue mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. A consulta é especificada `SqlQuery` na propriedade do atributo.

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

Aqui está o código de script C#:

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>GATILHO HTTP, obtenha vários docs, usando o DocumentClient

O exemplo que se segue mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é desencadeada por um pedido HTTP. O código `DocumentClient` utiliza uma instância fornecida pelo Azure Cosmos DB vinculando-se para ler uma lista de documentos. A `DocumentClient` instância também poderia ser utilizada para as operações de escrita.

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

Aqui está o código de script C#:

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

A secção de [configuração](#input---configuration) explica estas propriedades.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP gatilho, procure o ID da corda de consulta

O exemplo seguinte mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP gatilho, procure id a partir de dados de rota

O exemplo seguinte mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID a procurar. O ID é usado `ToDoItem` para recuperar um documento da base de dados e recolha especificadas.

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

O exemplo seguinte mostra uma ligação de entrada Azure Cosmos DB num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função recupera vários documentos especificados por uma consulta SQL, utilizando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho da fila `departmentId`fornece um parâmetro . Uma mensagem `{ "departmentId" : "Finance" }` de fila devolveria todos os registos para o Departamento de Finanças.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#input---configuration) explica estas propriedades.

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

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da coleção. Para obter informações sobre essas definições e outras propriedades que possa configurar, consulte a seguinte secção de [configuração](#input---configuration).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `DocumentDB` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     | n/d | Tem de `documentdb`ser definido para.        |
|**direção**     | n/d | Tem de `in`ser definido para.         |
|**nome**     | n/d | Nome do parâmetro de ligação que representa o documento na função.  |
|**nome da base de dados** |**DatabaseName** |A base de dados que contém o documento.        |
|**coleçãoNome** |**CollectionName** | O nome da coleção que contém o documento. |
|**ID**    | **Id** | A identificação do documento para recuperar. Esta propriedade suporta [expressões vinculativas.](./functions-bindings-expressions-patterns.md) Não detete **as** propriedades id e **sqlQuery.** Se não definir nenhum dos dois, toda a coleção é recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta Azure Cosmos DB SQL usada para recuperar vários documentos. A propriedade suporta encadernações de `SELECT * FROM c where c.departmentId = {departmentId}`tempo de execução, como neste exemplo: . Não detete **as** propriedades id e **sqlQuery.** Se não definir nenhum dos dois, toda a coleção é recuperada.|
|**conexão**     |**Definição de cordas de ligação**|O nome da configuração da aplicação que contém a sua cadeia de ligação Azure Cosmos DB.        |
|**partiçãoChave**|**PartitionKey**|Especifica o valor-chave da divisória para a procura. Pode incluir parâmetros de ligação.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - utilização

# <a name="c"></a>[C #](#tab/csharp)

Quando a função sai com sucesso, quaisquer alterações feitas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistidas.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Quando a função sai com sucesso, quaisquer alterações feitas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistidas.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

As atualizações não são feitas automaticamente após a saída da função. Em vez `context.bindings.<documentName>In` `context.bindings.<documentName>Out` disso, use e faça atualizações. Consulte o exemplo da [entrada](#input).

---

## <a name="output"></a>Saída

A ligação de saída do Azure Cosmos DB permite-lhe escrever um novo documento para uma base de dados Azure Cosmos DB utilizando a API SQL.

# <a name="c"></a>[C #](#tab/csharp)

Esta secção contém os seguintes exemplos:

* Gatilho de fila, escrever um médico
* Gatilho de fila, escrever docs usando`IAsyncCollector`

Os exemplos referem-se a um tipo simples: `ToDoItem`

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

### <a name="queue-trigger-write-one-doc"></a>Gatilho de fila, escrever um médico

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que adiciona um documento a uma base de dados, utilizando dados fornecidos na mensagem a partir do armazenamento da fila.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando IAsyncCollector

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que adiciona uma recolha de documentos a uma base de dados, utilizando dados fornecidos numa mensagem de fila JSON.

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

* Gatilho de fila, escrever um médico
* Gatilho de fila, escrever docs usando`IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção de [configuração](#output---configuration) explica estas propriedades.

Aqui está o código de script C#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, escreva docs usando IAsyncCollector

Para criar vários documentos, `IAsyncCollector<T>` `T` pode ligar-se ou `ICollector<T>` onde é um dos tipos suportados.

Este exemplo refere-se `ToDoItem` a um tipo simples:

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

Aqui está o ficheiro função.json:

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

Aqui está o código de script C#:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A secção de [configuração](#output---configuration) explica estas propriedades.

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

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [DocumentDB.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da coleção. Para obter informações sobre essas definições e outras propriedades que possa configurar, consulte [A Saída - configuração](#output---configuration). Aqui está `DocumentDB` um exemplo de atributo numa assinatura de método:

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

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

---

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `DocumentDB` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     | n/d | Tem de `documentdb`ser definido para.        |
|**direção**     | n/d | Tem de `out`ser definido para.         |
|**nome**     | n/d | Nome do parâmetro de ligação que representa o documento na função.  |
|**nome da base de dados** | **DatabaseName**|A base de dados que contém a coleção onde o documento é criado.     |
|**coleçãoNome** |**CollectionName**  | O nome da coleção onde o documento é criado. |
|**criarIfNotexists**  |**Criarifnotexist**    | Um valor booleano para indicar se a coleção é criada quando não existe. O padrão é *falso* porque novas coleções são criadas com entrada reservada, que tem implicações de custos. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partiçãoChave**|**PartitionKey** |Quando `CreateIfNotExists` é verdade, define o caminho-chave da partilha para a coleção criada.|
|**coleçãoThroughput**|**ColeçãoThroughput**| Quando `CreateIfNotExists` é verdade, define a [entrada](../cosmos-db/set-throughput.md) da coleção criada.|
|**conexão**    |**Definição de cordas de ligação** |O nome da configuração da aplicação que contém a sua cadeia de ligação Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

Por predefinição, quando escreve para o parâmetro de saída na sua função, é criado um documento na sua base de dados. Este documento tem um GUID gerado automaticamente como id do documento. Pode especificar o documento de identificação do `id` documento de saída especificando a propriedade no objeto JSON passada para o parâmetro de saída.

> [!Note]
> Quando especifica a identificação de um documento existente, este é substituído pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| CosmosDB | [Códigos de Erro CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a computação de base de dados sem servidores com cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
