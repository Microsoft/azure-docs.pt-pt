---
title: Azure Cosmos DB enlaces para as funções 2.x
description: Compreenda como utilizar o Azure Cosmos DB acionadores e enlaces nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5762e934d7735dd9617cefc1f56105823d74312f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782195"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Cosmos DB enlaces das funções do Azure 2.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este artigo explica como trabalhar com [do Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) enlaces no funções do Azure 2.x. Funções do Azure suporta acionaram, de entrada e saída enlaces para o Azure Cosmos DB.

> [!NOTE]
> Este artigo destina-se [versão das funções do Azure 2.x](functions-versions.md).  Para obter informações sobre como utilizar essas ligações nas funções 1.x, consulte [do Azure Cosmos DB enlaces das funções do Azure 1.x](functions-bindings-cosmosdb.md).
>
> Este enlace foi originalmente denominado DocumentDB. Na versão de funções 2.x, o acionador, enlaces e pacote são todos com o nome do Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>APIs compatíveis

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

As ligações do Azure Cosmos DB para a versão de funções 2.x são fornecidos na [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) pacote NuGet, versão 3.x. É de código-fonte para as ligações na [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

O acionador do Azure Cosmos DB utiliza a [Feed de alterações do Azure Cosmos DB](../cosmos-db/change-feed.md) para escutar as inserções e atualizações em várias partições. O feed de alterações publica inserções e atualizações, eliminações não.

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de idioma específico:

* [C#](#trigger---c-example)
* [Script do c# (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

Ignorar os exemplos de Acionador

### <a name="trigger---c-example"></a>Acionador - exemplo do c#

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

Ignorar os exemplos de Acionador

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo seguinte mostra um acionador Cosmos DB ligando uma *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função escreve as mensagens de registo quando os registos de Cosmos DB são modificados.

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

Ignorar os exemplos de Acionador

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo seguinte mostra um acionador Cosmos DB ligando uma *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função escreve as mensagens de registo quando os registos de Cosmos DB são modificados.

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

### <a name="trigger---java-example"></a>Acionador - exemplo de Java

O exemplo seguinte mostra um acionador Cosmos DB ligando *Function* ficheiro e uma [função Java](functions-reference-java.md) que utiliza o enlace. A função está envolvida quando existem insere ou atualiza a coleção e a base de dados especificada.

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


Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@CosmosDBTrigger` anotação em parâmetros cujo valor virá do Cosmos DB.  Esta anotação pode ser utilizada com tipos nativos de Java, POJOs ou valores anuláveis usando opcional<T>.


Ignorar os exemplos de Acionador

### <a name="trigger---python-example"></a>Acionador - exemplo de Python

O exemplo seguinte mostra um acionador Cosmos DB ligando uma *Function* ficheiro e uma [função Python](functions-reference-python.md) que utiliza o enlace. A função escreve as mensagens de registo quando os registos de Cosmos DB são modificados.

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

Aqui está o código de Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

## <a name="trigger---c-attributes"></a>Acionador - c# atributos

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

Para obter um exemplo completo, consulte [acionador - exemplo do c#](#trigger---c-example).


## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `CosmosDBTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** || Tem de ser definido como `cosmosDBTrigger`. |
|**direção** || Tem de ser definido como `in`. Este parâmetro é definido automaticamente quando criar o acionador no portal do Azure. |
|**name** || O nome da variável no código de função que representa a lista de documentos com as alterações. |
|**connectionStringSetting**|**ConnectionStringSetting** | O nome de uma definição de aplicação que contém a cadeia de ligação utilizada para ligar à conta do Azure Cosmos DB a ser monitorizada. |
|**databaseName**|**DatabaseName**  | O nome da base de dados do Azure Cosmos DB com a coleção a ser monitorizada. |
|**collectionName** |**CollectionName** | O nome da coleção a ser monitorizado. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcional) O nome de uma definição de aplicação que contém a cadeia de ligação para o serviço que mantém a coleção de concessão. Quando não definido, o `connectionStringSetting` valor é utilizado. Este parâmetro é definido automaticamente quando a associação é criada no portal. A cadeia de ligação para a coleção de concessões tem de ter permissões de escrita.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) O nome da base de dados que contém a coleção utilizada para armazenar as concessões. Quando não definido, o valor da `databaseName` definição é utilizada. Este parâmetro é definido automaticamente quando a associação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção utilizada para armazenar as concessões. Quando não definido, o valor `leases` é utilizado. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcional) Quando definido como `true`, a coleção de concessões é criada automaticamente quando ainda não exista. O valor predefinido é `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Opcional) Define a quantidade de unidades de pedido para atribuir quando é criada a coleção de concessões. Esta definição é apenas utilizado quando `createLeaseCollectionIfNotExists` está definido como `true`. Este parâmetro é definido automaticamente quando a associação é criada com o portal.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| (Opcional) Quando definido, ele adiciona um prefixo para as concessões criadas na coleção de concessão para essa função, permitindo efetivamente dois as funções do Azure separadas partilhar a mesma coleção de concessão por diferentes prefixos a utilizar.
|**feedPollDelay**| **feedPollDelay**| (Opcional) Quando o conjunto, define, em milissegundos, o atraso entre uma partição para novas alterações no feed de consulta são drenadas alterações Afinal de contas atuais. A predefinição é de 5000 (5 segundos).
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo de disparar uma tarefa de computação se as partições são distribuídas uniformemente entre instâncias de host conhecidos. A predefinição é 13000 (13 segundos).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo para o qual a concessão é criada numa concessão que representa uma partição. Se a concessão não for renovada dentro deste intervalo, fará com que expire e propriedade da partição irá mudar para outra instância. A predefinição é 60000 (60 segundos).
|**leaseRenewInterval**| **leaseRenewInterval**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo de renovação para todas as concessões para partições atualmente mantido por uma instância. A predefinição é 17000 (17 segundos).
|**checkpointFrequency**| **checkpointFrequency**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo entre pontos de verificação de concessão. A predefinição é sempre após cada chamada de função.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| (Opcional) Quando definida, personaliza a quantidade máxima de itens recebidas por chamada de função.
|**startFromBeginning**| **StartFromBeginning**| (Opcional) Quando definido, ele instrui o acionador para começar a ler as alterações desde o início da história da coleção em vez da hora atual. Isso funciona apenas na primeira vez iniciada a acionador, tal como as execuções posteriores, os pontos de verificação já está armazenadas. Definir este tipo como `true` quando houver concessões criadas já não tem qualquer efeito.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

O acionador requer uma segunda coleção, que utiliza para armazenar _concessões_ nas partições. A coleção a ser monitorizado e a coleção que contém as concessões tem de estar disponíveis para o acionador trabalhar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para utilizar um acionador Cosmos DB para a mesma coleção, cada uma das funções deve usar uma coleção de concessão dedicado ou especificar um `LeaseCollectionPrefix` para cada função. Caso contrário, será acionada apenas uma das funções. Para obter informações sobre o prefixo, consulte a [seção de configuração](#trigger---configuration).

O acionador não indica que se um documento foi atualizado ou inserido, ele fornece apenas o próprio documento. Se precisar manipular atualizações e inserções de forma diferente, poderia fazê-lo através da implementação timestamp campos para inserção ou atualização.

## <a name="input"></a>Input

O enlace de entrada do Azure Cosmos DB utiliza a API de SQL para obter um ou mais documentos do Azure Cosmos DB e os passa para o parâmetro de entrada da função. Os parâmetros de consulta ou ID do documento podem ser determinados com base no acionador que invoca a função.

## <a name="input---examples"></a>Introdução - exemplos

Veja os exemplos de idioma específico de leitura de um único documento, especificando um valor de ID:

* [C#](#input---c-examples)
* [Script do c# (.csx)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---c-examples"></a>Introdução - exemplos de c#

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
        public string Description { get; set; }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Acionador de fila, procura de ID de JSON (c#)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é acionada por uma mensagem de fila que contém um objeto JSON. O acionador de fila analisa o JSON num objeto chamado `ToDoItemLookup`, que contém o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Acionador HTTP, procura de ID da cadeia de consulta (c#)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Acionador HTTP, procura de ID de dados da rota (c#)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza encaminhar os dados para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Acionador HTTP, procura de ID de encaminhar os dados, usando SqlQuery (c#)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza encaminhar os dados para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

O exemplo mostra como utilizar uma expressão de vinculação no `SqlQuery` parâmetro. Pode passar dados da rota para o `SqlQuery` parâmetro, como mostrado, mas, atualmente [não poder passar os valores de cadeia de caracteres de consulta](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


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

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP acionar, obter vários documentos, usando SqlQuery (c#)

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP acionar, obter vários documentos, com o DocumentClient (c#)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que obtém uma lista de documentos. A função é acionada por um pedido HTTP. O código utiliza um `DocumentClient` instância fornecida pelo enlace do Azure Cosmos DB para ler uma lista de documentos. O `DocumentClient` instância também poderia ser utilizada para operações de escrita.

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

### <a name="input---c-script-examples"></a>Introdução - exemplos de script do c#

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Acionador de fila, procura de ID de cadeia de caracteres (c# script)

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Acionador da fila, obter vários documentos, usando SqlQuery (c# script)

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Acionador HTTP, procura de ID da cadeia de consulta (c# script)

A exemplo a seguir mostra um [função de script do c#](functions-reference-csharp.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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
      "Id": "{Query.id}"
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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Acionador HTTP, procura de ID de dados da rota (c# script)

A exemplo a seguir mostra um [função de script do c#](functions-reference-csharp.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza encaminhar os dados para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP acionar, obter vários documentos, usando SqlQuery (c# script)

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP acionar, obter vários documentos, com o DocumentClient (c# script)

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

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---javascript-examples"></a>Introdução - exemplos de JavaScript

Esta secção contém os seguintes exemplos de leitura de um único documento, especificando um valor de ID de várias origens:

* [Acionador de fila, procura de ID de JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Acionador HTTP, procura de ID da cadeia de consulta](#http-trigger-look-up-id-from-query-string-javascript)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-javascript)
* [Acionador da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Acionador de fila, procura de ID de JSON (JavaScript)

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Acionador HTTP, procura de ID da cadeia de consulta (JavaScript)

A exemplo a seguir mostra um [função de JavaScript](functions-reference-node.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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
      "Id": "{Query.id}"
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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Acionador HTTP, procura de ID de dados da rota (JavaScript)

A exemplo a seguir mostra um [função de JavaScript](functions-reference-node.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Acionador da fila, obter vários documentos, usando SqlQuery (JavaScript)

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

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---python-examples"></a>Introdução - exemplos de Python

Esta secção contém os seguintes exemplos de leitura de um único documento, especificando um valor de ID de várias origens:

* [Acionador de fila, procura de ID de JSON](#queue-trigger-look-up-id-from-json-python)
* [Acionador HTTP, procura de ID da cadeia de consulta](#http-trigger-look-up-id-from-query-string-python)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-python)
* [Acionador da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Acionador de fila, procura de ID de JSON (Python)

O exemplo seguinte mostra um enlace de entrada do Cosmos DB num *Function* ficheiro e uma [função Python](functions-reference-python.md) que utiliza o enlace. A função lê um único documento e atualiza o valor de texto do documento.

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

Aqui está o código de Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>Acionador HTTP, procura de ID da cadeia de consulta (Python)

A exemplo a seguir mostra um [função Python](functions-reference-python.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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
      "Id": "{Query.id}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Aqui está o código de Python:

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

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>Acionador HTTP, procura de ID de dados da rota (Python)

A exemplo a seguir mostra um [função Python](functions-reference-python.md) que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Que ID é utilizado para obter um `ToDoItem` documento da coleção e base de dados especificada.

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

Aqui está o código de Python:

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

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Acionador da fila, obter vários documentos, usando SqlQuery (Python)

O exemplo seguinte mostra um enlace de entrada do Azure Cosmos DB num *Function* ficheiro e uma [função Python](functions-reference-python.md) que utiliza o enlace. A função obtém vários documentos especificados por uma consulta SQL, com um acionador de fila para personalizar os parâmetros de consulta.

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

Aqui está o código de Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Ignorar exemplos de entrada](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Entrada - F# exemplos

O exemplo seguinte mostra um enlace de entrada do Cosmos DB num *Function* ficheiro e uma [ F# função](functions-reference-fsharp.md) que utiliza o enlace. A função lê um único documento e atualiza o valor de texto do documento.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#input---configuration) seção explica essas propriedades.

Aqui está o F# código:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Este exemplo requer um `project.json` ficheiro que especifica o `FSharp.Interop.Dynamic` e `Dynamitey` dependências NuGet:

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

Para adicionar um `project.json` de ficheiros, consulte [ F# gestão de pacotes](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Introdução - exemplos de Java

Esta secção contém os exemplos seguintes:

* [Acionador HTTP, procura de ID da cadeia de consulta - parâmetro de cadeia de caracteres](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Acionador HTTP, procura de ID da cadeia de consulta - parâmetro POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Acionador HTTP, procura de ID de dados da rota](#http-trigger-look-up-id-from-route-data-java)
* [Acionador HTTP, procura de ID de encaminhar os dados, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP acionar, obter vários documentos de dados da rota, utilizando SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

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

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>Acionador HTTP, procura de ID da cadeia de consulta - parâmetro de cadeia de caracteres (Java)

O exemplo seguinte mostra uma função de Java que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Esse identificador é utilizado para obter um documento a partir da base de dados especificada e a coleção, na forma de cadeia de caracteres.

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

Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@CosmosDBInput` anotação nos parâmetros de função, cujo valor virá do Cosmos DB.  Esta anotação pode ser utilizada com tipos nativos de Java, POJOs ou valores anuláveis usando opcional<T>.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>Acionador HTTP, procura de ID da cadeia de consulta - parâmetro POJO (Java)

O exemplo seguinte mostra uma função de Java que obtém um único documento. A função é acionada por um pedido HTTP que utiliza uma cadeia de consulta para especificar o ID para procurar. Que ID é utilizado para obter um documento da coleção e base de dados especificada. O documento, em seguida, é convertido numa instância do ```ToDoItem``` POJO anteriormente criado e transmitido como argumento para a função.

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

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>Acionador HTTP, procura de ID de dados da rota (Java)

O exemplo seguinte mostra uma função de Java que obtém um único documento. A função é acionada por um pedido HTTP que utiliza um parâmetro de rotas para especificar o ID para procurar. Que ID é utilizado para obter um documento a partir da base de dados especificada e a coleção, retorná-la como um ```Optional<String>```.

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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>Acionador HTTP, procura de ID de encaminhar os dados, usando SqlQuery (Java)

O exemplo seguinte mostra uma função de Java que obtém um único documento. A função é acionada por um pedido HTTP que utiliza um parâmetro de rotas para especificar o ID para procurar. Que ID é usado para recuperar um documento da base de dados especificada e a coleção, converter o resultado definido como um ```ToDoItem[]```, uma vez que muitos documentos podem ser devolvidos, consoante os critérios de consulta.

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

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>HTTP acionar, obter vários documentos de dados da rota, utilizando SqlQuery (Java)

O exemplo seguinte mostra uma função de Java que vários documentos. A função é acionada por um pedido HTTP que utiliza um parâmetro de rota ```desc``` para especificar a cadeia de caracteres para pesquisa na ```description``` campo. O termo de pesquisa é usado para recuperar uma coleção de documentos a partir da base de dados especificada e a coleção, converter o conjunto de resultados para um ```ToDoItem[]``` e passando-o como um argumento para a função.

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

## <a name="input---attributes"></a>Introdução - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atributo.

Construtor do atributo pega o nome de base de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que pode configurar, consulte [a seguinte secção de configuração](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `CosmosDB` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     || Tem de ser definido como `cosmosDB`.        |
|**direção**     || Tem de ser definido como `in`.         |
|**name**     || Nome do parâmetro de enlace que representa o documento na função.  |
|**databaseName** |**DatabaseName** |A base de dados que contém o documento.        |
|**collectionName** |**CollectionName** | O nome da coleção que contém o documento. |
|**id**    | **Id** | O ID do documento a obter. Esta propriedade suporta [expressões de enlace](./functions-bindings-expressions-patterns.md). Não definir ambos os **id** e **sqlQuery** propriedades. Se não definir individualmente, toda a coleção é recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta de SQL do Azure Cosmos DB usada para recuperar vários documentos. A propriedade suporta ligações de tempo de execução, tal como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`. Não definir ambos os **id** e **sqlQuery** propriedades. Se não definir individualmente, toda a coleção é recuperada.|
|**connectionStringSetting**     |**ConnectionStringSetting**|O nome da definição de aplicação que contém a cadeia de ligação do Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Especifica o valor de chave de partição para a pesquisa. Pode incluir parâmetros de ligação.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

No C# e F# funções, quando a função é encerrado com êxito, todas as alterações efetuadas ao documento de entrada por meio de entrada nomeada parâmetros são mantidos automaticamente.

Nas funções de JavaScript, as atualizações não são feitas automaticamente ao sair de função. Em alternativa, utilize `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para disponibilizar as atualizações. Veja o exemplo de JavaScript.

## <a name="output"></a>Saída

A saída do Azure Cosmos DB ligação permite que escrever um novo documento para uma base de dados do Azure Cosmos DB com a API SQL.

## <a name="output---examples"></a>Saída - exemplos

Veja os exemplos de idioma específico:

* [C#](#output---c-examples)
* [Script do c# (.csx)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)

Consulte também os [entrado de exemplo](#input---c-examples) que utiliza `DocumentClient`.

[Ignorar os exemplos de saída](#output---attributes)

### <a name="output---c-examples"></a>Saída - C# exemplos

Esta secção contém os exemplos seguintes:

* Acionador de fila, um de gravação de documentos
* Acionador de fila, docs escrita usando IAsyncCollector

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

[Ignorar os exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Acionador de fila, o documento de uma gravação (c#)

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

[Ignorar os exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Acionador de fila, docs escrita usando IAsyncCollector (c#)

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

[Ignorar os exemplos de saída](#output---attributes)

### <a name="output---c-script-examples"></a>Saída - exemplos de script do c#

Esta secção contém os exemplos seguintes:

* Acionador de fila, um de gravação de documentos
* Acionador de fila, docs escrita usando IAsyncCollector

[Ignorar os exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Acionador de fila, o documento de uma gravação (c# script)

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Acionador de fila, docs escrita usando IAsyncCollector

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

[Ignorar os exemplos de saída](#output---attributes)

### <a name="output---javascript-examples"></a>Saída - exemplos de JavaScript

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

[Ignorar os exemplos de saída](#output---attributes)

### <a name="output---f-examples"></a>Saída - F# exemplos

O exemplo seguinte mostra uma saída de Azure Cosmos DB ligando uma *Function* ficheiro e uma [ F# função](functions-reference-fsharp.md) que utiliza o enlace. A função usa um enlace de entrada de fila de uma fila que recebe JSON no seguinte formato:

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

Aqui está o F# código:

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

Este exemplo requer um `project.json` ficheiro que especifica o `FSharp.Interop.Dynamic` e `Dynamitey` dependências NuGet:

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

Para adicionar um `project.json` de ficheiros, consulte [ F# gestão de pacotes](functions-reference-fsharp.md#package).

### <a name="output---java-examples"></a>Saída - exemplos de Java

* [Acionador de fila, guarde a mensagem para o banco de dados pelo valor de retorno](#queue-trigger-save-message-to-database-via-return-value-java)
* [Acionador HTTP, salvar um documento à base de dados por meio do valor de retorno](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Acionador HTTP, salvar um documento à base de dados por meio de OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Acionador HTTP, guardar vários documentos à base de dados por meio de OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Acionador de fila, guarde a mensagem para o banco de dados pelo valor de retorno (Java)

O exemplo seguinte mostra uma função de Java que adiciona um documento para uma base de dados com dados a partir de uma mensagem no armazenamento de filas.

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>Acionador HTTP, salvar um documento à base de dados por meio do valor de retorno (Java)

O exemplo seguinte mostra uma função de Java cuja assinatura está anotada com ```@CosmosDBOutput``` e tem o valor de retorno do tipo ```String```. O documento JSON devolvido pela função será escrito automaticamente para a coleção do cosmos DB correspondente.

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

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>Acionador HTTP, salvar um documento à base de dados por meio de OutputBinding (Java)

O exemplo seguinte mostra uma função de Java que escreve um documento do cosmos DB através de um ```OutputBinding<T>``` parâmetro de saída. Tenha em atenção que, nessa configuração, é o ```outputItem``` parâmetro tem de ser anotado com ```@CosmosDBOutput```, não a assinatura de função. Usando ```OutputBinding<T>``` permite que sua função tirar partido da associação ao escrever o documento no cosmos DB e também permite que retornando um valor diferente para o chamador de função, como um documento JSON ou XML.

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

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>Acionador HTTP, guardar vários documentos à base de dados por meio de OutputBinding (Java)

O exemplo seguinte mostra uma função de Java que escreve vários documentos para o cosmos DB através de um ```OutputBinding<T>``` parâmetro de saída. Tenha em atenção que, nessa configuração, é o ```outputItem``` parâmetro tem de ser anotado com ```@CosmosDBOutput```, não a assinatura de função. O parâmetro de saída ```outputItem``` tem uma lista de ```ToDoItem``` objetos como seu tipo de parâmetro de modelo. Usando ```OutputBinding<T>``` permite que sua função tirar partido da associação ao escrever os documentos no cosmos DB e também permite que retornando um valor diferente para o chamador de função, como um documento JSON ou XML.

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

Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@CosmosDBOutput` anotação sobre os parâmetros que serão escritos no Cosmos DB.  O tipo de parâmetro de anotação deve ser ```OutputBinding<T>```, em que T é um tipo de Java nativo ou um POJO.


## <a name="output---attributes"></a>Saída - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) atributo.

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

Para obter um exemplo completo, ver um resultado - C# exemplo.

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `CosmosDB` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**     || Tem de ser definido como `cosmosDB`.        |
|**direção**     || Tem de ser definido como `out`.         |
|**name**     || Nome do parâmetro de enlace que representa o documento na função.  |
|**databaseName** | **DatabaseName**|A base de dados que contém a coleção onde o documento é criado.     |
|**collectionName** |**CollectionName**  | O nome da coleção em que o documento é criado. |
|**createIfNotExists**  |**CreateIfNotExists**    | Um valor booleano para indicar se a coleção é criada quando não existe. A predefinição é *false* porque novas coleções são criadas com débito reservado, que tem custos implicações. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Quando `CreateIfNotExists` é true, define o caminho de chave de partição para a coleção criada.|
|**collectionThroughput**|**CollectionThroughput**| Quando `CreateIfNotExists` for verdadeira, define a [débito](../cosmos-db/set-throughput.md) da coleção criada.|
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
|GatewayMode|Gateway|O modo de ligação utilizado pela função, ao ligar ao serviço do Azure Cosmos DB. As opções são `Direct` e `Gateway`|
|Protocolo|Https|O protocolo de ligação utilizado pela função quando a ligação ao serviço do Azure Cosmos DB.  Leitura [aqui para obter uma explicação de ambos os modos](../cosmos-db/performance-tips.md#networking)| 
|leasePrefix|n/d|Prefixo de concessão para utilizar em todas as funções num aplicativo.| 

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre a base de dados sem servidor computação com o Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
