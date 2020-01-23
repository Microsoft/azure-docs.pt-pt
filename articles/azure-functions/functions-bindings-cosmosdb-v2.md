---
title: Associações de Azure Cosmos DB para o Functions 2. x
description: Compreenda como utilizar o Azure Cosmos DB acionadores e enlaces nas funções do Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: da05dc7136a75d519660412f2ce176f7530eb392
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547443"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB associações para Azure Functions 2. x

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este artigo explica como trabalhar com associações de [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) no Azure Functions 2. x. Funções do Azure suporta acionaram, de entrada e saída enlaces para o Azure Cosmos DB.

> [!NOTE]
> Este artigo é para [Azure Functions versão 2. x](functions-versions.md).  Para obter informações sobre como usar essas associações no functions 1. x, consulte [Azure Cosmos DB associações para Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Este enlace foi originalmente denominado DocumentDB. Nas funções versão 2. x, o gatilho, as associações e o pacote são todos nomeados Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>APIs com suporte

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

As associações de Azure Cosmos DB para as funções versão 2. x são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) , versão 3. x. É de código-fonte para as ligações na [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

O acionador do Azure Cosmos DB utiliza a [Feed de alterações do Azure Cosmos DB](../cosmos-db/change-feed.md) para escutar as inserções e atualizações em várias partições. O feed de alterações publica inserções e atualizações, eliminações não.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que é invocado quando há insere ou atualiza a coleção e a base de dados especificada.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo seguinte mostra um acionador Cosmos DB ligando uma *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função grava mensagens de log quando Cosmos DB registros são adicionados ou modificados.

Eis a vinculação de dados a *Function* ficheiro:

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

Aqui está o código de script do c#:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra um acionador Cosmos DB ligando uma *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função grava mensagens de log quando Cosmos DB registros são adicionados ou modificados.

Eis a vinculação de dados a *Function* ficheiro:

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

Eis o código JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho Cosmos DB em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função escreve as mensagens de registo quando os registos de Cosmos DB são modificados.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Este é o código Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo a seguir mostra uma associação de gatilho Cosmos DB no arquivo *Function. JSON* e uma [função Java](functions-reference-java.md) que usa a associação. A função é invocada quando há inserções ou atualizações no banco de dados e na coleção especificados.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Eis o código Java:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBTrigger` em parâmetros cujo valor venha de Cosmos DB.  Essa anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando `Optional<T>`.

---

## <a name="trigger---attributes-and-annotations"></a>Gatilho-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) atributo.

Construtor do atributo pega o nome de base de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que pode configurar, consulte [acionador - configuração](#trigger---configuration). Aqui está um `CosmosDBTrigger` exemplo de atributo numa assinatura do método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Para obter um exemplo completo, consulte [gatilho](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBInput` em parâmetros que lêem dados de Cosmos DB.

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `CosmosDBTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `cosmosDBTrigger`. |
|**direção** | n/d | Tem de ser definido como `in`. Este parâmetro é definido automaticamente quando criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável no código de função que representa a lista de documentos com as alterações. |
|**connectionStringSetting**|**ConnectionStringSetting** | O nome de uma definição de aplicação que contém a cadeia de ligação utilizada para ligar à conta do Azure Cosmos DB a ser monitorizada. |
|**databaseName**|**DatabaseName**  | O nome da base de dados do Azure Cosmos DB com a coleção a ser monitorizada. |
|**collectionName** |**CollectionName** | O nome da coleção a ser monitorizado. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Adicional O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de Azure Cosmos DB que contém a coleção de concessão. Quando não definido, o `connectionStringSetting` valor é utilizado. Este parâmetro é definido automaticamente quando a associação é criada no portal. A cadeia de ligação para a coleção de concessões tem de ter permissões de escrita.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) O nome da base de dados que contém a coleção utilizada para armazenar as concessões. Quando não definido, o valor da `databaseName` definição é utilizada. Este parâmetro é definido automaticamente quando a associação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção utilizada para armazenar as concessões. Quando não definido, o valor `leases` é utilizado. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcional) Quando definido como `true`, a coleção de concessões é criada automaticamente quando ainda não exista. O valor predefinido é `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Adicional Define o número de unidades de solicitação a serem atribuídas quando a coleção de concessões é criada. Essa configuração só é usada quando `createLeaseCollectionIfNotExists` é definido como `true`. Este parâmetro é definido automaticamente quando a associação é criada com o portal.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| Adicional Quando definido, o valor é adicionado como um prefixo às concessões criadas na coleção de concessão para essa função. O uso de um prefixo permite que duas Azure Functions separadas compartilhem a mesma coleção de concessão usando prefixos diferentes.
|**feedPollDelay**| **feedPollDelay**| Adicional O tempo (em milissegundos) para o atraso entre a sondagem de uma partição para novas alterações no feed, depois que todas as alterações atuais forem descarregadas. O padrão é 5.000 milissegundos ou 5 segundos.
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo de disparar uma tarefa de computação se as partições são distribuídas uniformemente entre instâncias de host conhecidos. A predefinição é 13000 (13 segundos).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo para o qual a concessão é criada numa concessão que representa uma partição. Se a concessão não for renovada dentro deste intervalo, fará com que expire e propriedade da partição irá mudar para outra instância. A predefinição é 60000 (60 segundos).
|**leaseRenewInterval**| **leaseRenewInterval**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo de renovação para todas as concessões para partições atualmente mantido por uma instância. A predefinição é 17000 (17 segundos).
|**checkpointFrequency**| **checkpointFrequency**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo entre pontos de verificação de concessão. O padrão é sempre após cada chamada de função.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| Adicional Quando definido, essa propriedade define o número máximo de itens recebidos por chamada de função. Se as operações na coleção monitorada forem executadas por meio de procedimentos armazenados, o [escopo da transação](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) será preservado durante a leitura de itens do feed de alterações. Como resultado, o número de itens recebidos pode ser maior que o valor especificado para que os itens alterados pela mesma transação sejam retornados como parte de um lote atômico.
|**startFromBeginning**| **StartFromBeginning**| Adicional Essa opção informa o gatilho para ler as alterações desde o início do histórico de alterações da coleção, em vez de iniciar na hora atual. A leitura do início só funciona na primeira vez que o gatilho é iniciado, como nas execuções subsequentes, os pontos de verificação já estão armazenados. Definir essa opção como `true` quando houver concessões já criadas não tem nenhum efeito.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

O acionador requer uma segunda coleção, que utiliza para armazenar _concessões_ nas partições. A coleção a ser monitorizado e a coleção que contém as concessões tem de estar disponíveis para o acionador trabalhar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para utilizar um acionador Cosmos DB para a mesma coleção, cada uma das funções deve usar uma coleção de concessão dedicado ou especificar um `LeaseCollectionPrefix` para cada função. Caso contrário, será acionada apenas uma das funções. Para obter informações sobre o prefixo, consulte a [seção de configuração](#trigger---configuration).

O acionador não indica que se um documento foi atualizado ou inserido, ele fornece apenas o próprio documento. Se precisar manipular atualizações e inserções de forma diferente, poderia fazê-lo através da implementação timestamp campos para inserção ou atualização.

## <a name="input"></a>Input

O enlace de entrada do Azure Cosmos DB utiliza a API de SQL para obter um ou mais documentos do Azure Cosmos DB e os passa para o parâmetro de entrada da função. Os parâmetros de consulta ou ID do documento podem ser determinados com base no acionador que invoca a função.

> [!NOTE]
> Se a coleção for [particionada](../cosmos-db/partition-data.md#logical-partitions), as operações de pesquisa precisarão especificar também o valor da chave de partição.
>

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Esta secção contém os exemplos seguintes:

* [Acionador de fila, procura de ID de JSON](#queue-trigger-look-up-id-from-json-c)
* [Acionador HTTP, procura de ID da cadeia de consulta](#http-trigger-look-up-id-from-query-string-c)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-c)
* [Acionador HTTP, procura de ID de encaminhar os dados, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP acionar, obter vários documentos, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP acionar, obter vários documentos, com o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Consulte os exemplos simples `ToDoItem` tipo:

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

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho de fila, Pesquisar ID de JSON 

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é acionada por uma mensagem de fila que contém um objeto JSON. O gatilho de fila analisa o JSON em um objeto do tipo `ToDoItemLookup`, que contém a ID e o valor da chave de partição a ser pesquisada. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID e o valor da chave de partição a ser pesquisada. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

>[!NOTE]
>O parâmetro de cadeia de caracteres de consulta HTTP diferencia maiúsculas de minúsculas.
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

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, Pesquisar ID de dados de rota

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é disparada por uma solicitação HTTP que usa dados de rota para especificar a ID e o valor de chave de partição a serem pesquisados. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Gatilho HTTP, Pesquisar ID de dados de rota, usando SQLQuery

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza encaminhar os dados para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

O exemplo mostra como usar uma expressão de associação no parâmetro `SqlQuery`. Você pode passar os dados de rota para o parâmetro `SqlQuery` como mostrado, mas atualmente [não é possível passar valores de cadeia de caracteres de consulta](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Se você precisar consultar apenas a ID, é recomendável usar uma pesquisa, como os [exemplos anteriores](#http-trigger-look-up-id-from-query-string-c), pois ela consumirá menos [unidades de solicitação](../cosmos-db/request-units.md). Operações de leitura de ponto (GET) são [mais eficientes](../cosmos-db/optimize-cost-queries.md) do que as consultas por ID.
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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho HTTP, obter vários documentos, usando SQLQuery

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém uma lista de documentos. A função é acionada por um pedido HTTP. A consulta é especificada no `SqlQuery` atributo de propriedade.

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Gatilho HTTP, obtenha vários documentos, usando DocumentClient

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém uma lista de documentos. A função é acionada por um pedido HTTP. O código utiliza um `DocumentClient` instância fornecida pelo enlace do Azure Cosmos DB para ler uma lista de documentos. O `DocumentClient` instância também poderia ser utilizada para operações de escrita.

> [!NOTE]
> Você também pode usar a interface [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) para facilitar o teste.

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Esta secção contém os exemplos seguintes:

* [Acionador de fila, procura de ID da cadeia](#queue-trigger-look-up-id-from-string-c-script)
* [Acionador da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Acionador HTTP, procura de ID da cadeia de consulta](#http-trigger-look-up-id-from-query-string-c-script)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP acionar, obter vários documentos, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP acionar, obter vários documentos, com o DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Consulte os exemplos de Acionador HTTP simples `ToDoItem` tipo:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Gatilho de fila, Pesquisar ID da cadeia de caracteres

O exemplo seguinte mostra um enlace de entrada do Cosmos DB num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função lê um único documento e atualiza o valor de texto do documento.

Eis a vinculação de dados a *Function* ficheiro:

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
O [configuração](#input---configuration) seção explica essas propriedades.

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho de fila, obter vários documentos, usando SQLQuery

O exemplo seguinte mostra um enlace de entrada do Azure Cosmos DB num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função obtém vários documentos especificados por uma consulta SQL, com um acionador de fila para personalizar os parâmetros de consulta.

O acionador de fila fornece um parâmetro `departmentId`. Uma mensagem de fila de `{ "departmentId" : "Finance" }` retornaria todos os registos para o departamento financeiro.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#input---configuration) seção explica essas propriedades.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta

A exemplo a seguir mostra um [função de script do c#](functions-reference-csharp.md) que obtém um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID e o valor da chave de partição a ser pesquisada. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Aqui está o *Function* ficheiro:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, Pesquisar ID de dados de rota

A exemplo a seguir mostra um [função de script do c#](functions-reference-csharp.md) que obtém um único documento. A função é disparada por uma solicitação HTTP que usa dados de rota para especificar a ID e o valor de chave de partição a serem pesquisados. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Aqui está o *Function* ficheiro:

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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho HTTP, obter vários documentos, usando SQLQuery

A exemplo a seguir mostra um [função de script do c#](functions-reference-csharp.md) que obtém uma lista de documentos. A função é acionada por um pedido HTTP. A consulta é especificada no `SqlQuery` atributo de propriedade.

Aqui está o *Function* ficheiro:

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Gatilho HTTP, obtenha vários documentos, usando DocumentClient

A exemplo a seguir mostra um [função de script do c#](functions-reference-csharp.md) que obtém uma lista de documentos. A função é acionada por um pedido HTTP. O código utiliza um `DocumentClient` instância fornecida pelo enlace do Azure Cosmos DB para ler uma lista de documentos. O `DocumentClient` instância também poderia ser utilizada para operações de escrita.

Aqui está o *Function* ficheiro:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Esta seção contém os exemplos a seguir que lêem um único documento especificando um valor de ID de várias fontes:

* [Acionador de fila, procura de ID de JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Acionador HTTP, procura de ID da cadeia de consulta](#http-trigger-look-up-id-from-query-string-javascript)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-javascript)
* [Acionador da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho de fila, Pesquisar ID de JSON

O exemplo seguinte mostra um enlace de entrada do Cosmos DB num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função lê um único documento e atualiza o valor de texto do documento.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#input---configuration) seção explica essas propriedades.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta

A exemplo a seguir mostra um [função de JavaScript](functions-reference-node.md) que obtém um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID e o valor da chave de partição a ser pesquisada. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Aqui está o *Function* ficheiro:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, Pesquisar ID de dados de rota

A exemplo a seguir mostra um [função de JavaScript](functions-reference-node.md) que obtém um único documento. A função é disparada por uma solicitação HTTP que usa dados de rota para especificar a ID e o valor de chave de partição a serem pesquisados. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Aqui está o *Function* ficheiro:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho de fila, obter vários documentos, usando SQLQuery

O exemplo seguinte mostra um enlace de entrada do Azure Cosmos DB num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função obtém vários documentos especificados por uma consulta SQL, com um acionador de fila para personalizar os parâmetros de consulta.

O acionador de fila fornece um parâmetro `departmentId`. Uma mensagem de fila de `{ "departmentId" : "Finance" }` retornaria todos os registos para o departamento financeiro.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#input---configuration) seção explica essas propriedades.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Esta seção contém os exemplos a seguir que lêem um único documento especificando um valor de ID de várias fontes:

* [Acionador de fila, procura de ID de JSON](#queue-trigger-look-up-id-from-json-python)
* [Acionador HTTP, procura de ID da cadeia de consulta](#http-trigger-look-up-id-from-query-string-python)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-python)
* [Acionador da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho de fila, Pesquisar ID de JSON

O exemplo a seguir mostra uma associação de entrada Cosmos DB em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função lê um único documento e atualiza o valor de texto do documento.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#input---configuration) seção explica essas propriedades.

Este é o código Python:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID e o valor da chave de partição a ser pesquisada. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Aqui está o *Function* ficheiro:

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

Este é o código Python:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, Pesquisar ID de dados de rota

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa dados de rota para especificar a ID e o valor de chave de partição a serem pesquisados. Esse ID e o valor da chave de partição são usados para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Aqui está o *Function* ficheiro:

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

Este é o código Python:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho de fila, obter vários documentos, usando SQLQuery

O exemplo a seguir mostra uma associação de entrada Azure Cosmos DB em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função obtém vários documentos especificados por uma consulta SQL, com um acionador de fila para personalizar os parâmetros de consulta.

O acionador de fila fornece um parâmetro `departmentId`. Uma mensagem de fila de `{ "departmentId" : "Finance" }` retornaria todos os registos para o departamento financeiro.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#input---configuration) seção explica essas propriedades.

Este é o código Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta secção contém os exemplos seguintes:

* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro de cadeia de caracteres](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-java)
* [Acionador HTTP, procura de ID de encaminhar os dados, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Gatilho HTTP, obter vários documentos de dados de rota, usando SQLQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Consulte os exemplos simples `ToDoItem` tipo:

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

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro de cadeia de caracteres

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID e o valor da chave de partição a ser pesquisada. Esse valor de ID e chave de partição são usados para recuperar um documento do banco de dados e da coleção especificados, na forma de cadeia de caracteres.

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

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBInput` em parâmetros de função cujo valor venha de Cosmos DB.  Essa anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro POJO

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID e o valor da chave de partição a ser pesquisada. Essa ID e o valor de chave de partição usados para recuperar um documento do banco de dados e da coleção especificados. O documento é então convertido em uma instância do ```ToDoItem``` POJO criado anteriormente e passado como um argumento para a função.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, Pesquisar ID de dados de rota

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa um parâmetro de rota para especificar a ID e o valor da chave de partição a ser pesquisada. Esse valor de ID e chave de partição são usados para recuperar um documento do banco de dados e da coleção especificados, retornando-o como um ```Optional<String>```.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Gatilho HTTP, Pesquisar ID de dados de rota, usando SQLQuery

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa um parâmetro de rota para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento do banco de dados e da coleção especificados, convertendo o conjunto de resultados em um ```ToDoItem[]```, já que muitos documentos podem ser retornados, dependendo dos critérios de consulta.

> [!NOTE]
> Se você precisar consultar apenas a ID, é recomendável usar uma pesquisa, como os [exemplos anteriores](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), pois ela consumirá menos [unidades de solicitação](../cosmos-db/request-units.md). Operações de leitura de ponto (GET) são [mais eficientes](../cosmos-db/optimize-cost-queries.md) do que as consultas por ID.
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

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Gatilho HTTP, obter vários documentos de dados de rota, usando SQLQuery

O exemplo a seguir mostra uma função Java que recupera vários documentos. A função é disparada por uma solicitação HTTP que usa um parâmetro de rota ```desc``` para especificar a cadeia de caracteres a ser pesquisada no campo ```description```. O termo de pesquisa é usado para recuperar uma coleção de documentos do banco de dados e da coleção especificados, convertendo o conjunto de resultados em um ```ToDoItem[]``` e passando-o como um argumento para a função.

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

## <a name="input---attributes-and-annotations"></a>Entrada-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Construtor do atributo pega o nome de base de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que pode configurar, consulte [a seguinte secção de configuração](#input---configuration).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBOutput` em parâmetros que gravam em Cosmos DB. O tipo de parâmetro Annotation deve ser `OutputBinding<T>`, onde `T` é um tipo Java nativo ou um POJO.

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `CosmosDB` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     | n/d | Tem de ser definido como `cosmosDB`.        |
|**direção**     | n/d | Tem de ser definido como `in`.         |
|**name**     | n/d | Nome do parâmetro de enlace que representa o documento na função.  |
|**databaseName** |**DatabaseName** |A base de dados que contém o documento.        |
|**collectionName** |**CollectionName** | O nome da coleção que contém o documento. |
|**id**    | **Id** | O ID do documento a obter. Esta propriedade suporta [expressões de enlace](./functions-bindings-expressions-patterns.md). Não defina as propriedades `id` e **SQLQuery** . Se não definir individualmente, toda a coleção é recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta de SQL do Azure Cosmos DB usada para recuperar vários documentos. A propriedade suporta ligações de tempo de execução, tal como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`. Não defina as propriedades `id` e `sqlQuery`. Se não definir individualmente, toda a coleção é recuperada.|
|**connectionStringSetting**     |**ConnectionStringSetting**|O nome da definição de aplicação que contém a cadeia de ligação do Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Especifica o valor de chave de partição para a pesquisa. Pode incluir parâmetros de ligação. Ele é necessário para pesquisas em coleções [particionadas](../cosmos-db/partition-data.md#logical-partitions) .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Quando a função é encerrada com êxito, todas as alterações feitas no documento de entrada por meio de parâmetros de entrada nomeados são persistidas automaticamente.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Quando a função é encerrada com êxito, todas as alterações feitas no documento de entrada por meio de parâmetros de entrada nomeados são persistidas automaticamente.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

As atualizações não são feitas automaticamente após a saída da função. Em alternativa, utilize `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para disponibilizar as atualizações. Consulte o exemplo de JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Os dados são disponibilizados para a função por meio de um parâmetro `DocumentList`. As alterações feitas no documento não são mantidas automaticamente.

# <a name="javatabjava"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), a anotação [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) expõe Cosmos DB dados para a função. Essa anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando `Optional<T>`.

---

## <a name="output"></a>Saída

A saída do Azure Cosmos DB ligação permite que escrever um novo documento para uma base de dados do Azure Cosmos DB com a API SQL.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Esta secção contém os exemplos seguintes:

* [Gatilho de fila, gravar um documento](#queue-trigger-write-one-doc-c)
* [Gatilho de fila, gravar documentos usando IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Consulte os exemplos simples `ToDoItem` tipo:

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

### <a name="queue-trigger-write-one-doc"></a>Acionador de fila, um de gravação de documentos

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que adiciona um documento a uma base de dados, com dados fornecidos na mensagem do armazenamento de filas.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Acionador de fila, docs escrita usando IAsyncCollector

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que adiciona uma coleção de documentos para uma base de dados, com dados fornecidos numa mensagem de fila JSON.

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Esta secção contém os exemplos seguintes:

* [Gatilho de fila, gravar um documento](#queue-trigger-write-one-doc-c-script)
* [Gatilho de fila, gravar documentos usando IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Acionador de fila, um de gravação de documentos

O exemplo seguinte mostra uma saída de Azure Cosmos DB ligando uma *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função usa um enlace de entrada de fila de uma fila que recebe JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos do Azure Cosmos DB no seguinte formato para cada registo:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#output---configuration) seção explica essas propriedades.

Aqui está o código de script do c#:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Acionador de fila, docs escrita usando IAsyncCollector

Para criar vários documentos, é possível vincular à `ICollector<T>` ou `IAsyncCollector<T>` onde `T` é um dos tipos suportados.

Este exemplo se refere a uma simples `ToDoItem` tipo:

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

Este é o ficheiro de Function:

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

Aqui está o código de script do c#:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma saída de Azure Cosmos DB ligando um *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função usa um enlace de entrada de fila de uma fila que recebe JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos do Azure Cosmos DB no seguinte formato para cada registo:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#output---configuration) seção explica essas propriedades.

Eis o código JavaScript:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir demonstra como gravar um documento em um banco de dados CosmosDB do Azure como a saída de uma função.

A definição de associação é definida em *Function. JSON* , em que *Type* é definido como `cosmosDB`.

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

Para gravar no banco de dados, passe um objeto Document para o método `set` do parâmetro Database.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

* [Gatilho de fila, salvar mensagem no banco de dados por meio do valor de retorno](#queue-trigger-save-message-to-database-via-return-value-java)
* [Gatilho HTTP, salve um documento no banco de dados por meio do valor de retorno](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Gatilho HTTP, salve um documento no banco de dados via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Gatilho HTTP, salve vários documentos no banco de dados por meio de Saídabinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Gatilho de fila, salvar mensagem no banco de dados por meio do valor de retorno

O exemplo a seguir mostra uma função Java que adiciona um documento a um banco de dados com um dado de uma mensagem no armazenamento de filas.

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Gatilho HTTP, salve um documento no banco de dados por meio do valor de retorno

O exemplo a seguir mostra uma função Java cuja assinatura é anotada com ```@CosmosDBOutput``` e tem valor de retorno do tipo ```String```. O documento JSON retornado pela função será gravado automaticamente na coleção CosmosDB correspondente.

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

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Gatilho HTTP, salve um documento no banco de dados via OutputBinding

O exemplo a seguir mostra uma função Java que grava um documento para CosmosDB por meio de um ```OutputBinding<T>``` parâmetro de saída. Neste exemplo, o parâmetro ```outputItem``` precisa ser anotado com ```@CosmosDBOutput```, não a assinatura de função. O uso de ```OutputBinding<T>``` permite que sua função Aproveite a associação para gravar o documento em CosmosDB enquanto também permite retornar um valor diferente para o chamador da função, como um documento JSON ou XML.

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

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Gatilho HTTP, salve vários documentos no banco de dados por meio de Saídabinding

O exemplo a seguir mostra uma função Java que grava vários documentos em CosmosDB por meio de um ```OutputBinding<T>``` parâmetro de saída. Neste exemplo, o parâmetro ```outputItem``` é anotado com ```@CosmosDBOutput```, não a assinatura de função. O parâmetro de saída ```outputItem``` tem uma lista de objetos ```ToDoItem``` como seu tipo de parâmetro de modelo. O uso de ```OutputBinding<T>``` permite que sua função Aproveite a associação para gravar os documentos em CosmosDB enquanto também permite retornar um valor diferente para o chamador da função, como um documento JSON ou XML.

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

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBOutput` em parâmetros que serão gravados em Cosmos DB.  O tipo de parâmetro Annotation deve ser ```OutputBinding<T>```, em que T é um tipo Java nativo ou um POJO.

---

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Construtor do atributo pega o nome de base de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que pode configurar, consulte [de saída - configuração](#output---configuration). Aqui está um `CosmosDB` exemplo de atributo numa assinatura do método:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
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

A anotação `CosmosDBOutput` está disponível para gravar dados no Cosmos DB. Você pode aplicar a anotação à função ou a um parâmetro de função individual. Quando usado no método Function, o valor de retorno da função é o que é gravado em Cosmos DB. Se você usar a anotação com um parâmetro, o tipo do parâmetro deverá ser declarado como um `OutputBinding<T>` onde `T` um tipo Java nativo ou um POJO.

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `CosmosDB` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     | n/d | Tem de ser definido como `cosmosDB`.        |
|**direção**     | n/d | Tem de ser definido como `out`.         |
|**name**     | n/d | Nome do parâmetro de enlace que representa o documento na função.  |
|**databaseName** | **DatabaseName**|A base de dados que contém a coleção onde o documento é criado.     |
|**collectionName** |**CollectionName**  | O nome da coleção em que o documento é criado. |
|**createIfNotExists**  |**CreateIfNotExists**    | Um valor booleano para indicar se a coleção é criada quando não existe. A predefinição é *false* porque novas coleções são criadas com débito reservado, que tem custos implicações. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Quando `CreateIfNotExists` é true, ele define o caminho de chave de partição para a coleção criada.|
|**collectionThroughput**|**CollectionThroughput**| Quando `CreateIfNotExists` é true, ele define a [taxa de transferência](../cosmos-db/set-throughput.md) da coleção criada.|
|**connectionStringSetting**    |**ConnectionStringSetting** |O nome da definição de aplicação que contém a cadeia de ligação do Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

Por predefinição, quando escreve para o parâmetro de saída na sua função, um documento é criado na base de dados. Este documento tem um GUID gerado automaticamente, como o ID do documento. Pode especificar o ID do documento do documento de saída especificando o `id` propriedade no objeto JSON passado para o parâmetro de saída.

> [!Note]
> Quando especificar o ID de um documento existente, seja substituída pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| CosmosDB | [Códigos de erro do cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para essa ligação na versão 2.x. Para obter mais informações sobre as definições de configuração global na versão 2.x, consulte [referência de Host. JSON para a versão das funções do Azure 2.x](functions-host-json.md).

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
|GatewayMode|Gateway|O modo de conexão usado pela função ao se conectar ao serviço de Azure Cosmos DB. As opções são `Direct` e `Gateway`|
|Protocolo|Https|O protocolo de conexão usado pela função quando a conexão com o serviço de Azure Cosmos DB.  Leia [aqui para obter uma explicação dos dois modos](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/d|Prefixo de concessão a ser usado em todas as funções em um aplicativo.|

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a base de dados sem servidor computação com o Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
