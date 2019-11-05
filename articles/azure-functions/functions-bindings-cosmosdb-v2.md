---
title: Associações de Azure Cosmos DB para o Functions 2. x
description: Entenda como usar Azure Cosmos DB gatilhos e associações no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: d8aee88f6ef3f6a73beadfdf242d79d9b361de0a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469386"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB associações para Azure Functions 2. x

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este artigo explica como trabalhar com associações de [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) no Azure Functions 2. x. Azure Functions dá suporte a associações de gatilho, entrada e saída para Azure Cosmos DB.

> [!NOTE]
> Este artigo é para [Azure Functions versão 2. x](functions-versions.md).  Para obter informações sobre como usar essas associações no functions 1. x, consulte [Azure Cosmos DB associações para Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Essa associação foi originalmente denominada DocumentDB. Nas funções versão 2. x, o gatilho, as associações e o pacote são todos nomeados Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>APIs com suporte

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Pacotes – funções 2. x

As associações de Azure Cosmos DB para as funções versão 2. x são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) , versão 3. x. O código-fonte para as associações está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

O gatilho de Azure Cosmos DB usa o [Azure Cosmos DB o feed de alterações](../cosmos-db/change-feed.md) para escutar inserções e atualizações entre partições. O feed de alterações publica inserções e atualizações, não exclusões.

## <a name="trigger---example"></a>Gatilho-exemplo

Consulte o exemplo específico de linguagem:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Ignorar exemplos de gatilho

### <a name="trigger---c-example"></a>Gatilho- C# exemplo

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que é invocada quando há inserções ou atualizações no banco de dados e na coleção especificados.

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

Ignorar exemplos de gatilho

### <a name="trigger---c-script-example"></a>Gatilho- C# exemplo de script

O exemplo a seguir mostra uma associação de gatilho Cosmos DB em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função grava mensagens de log quando Cosmos DB registros são modificados.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

Este é o C# código de script:

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

Ignorar exemplos de gatilho

### <a name="trigger---javascript-example"></a>Gatilho-exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho Cosmos DB em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava mensagens de log quando Cosmos DB registros são modificados.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

Este é o código JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>Gatilho-exemplo de Java

O exemplo a seguir mostra uma associação de gatilho Cosmos DB no arquivo *Function. JSON* e uma [função Java](functions-reference-java.md) que usa a associação. A função é envolvida quando há inserções ou atualizações no banco de dados e na coleção especificados.

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

Aqui está o código Java:

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


Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBTrigger` em parâmetros cujo valor venha de Cosmos DB.  Esta anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando > opcional\<T.


Ignorar exemplos de gatilho

### <a name="trigger---python-example"></a>Gatilho-exemplo de Python

O exemplo a seguir mostra uma associação de gatilho Cosmos DB em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função grava mensagens de log quando Cosmos DB registros são modificados.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

## <a name="trigger---c-attributes"></a>Gatilho- C# atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

O construtor do atributo usa o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que você pode configurar, consulte [Trigger-Configuration](#trigger---configuration). Aqui está um exemplo de atributo `CosmosDBTrigger` em uma assinatura de método:

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

Para obter um exemplo completo, consulte [gatilho C# -exemplo](#trigger---c-example).


## <a name="trigger---configuration"></a>Gatilho-configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `CosmosDBTrigger`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** || Deve ser definido como `cosmosDBTrigger`. |
|**direção** || Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria o gatilho no portal do Azure. |
|**nomes** || O nome da variável usada no código de função que representa a lista de documentos com alterações. |
|**connectionStringSetting**|**ConnectionStringSetting** | O nome de uma configuração de aplicativo que contém a cadeia de conexão usada para se conectar à conta de Azure Cosmos DB que está sendo monitorada. |
|**NomeDoBancoDeDados**|**NomeDoBancoDeDados**  | O nome do banco de dados Azure Cosmos DB com a coleção que está sendo monitorada. |
|**collectionName** |**CollectionName** | O nome da coleção que está sendo monitorada. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Adicional O nome de uma configuração de aplicativo que contém a cadeia de conexão para o serviço que contém a coleção de concessão. Quando não definido, o valor de `connectionStringSetting` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no Portal. A cadeia de conexão para a coleção de concessões deve ter permissões de gravação.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Adicional O nome do banco de dados que contém a coleção usada para armazenar concessões. Quando não definido, o valor da configuração de `databaseName` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no Portal. |
|**leaseCollectionName** | **LeaseCollectionName** | Adicional O nome da coleção usada para armazenar concessões. Quando não definido, o valor `leases` é usado. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Adicional Quando definido como `true`, a coleção de concessões é criada automaticamente quando ela ainda não existe. O valor padrão é `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Adicional Define a quantidade de unidades de solicitação a serem atribuídas quando a coleção de concessões é criada. Essa configuração só é usada quando `createLeaseCollectionIfNotExists` é definido como `true`. Esse parâmetro é definido automaticamente quando a associação é criada usando o Portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Adicional Quando definido, ele adiciona um prefixo às concessões criadas na coleção de concessão para essa função, permitindo efetivamente que duas Azure Functions separadas compartilhem a mesma coleção de concessão usando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| Adicional Quando definido, ele define, em milissegundos, o atraso entre a sondagem de uma partição para novas alterações no feed, após a descarga de todas as alterações atuais. O padrão é 5000 (5 segundos).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Adicional Quando definido, ele define, em milissegundos, o intervalo para disparar uma tarefa a ser computada se as partições forem distribuídas uniformemente entre as instâncias de host conhecidas. O padrão é 13000 (13 segundos).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Adicional Quando definido, ele define, em milissegundos, o intervalo para o qual a concessão é executada em uma concessão que representa uma partição. Se a concessão não for renovada dentro desse intervalo, ela fará com que ela expire e a propriedade da partição será movida para outra instância. O padrão é 60000 (60 segundos).
|**leaseRenewInterval**| **LeaseRenewInterval**| Adicional Quando definido, ele define, em milissegundos, o intervalo de renovação para todas as concessões de partições atualmente mantidas por uma instância. O padrão é 17000 (17 segundos).
|**checkpointFrequency**| **CheckpointFrequency**| Adicional Quando definido, ele define, em milissegundos, o intervalo entre pontos de verificação de concessão. O padrão é sempre após cada chamada de função.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Adicional Quando definido, essa propriedade define a quantidade máxima de itens recebidos por chamada de função. Se as operações na coleção monitorada forem executadas por meio de procedimentos armazenados, o [escopo da transação](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) será preservado durante a leitura de itens do feed de alterações. Como resultado disso, é possível que a quantidade de itens recebidos seja maior do que o valor especificado para que os itens alterados pela mesma transação sejam retornados como parte de um lote atômico.
|**startFromBeginning**| **StartFromBeginning**| Adicional Quando definido, ele informa ao gatilho para começar a ler as alterações desde o início do histórico da coleção, em vez da hora atual. Isso só funciona na primeira vez que o gatilho é iniciado, como nas execuções subsequentes, os pontos de verificação já estão armazenados. Definir isso como `true` quando há concessões já criadas não tem nenhum efeito.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho-uso

O gatilho requer uma segunda coleção que ele usa para armazenar as _concessões_ nas partições. A coleção que está sendo monitorada e a coleção que contém as concessões devem estar disponíveis para que o gatilho funcione.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para usar um gatilho Cosmos DB para a mesma coleção, cada uma das funções deverá usar uma coleção de concessão dedicada ou especificar um `LeaseCollectionPrefix` diferente para cada função. Caso contrário, apenas uma das funções será disparada. Para obter informações sobre o prefixo, consulte a [seção configuração](#trigger---configuration).

O gatilho não indica se um documento foi atualizado ou inserido, ele apenas fornece o documento em si. Se você precisar lidar com atualizações e inserções diferentes, poderá fazer isso implementando campos de carimbo de data/hora para inserção ou atualização.

## <a name="input"></a>Input

A associação de entrada de Azure Cosmos DB usa a API do SQL para recuperar um ou mais documentos de Azure Cosmos DB e os passa para o parâmetro de entrada da função. A ID do documento ou os parâmetros de consulta podem ser determinados com base no gatilho que invoca a função.

## <a name="input---examples"></a>Entrada-exemplos

Consulte os exemplos específicos de idioma que lêem um único documento especificando um valor de ID:

* [C#](#input---c-examples)
* [C#script (. CSX)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---c-examples"></a>Entrada- C# exemplos

Esta seção contém os seguintes exemplos:

* [Gatilho de fila, Pesquisar ID de JSON](#queue-trigger-look-up-id-from-json-c)
* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-c)
* [Gatilho HTTP, Pesquisar ID de dados de rota](#http-trigger-look-up-id-from-route-data-c)
* [Gatilho HTTP, Pesquisar ID de dados de rota, usando SQLQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Gatilho HTTP, obter vários documentos, usando SQLQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Gatilho HTTP, obtenha vários documentos, usando DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Os exemplos se referem a um tipo simples de `ToDoItem`:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Gatilho de fila, Pesquisar ID de JSON (C#)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma mensagem da fila que contém um objeto JSON. O gatilho de fila analisa o JSON em um objeto chamado `ToDoItemLookup`, que contém a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

```cs
namespace CosmosDBSamplesV2
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
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres deC#consulta ()

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

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
                Id = "{Query.id}")] ToDoItem toDoItem,
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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Gatilho HTTP, Pesquisar ID de dados de rota (C#)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa dados de rota para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

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
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Gatilho HTTP, Pesquisar ID de dados de rota, usando SQLQueryC#()

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa dados de rota para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

O exemplo mostra como usar uma expressão de associação no parâmetro `SqlQuery`. Você pode passar os dados de rota para o parâmetro `SqlQuery` como mostrado, mas atualmente [não é possível passar valores de cadeia de caracteres de consulta](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>Gatilho HTTP, obter vários documentos, usando SQLQueryC#()

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. A consulta é especificada na propriedade de atributo `SqlQuery`.

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Gatilho HTTP, obtenha vários documentos usando DocumentClient (C#)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. O código usa uma instância de `DocumentClient` fornecida pela Associação de Azure Cosmos DB para ler uma lista de documentos. A instância de `DocumentClient` também pode ser usada para operações de gravação.

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

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---c-script-examples"></a>Exemplos de C# script de entrada

Esta seção contém os seguintes exemplos:

* [Gatilho de fila, Pesquisar ID da cadeia de caracteres](#queue-trigger-look-up-id-from-string-c-script)
* [Gatilho de fila, obter vários documentos, usando SQLQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-c-script)
* [Gatilho HTTP, Pesquisar ID de dados de rota](#http-trigger-look-up-id-from-route-data-c-script)
* [Gatilho HTTP, obter vários documentos, usando SQLQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Gatilho HTTP, obtenha vários documentos, usando DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Os exemplos de gatilho HTTP se referem a um tipo simples de `ToDoItem`:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Gatilho de fila, Pesquisar ID da cadeia deC# caracteres (script)

O exemplo a seguir mostra uma associação de entrada Cosmos DB em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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
A seção de [configuração](#input---configuration) explica essas propriedades.

Este é o C# código de script:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Gatilho de fila, obter vários documentos, usando SQLQuery (C# script)

O exemplo a seguir mostra uma associação de entrada Azure Cosmos DB em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função recupera vários documentos especificados por uma consulta SQL, usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de fila fornece um parâmetro `departmentId`. Uma mensagem da fila de `{ "departmentId" : "Finance" }` retornaria todos os registros do departamento financeiro.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

A seção de [configuração](#input---configuration) explica essas propriedades.

Este é o C# código de script:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres deC# consulta (script)

O exemplo a seguir mostra uma [ C# função de script](functions-reference-csharp.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Este é o arquivo *Function. JSON* :

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

Este é o C# código de script:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Gatilho HTTP, Pesquisar ID de dados de rota (C# script)

O exemplo a seguir mostra uma [ C# função de script](functions-reference-csharp.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa dados de rota para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Este é o arquivo *Function. JSON* :

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
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Este é o C# código de script:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Gatilho HTTP, obter vários documentos, usando SQLQueryC# (script)

O exemplo a seguir mostra uma [ C# função de script](functions-reference-csharp.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. A consulta é especificada na propriedade de atributo `SqlQuery`.

Este é o arquivo *Function. JSON* :

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

Este é o C# código de script:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>Gatilho HTTP, obtenha vários documentos, usando DocumentClient (C# script)

O exemplo a seguir mostra uma [ C# função de script](functions-reference-csharp.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. O código usa uma instância de `DocumentClient` fornecida pela Associação de Azure Cosmos DB para ler uma lista de documentos. A instância de `DocumentClient` também pode ser usada para operações de gravação.

Este é o arquivo *Function. JSON* :

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

Este é o C# código de script:

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

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---javascript-examples"></a>Entrada-exemplos de JavaScript

Esta seção contém os exemplos a seguir que lêem um único documento especificando um valor de ID de várias fontes:

* [Gatilho de fila, Pesquisar ID de JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-javascript)
* [Gatilho HTTP, Pesquisar ID de dados de rota](#http-trigger-look-up-id-from-route-data-javascript)
* [Gatilho de fila, obter vários documentos, usando SQLQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Gatilho de fila, Pesquisar ID de JSON (JavaScript)

O exemplo a seguir mostra uma associação de entrada Cosmos DB em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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
A seção de [configuração](#input---configuration) explica essas propriedades.

Este é o código JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta (JavaScript)

O exemplo a seguir mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Este é o arquivo *Function. JSON* :

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

Este é o código JavaScript:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Gatilho HTTP, Pesquisar ID de dados de rota (JavaScript)

O exemplo a seguir mostra uma [função JavaScript](functions-reference-node.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Este é o arquivo *Function. JSON* :

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
      "type": "cosmosDB",
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

Este é o código JavaScript:

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Gatilho de fila, obter vários documentos, usando SQLQuery (JavaScript)

O exemplo a seguir mostra uma associação de entrada Azure Cosmos DB em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função recupera vários documentos especificados por uma consulta SQL, usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de fila fornece um parâmetro `departmentId`. Uma mensagem da fila de `{ "departmentId" : "Finance" }` retornaria todos os registros do departamento financeiro.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

A seção de [configuração](#input---configuration) explica essas propriedades.

Este é o código JavaScript:

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

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---python-examples"></a>Exemplos de entrada-Python

Esta seção contém os exemplos a seguir que lêem um único documento especificando um valor de ID de várias fontes:

* [Gatilho de fila, Pesquisar ID de JSON](#queue-trigger-look-up-id-from-json-python)
* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-python)
* [Gatilho HTTP, Pesquisar ID de dados de rota](#http-trigger-look-up-id-from-route-data-python)
* [Gatilho de fila, obter vários documentos, usando SQLQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Gatilho de fila, Pesquisar ID de JSON (Python)

O exemplo a seguir mostra uma associação de entrada Cosmos DB em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

A seção de [configuração](#input---configuration) explica essas propriedades.

Este é o código Python:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta (Python)

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Este é o arquivo *Function. JSON* :

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
      "Id": "{Query.id}"
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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>Gatilho HTTP, Pesquisar ID de dados de rota (Python)

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento `ToDoItem` do banco de dados e da coleção especificados.

Este é o arquivo *Function. JSON* :

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
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Gatilho de fila, obter vários documentos, usando SQLQuery (Python)

O exemplo a seguir mostra uma associação de entrada Azure Cosmos DB em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função recupera vários documentos especificados por uma consulta SQL, usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de fila fornece um parâmetro `departmentId`. Uma mensagem da fila de `{ "departmentId" : "Finance" }` retornaria todos os registros do departamento financeiro.

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

A seção de [configuração](#input---configuration) explica essas propriedades.

Este é o código Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Ignorar exemplos de entrada](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Entrada- F# exemplos

O exemplo a seguir mostra uma associação de entrada Cosmos DB em um arquivo *Function. JSON* e uma [ F# função](functions-reference-fsharp.md) que usa a associação. A função lê um único documento e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *Function. JSON* :

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

A seção de [configuração](#input---configuration) explica essas propriedades.

Este é o F# código:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Este exemplo requer um arquivo `project.json` que especifica o `FSharp.Interop.Dynamic` e `Dynamitey` dependências do NuGet:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Para adicionar um arquivo de `project.json`, consulte [ F# gerenciamento de pacotes](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Entrada-exemplos de Java

Esta seção contém os seguintes exemplos:

* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro de cadeia de caracteres](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Gatilho HTTP, Pesquisar ID de dados de rota](#http-trigger-look-up-id-from-route-data-java)
* [Gatilho HTTP, Pesquisar ID de dados de rota, usando SQLQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Gatilho HTTP, obter vários documentos de dados de rota, usando SQLQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Os exemplos se referem a um tipo simples de `ToDoItem`:

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

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro de cadeia de caracteres (Java)

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento do banco de dados e da coleção especificados, na forma de cadeia de caracteres.

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
              partitionKey = "{Query.id}",
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

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBInput` em parâmetros de função cujo valor venha de Cosmos DB.  Esta anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando > opcional\<T.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>Gatilho HTTP, Pesquisar ID da cadeia de caracteres de consulta-parâmetro POJO (Java)

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento do banco de dados e da coleção especificados. O documento é então convertido em uma instância do ```ToDoItem``` POJO criado anteriormente e passado como um argumento para a função.

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
              partitionKey = "{Query.id}",
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

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>Gatilho HTTP, Pesquisar ID de dados de rota (Java)

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa um parâmetro de rota para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento do banco de dados e da coleção especificados, retornando-o como um ```Optional<String>```.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{id}",
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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>Gatilho HTTP, Pesquisar ID de dados de rota, usando SQLQuery (Java)

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é disparada por uma solicitação HTTP que usa um parâmetro de rota para especificar a ID a ser pesquisada. Essa ID é usada para recuperar um documento do banco de dados e da coleção especificados, convertendo o conjunto de resultados em um ```ToDoItem[]```, já que muitos documentos podem ser retornados, dependendo dos critérios de consulta.

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

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>Gatilho HTTP, obter vários documentos de dados de rota, usando SQLQuery (Java)

O exemplo a seguir mostra uma função Java que contém vários documentos. A função é disparada por uma solicitação HTTP que usa um parâmetro de rota ```desc``` para especificar a cadeia de caracteres a ser pesquisada no campo ```description```. O termo de pesquisa é usado para recuperar uma coleção de documentos do banco de dados e da coleção especificados, convertendo o conjunto de resultados em um ```ToDoItem[]``` e passando-o como um argumento para a função.

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

## <a name="input---attributes"></a>Entrada-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

O construtor do atributo usa o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que você pode configurar, consulte [a seção de configuração a seguir](#input---configuration).

## <a name="input---configuration"></a>Entrada-configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `CosmosDB`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     || Deve ser definido como `cosmosDB`.        |
|**direção**     || Deve ser definido como `in`.         |
|**nomes**     || Nome do parâmetro de associação que representa o documento na função.  |
|**NomeDoBancoDeDados** |**NomeDoBancoDeDados** |O banco de dados que contém o documento.        |
|**collectionName** |**CollectionName** | O nome da coleção que contém o documento. |
|**sessão**    | **Id** | A ID do documento a ser recuperado. Esta propriedade dá suporte a [expressões de associação](./functions-bindings-expressions-patterns.md). Não defina as propriedades **ID** e **SQLQuery** . Se você não definir um deles, toda a coleção será recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta SQL Azure Cosmos DB usada para recuperar vários documentos. A propriedade oferece suporte a associações de tempo de execução, como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`. Não defina as propriedades **ID** e **SQLQuery** . Se você não definir um deles, toda a coleção será recuperada.|
|**connectionStringSetting**     |**ConnectionStringSetting**|O nome da configuração do aplicativo que contém a cadeia de conexão Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Especifica o valor da chave de partição para a pesquisa. Pode incluir parâmetros de associação.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada-uso

Em C# funções F# e, quando a função é encerrada com êxito, todas as alterações feitas no documento de entrada por meio de parâmetros de entrada nomeados são persistidas automaticamente.

Em funções de JavaScript, as atualizações não são feitas automaticamente após a saída da função. Em vez disso, use `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para fazer atualizações. Consulte o exemplo de JavaScript.

## <a name="output"></a>Saída

A associação de saída Azure Cosmos DB permite que você grave um novo documento em um banco de dados Azure Cosmos DB usando a API do SQL.

## <a name="output---examples"></a>Saída-exemplos

Consulte os exemplos específicos de idioma:

* [C#](#output---c-examples)
* [C#script (. CSX)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

Consulte também o [exemplo de entrada](#input---c-examples) que usa `DocumentClient`.

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---c-examples"></a>Saída- C# exemplos

Esta seção contém os seguintes exemplos:

* Gatilho de fila, gravar um documento
* Gatilho de fila, gravar documentos usando IAsyncCollector

Os exemplos se referem a um tipo simples de `ToDoItem`:

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

[Ignorar exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Gatilho de fila, gravar um documentoC#()

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que adiciona um documento a um banco de dados do, usando o dado fornecido na mensagem do armazenamento de filas.

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

[Ignorar exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Gatilho de fila, gravar documentos usando IAsyncCollectorC#()

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que adiciona uma coleção de documentos a um banco de dados do, usando aqueles fornecidos em uma mensagem de fila JSON.

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

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---c-script-examples"></a>Exemplos de C# script de saída

Esta seção contém os seguintes exemplos:

* Gatilho de fila, gravar um documento
* Gatilho de fila, gravar documentos usando IAsyncCollector

[Ignorar exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Gatilho de fila, gravar um documentoC# (script)

O exemplo a seguir mostra uma associação de saída Azure Cosmos DB em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria Azure Cosmos DB documentos no seguinte formato para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

A seção de [configuração](#output---configuration) explica essas propriedades.

Este é o C# código de script:

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho de fila, gravar documentos usando IAsyncCollector

Para criar vários documentos, você pode associar a `ICollector<T>` ou `IAsyncCollector<T>` em que `T` é um dos tipos com suporte.

Este exemplo refere-se a um tipo de `ToDoItem` simples:

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

Este é o arquivo function. JSON:

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

Este é o C# código de script:

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

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---javascript-examples"></a>Saída-exemplos de JavaScript

O exemplo a seguir mostra uma associação de saída Azure Cosmos DB em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria Azure Cosmos DB documentos no seguinte formato para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *Function. JSON* :

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

A seção de [configuração](#output---configuration) explica essas propriedades.

Este é o código JavaScript:

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

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---f-examples"></a>Saída- F# exemplos

O exemplo a seguir mostra uma associação de saída Azure Cosmos DB em um arquivo *Function. JSON* e uma [ F# função](functions-reference-fsharp.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria Azure Cosmos DB documentos no seguinte formato para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *Function. JSON* :

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
A seção de [configuração](#output---configuration) explica essas propriedades.

Este é o F# código:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Este exemplo requer um arquivo `project.json` que especifica o `FSharp.Interop.Dynamic` e `Dynamitey` dependências do NuGet:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Para adicionar um arquivo de `project.json`, consulte [ F# gerenciamento de pacotes](functions-reference-fsharp.md#package).

### <a name="output---java-examples"></a>Saída-exemplos de Java

* [Gatilho de fila, salvar mensagem no banco de dados por meio do valor de retorno](#queue-trigger-save-message-to-database-via-return-value-java)
* [Gatilho HTTP, salve um documento no banco de dados por meio do valor de retorno](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Gatilho HTTP, salve um documento no banco de dados via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Gatilho HTTP, salve vários documentos no banco de dados por meio de Saídabinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Gatilho de fila, salvar mensagem no banco de dados por meio de valor de retorno (Java)

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>Gatilho HTTP, salve um documento no banco de dados por meio do valor de retorno (Java)

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

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>Gatilho HTTP, salve um documento no banco de dados via OutputType (Java)

O exemplo a seguir mostra uma função Java que grava um documento para CosmosDB por meio de um ```OutputBinding<T>``` parâmetro de saída. Observe que, nessa configuração, é o parâmetro ```outputItem``` que precisa ser anotado com ```@CosmosDBOutput```, não a assinatura de função. O uso de ```OutputBinding<T>``` permite que sua função Aproveite a associação para gravar o documento em CosmosDB enquanto também permite retornar um valor diferente para o chamador da função, como um documento JSON ou XML.

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

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>Gatilho HTTP, salvar vários documentos no banco de dados por meio de Saídabinding (Java)

O exemplo a seguir mostra uma função Java que grava vários documentos em CosmosDB por meio de um ```OutputBinding<T>``` parâmetro de saída. Observe que, nessa configuração, é o parâmetro ```outputItem``` que precisa ser anotado com ```@CosmosDBOutput```, não a assinatura de função. O parâmetro de saída ```outputItem``` tem uma lista de objetos ```ToDoItem``` como seu tipo de parâmetro de modelo. O uso de ```OutputBinding<T>``` permite que sua função Aproveite a associação para gravar os documentos em CosmosDB enquanto também permite retornar um valor diferente para o chamador da função, como um documento JSON ou XML.

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

### <a name="output---python-examples"></a>Saída-exemplos do Python

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

## <a name="output---attributes"></a>Saída-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

O construtor do atributo usa o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [saída-configuração](#output---configuration). Aqui está um exemplo de atributo `CosmosDB` em uma assinatura de método:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Para obter um exemplo completo, consulte saída C# -exemplo.

## <a name="output---configuration"></a>Saída-configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `CosmosDB`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     || Deve ser definido como `cosmosDB`.        |
|**direção**     || Deve ser definido como `out`.         |
|**nomes**     || Nome do parâmetro de associação que representa o documento na função.  |
|**NomeDoBancoDeDados** | **NomeDoBancoDeDados**|O banco de dados que contém a coleção na qual o documento é criado.     |
|**collectionName** |**CollectionName**  | O nome da coleção na qual o documento é criado. |
|**createIfNotExists**  |**CreateIfNotExists**    | Um valor booliano para indicar se a coleção é criada quando ela não existe. O padrão é *false* porque novas coleções são criadas com taxa de transferência reservada, que tem implicações de custo. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Quando `CreateIfNotExists` é true, define o caminho da chave de partição para a coleção criada.|
|**collectionThroughput**|**CollectionThroughput**| Quando `CreateIfNotExists` é true, define a [taxa de transferência](../cosmos-db/set-throughput.md) da coleção criada.|
|**connectionStringSetting**    |**ConnectionStringSetting** |O nome da configuração do aplicativo que contém a cadeia de conexão Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída-uso

Por padrão, quando você grava o parâmetro de saída em sua função, um documento é criado no banco de dados. Este documento tem um GUID gerado automaticamente como a ID do documento. Você pode especificar a ID do documento de saída especificando a propriedade `id` no objeto JSON passado para o parâmetro de saída.

> [!Note]
> Quando você especifica a ID de um documento existente, ele é substituído pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vinculação | Referência |
|---|---|
| CosmosDB | [Códigos de erro CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>configurações de host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação na versão 2. x. Para obter mais informações sobre definições de configuração global na versão 2. x, consulte [referência de host. JSON para Azure Functions versão 2. x](functions-host-json.md).

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
|Gatewaymode|Gateway|O modo de conexão usado pela função ao se conectar ao serviço de Azure Cosmos DB. As opções são `Direct` e `Gateway`|
|Protocolo|https|O protocolo de conexão usado pela função quando a conexão com o serviço de Azure Cosmos DB.  Leia [aqui para obter uma explicação dos dois modos](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/d|Prefixo de concessão a ser usado em todas as funções em um aplicativo.|

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a computação de banco de dados sem servidor com o Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Saiba mais sobre os gatilhos e associações do Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
