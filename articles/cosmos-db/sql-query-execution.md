---
title: Execução de consulta SQL em Azure Cosmos DB
description: Aprenda a criar uma consulta SQL e executá-la em Azure Cosmos DB. Este artigo descreve como criar e executar uma consulta SQL usando REST API, .Net SDK, JavaScript SDK e vários outros SDKs.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871266"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Execução de consulta azure Cosmos DB SQL

Qualquer idioma capaz de fazer pedidos HTTP/HTTPS pode chamar a Cosmos DB REST API. Cosmos DB também oferece bibliotecas de programação para .NET, Node.js, JavaScript e Python. A Rest API e as bibliotecas suportam todas as consultas através do SQL, e o .NET SDK também suporta [a consulta de LINQ](sql-query-linq-to-sql.md).

Os seguintes exemplos mostram como criar uma consulta e submetê-la contra uma conta de base de dados da Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB oferece um modelo de programação RESTful aberto em HTTP. O modelo de recursos consiste num conjunto de recursos sob uma conta de base de dados, que uma disposição de subscrição do Azure. A conta de base de dados é constituída por um conjunto de bases de *dados,* cada uma das quais pode conter *vários contentores,* que por sua vez contêm *itens,* UDFs e outros tipos de recursos. Cada recurso Cosmos DB é endereçado usando um URI lógico e estável. Um conjunto de recursos é chamado de *feed*. 

O modelo básico de interação com estes `GET` `PUT`recursos `POST`é `DELETE`através dos verbos HTTP, e, com as suas interpretações padrão. Use `POST` para criar um novo recurso, executar um procedimento armazenado ou emitir uma consulta de Db Cosmos. As consultas são sempre operações apenas de leitura sem efeitos secundários.

Os seguintes exemplos mostram uma `POST` consulta de API SQL contra os itens da amostra. A consulta tem um filtro simples `name` na propriedade JSON. O `x-ms-documentdb-isquery` tipo e `application/query+json` conteúdo: os cabeçalhos denotam que a operação é uma consulta. Substitua-a `mysqlapicosmosdb.documents.azure.com:443` com o URI para a sua conta Cosmos DB.

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

A consulta seguinte, mais complexa, devolve vários resultados de uma adesão:

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

Se os resultados de uma consulta não encaixarem numa única página, a `x-ms-continuation-token` API REST devolve um sinal de continuação através do cabeçalho de resposta. Os clientes podem paginar resultados, incluindo o cabeçalho nos resultados seguintes. Também pode controlar o número de `x-ms-max-item-count` resultados por página através do cabeçalho de número.

Se uma consulta tiver uma função de agregação como COUNT, a página de consulta pode devolver um valor parcialmente agregado em apenas uma página dos resultados. Os clientes devem realizar uma agregação de segundo nível sobre estes resultados para produzir os resultados finais. Por exemplo, soma sobre as contagens devolvidas nas páginas individuais para devolver a contagem total.

Para gerir a política de consistência de `x-ms-consistency-level` dados para consultas, use o cabeçalho como em todos os pedidos da Rest API. A consistência da sessão também `x-ms-session-token` requer eco do mais recente cabeçalho de cookie saque no pedido de consulta. A política de indexação do contentor consultado também pode influenciar a consistência dos resultados da consulta. Com as definições de política de indexação padrão para contentores, o índice é sempre atual com o conteúdo do item, e os resultados da consulta correspondem à consistência escolhida para os dados. Para obter mais informações, consulte [Níveis de consistência do Azure Cosmos DB][níveis de consistência].

Se a política de indexação configurada no recipiente não puder suportar a consulta especificada, o servidor Azure Cosmos DB devolve 400 "Pedido Mau". Esta mensagem de erro retorna para consultas com caminhos explicitamente excluídos da indexação. Pode especificar `x-ms-documentdb-query-enable-scan` o cabeçalho para permitir que a consulta efetue uma varredura quando um índice não estiver disponível.

Você pode obter métricas detalhadas sobre `x-ms-documentdb-populatequerymetrics` a `true`execução de consulta, definindo o cabeçalho para . Para mais informações, consulte as métricas de [consulta SQL para Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

O SDK .NET suporta a consulta linq e SQL. O exemplo que se segue mostra como realizar a consulta do filtro anterior com .NET:

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

O exemplo seguinte compara duas propriedades para a igualdade dentro de cada item, e usa projeções anónimas.

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

O próximo exemplo mostra juntas, `SelectMany`expressas através do LINQ.

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

O cliente .NET iterates automaticamente através de todas `foreach` as páginas de consulta resulta nos blocos, como mostrado no exemplo anterior. As opções de consulta introduzidas na secção [REST API](#REST-API) também estão `FeedOptions` `FeedResponse` disponíveis no `CreateDocumentQuery` .NET SDK, utilizando as e classes no método. Pode controlar o número de `MaxItemCount` páginas utilizando a definição.

Também pode controlar explicitamente `IDocumentQueryable` a `IQueryable` pagagem criando usando `ResponseContinuationToken` o objeto, `RequestContinuationToken` em `FeedOptions`seguida, lendo os valores e passando-os de volta como em . Pode definir `EnableScanInQuery` para ativar as digitalizações quando a consulta não é suportada pela política de indexação configurada. Para recipientes divididos, `PartitionKey` pode utilizar para executar a consulta contra uma única divisória, embora o Azure Cosmos DB possa extraí-lo automaticamente do texto de consulta. Você pode `EnableCrossPartitionQuery` usar para executar consultas contra várias divisórias.

Para obter mais amostras .NET com consultas, consulte as [amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) no GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>API do lado do servidor JavaScript

A Azure Cosmos DB fornece um modelo de programação para executar a lógica de [aplicação baseada no JavaScript](stored-procedures-triggers-udfs.md) diretamente em contentores, utilizando procedimentos e gatilhos armazenados. A lógica JavaScript registada ao nível do contentor pode então emitir operações de base de dados nos itens do recipiente, embrulhados em transações de ACID ambiente.

O exemplo que se `queryDocuments` segue mostra como usar na API do servidor JavaScript para fazer consultas a partir de procedimentos e gatilhos armazenados internos:

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

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Níveis de consistência do Azure Cosmos DB](consistency-levels.md)
