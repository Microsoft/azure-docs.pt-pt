---
title: EXECUção de consulta SQL em Azure Cosmos DB
description: Aprenda a criar uma consulta SQL e execute-a em Azure Cosmos DB. Este artigo descreve como criar e executar uma consulta SQL usando REST API, .Net SDK, JavaScript SDK e vários outros SDKs.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74871266"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Execução de consulta Azure Cosmos DB SQL

Qualquer idioma capaz de fazer pedidos HTTP/HTTPS pode ligar para a API Cosmos DB REST. Cosmos DB também oferece bibliotecas de programação para linguagens de programação .NET, Node.js, JavaScript e Python. A API REST e as bibliotecas suportam a consulta através do SQL, e o .NET SDK também suporta [consultas LINQ.](sql-query-linq-to-sql.md)

Os exemplos a seguir mostram como criar uma consulta e submetê-la a uma conta de base de dados cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>API REST

Cosmos DB oferece um modelo de programação RESTful aberto em HTTP. O modelo de recursos consiste num conjunto de recursos numa conta de base de dados, que prevê uma assinatura Azure. A conta de base de dados consiste num conjunto de bases de *dados,* cada uma das quais pode conter *vários contentores,* que por sua vez contêm *itens,* UDFs e outros tipos de recursos. Cada recurso Cosmos DB é endereçado usando um URI lógico e estável. Um conjunto de recursos é chamado de *feed*. 

O modelo básico de interação com estes recursos é através dos verbos HTTP `GET` , e , com as suas `PUT` `POST` `DELETE` interpretações padrão. Use `POST` para criar um novo recurso, execute um procedimento armazenado ou emita uma consulta cosmos DB. As consultas são sempre operações só de leitura sem efeitos secundários.

Os exemplos a seguir mostram uma `POST` consulta de API SQL contra os itens de amostra. A consulta tem um filtro simples na `name` propriedade JSON. O `x-ms-documentdb-isquery` e Tipo de Conteúdo: os `application/query+json` cabeçalhos denotam que a operação é uma consulta. `mysqlapicosmosdb.documents.azure.com:443`Substitua-a pela URI pela sua conta Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Os resultados são:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

A próxima consulta, mais complexa, retorna vários resultados de uma junção:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Os resultados são: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Se os resultados de uma consulta não puderem caber numa única página, a API REST devolve um token de continuação através do `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar resultados incluindo o cabeçalho nos resultados seguintes. Também pode controlar o número de resultados por página através do cabeçalho de `x-ms-max-item-count` números.

Se uma consulta tiver uma função de agregação como o COUNT, a página de consulta pode devolver um valor parcialmente agregado sobre apenas uma página de resultados. Os clientes devem realizar uma agregação de segundo nível sobre estes resultados para produzir os resultados finais. Por exemplo, soma sobre as contagens devolvidas nas páginas individuais para devolver a contagem total.

Para gerir a política de consistência de dados para consultas, utilize o `x-ms-consistency-level` cabeçalho como em todos os pedidos da API REST. A consistência da sessão também requer ecoar o mais recente `x-ms-session-token` cabeçalho de cookies no pedido de consulta. A política de indexação do contentor questionado também pode influenciar a consistência dos resultados da consulta. Com as definições de política de indexação padrão para contentores, o índice está sempre atual com o conteúdo do item, e os resultados de consulta correspondem à consistência escolhida para os dados. Para obter mais informações, consulte [Níveis de consistência DB de Azure Cosmos][níveis de consistência].

Se a política de indexação configurada no recipiente não puder suportar a consulta especificada, o servidor DB Azure Cosmos devolve 400 "Mau Pedido". Esta mensagem de erro retorna para consultas com caminhos explicitamente excluídos da indexação. Pode especificar o `x-ms-documentdb-query-enable-scan` cabeçalho para permitir que a consulta efetue uma verificação quando um índice não estiver disponível.

Pode obter métricas detalhadas sobre a execução de consultas, definindo o `x-ms-documentdb-populatequerymetrics` cabeçalho para `true` . Para obter mais informações, consulte [as métricas de consulta SQL para Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

O .NET SDK suporta a consulta LINQ e SQL. O exemplo a seguir mostra como executar a consulta de filtro anterior com .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo a seguir compara duas propriedades para a igualdade dentro de cada item, e utiliza projeções anónimas.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O próximo exemplo mostra a união, expressa através do `SelectMany` LINQ.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

O cliente .NET itera automaticamente através de todas as páginas de consulta resulta nos `foreach` blocos, como mostra o exemplo anterior. As opções de consulta introduzidas na secção [API REST](#REST-API) também estão disponíveis no .NET SDK, utilizando as `FeedOptions` classes e classes no `FeedResponse` `CreateDocumentQuery` método. Pode controlar o número de páginas utilizando a `MaxItemCount` definição.

Também pode controlar explicitamente a paging criando `IDocumentQueryable` usando o `IQueryable` objeto, em seguida, lendo os `ResponseContinuationToken` valores e passando-os de volta como `RequestContinuationToken` em `FeedOptions` . Pode `EnableScanInQuery` configurar para ativar as verificações quando a consulta não é suportada pela política de indexação configurada. Para recipientes divididos, pode utilizar `PartitionKey` para executar a consulta contra uma única partição, embora a Azure Cosmos DB possa extrair automaticamente isto do texto de consulta. Pode usar `EnableCrossPartitionQuery` para fazer consultas contra várias divisórias.

Para obter mais amostras .NET com consultas, consulte as [amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) no GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript server-side API

A Azure Cosmos DB fornece um modelo de programação para executar a lógica [de aplicação baseada em JavaScript](stored-procedures-triggers-udfs.md) diretamente em recipientes, utilizando procedimentos e gatilhos armazenados. A lógica JavaScript registada ao nível do contentor pode então emitir operações de base de dados nos itens do contentor dado, envoltos em transações de ACID ambiente.

O exemplo a seguir mostra como usar `queryDocuments` na API do servidor JavaScript para fazer consultas a partir de procedimentos e gatilhos armazenados no interior:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Níveis de consistência do Azure Cosmos DB](consistency-levels.md)
