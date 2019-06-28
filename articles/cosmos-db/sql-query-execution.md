---
title: Execução da consulta SQL no Azure Cosmos DB
description: Saiba mais sobre a execução da consulta SQL no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342595"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Execução de consultas do Azure Cosmos DB SQL

Qualquer linguagem com capacidade de efetuar pedidos HTTP/HTTPS, pode chamar a API de REST do Cosmos DB. O cosmos DB também oferece bibliotecas de programação para linguagens de programação .NET, node. js, JavaScript e Python. A API REST e de bibliotecas para todos os oferecem suporte à consulta através do SQL, e o SDK do .NET também suporta [LINQ consultar](sql-query-linq-to-sql.md).

Os exemplos seguintes mostram como criar uma consulta e enviá-lo numa conta de base de dados do Cosmos DB.

## <a id="REST-API"></a>REST API

O cosmos DB oferece um modelo de programação RESTful aberto através de HTTP. O modelo de recursos é composta por um conjunto de recursos numa conta de base de dados, que Aprovisiona uma subscrição do Azure. A conta de base de dados consiste num conjunto de *bases de dados*, cada um dos quais pode conter vários *contentores*, que por sua vez de conter *itens*, UDFs e outros tipos de recursos. Cada recurso do Cosmos DB é endereçável através de um URI de lógico e estável. Um conjunto de recursos é chamado um *feed*. 

O modelo de interação básicas com estes recursos é por meio de verbos HTTP `GET`, `PUT`, `POST`, e `DELETE`, com seus interpretações padrão. Utilize `POST` para criar um novo recurso, executar um procedimento armazenado ou emitir uma consulta do Cosmos DB. As consultas são sempre operações só de leitura com sem efeitos colaterais.

Os exemplos seguintes mostram uma `POST` para uma consulta de API de SQL em relação as itens de exemplo. A consulta tem um filtro simple no JSON `name` propriedade. O `x-ms-documentdb-isquery` e Content-Type: `application/query+json` cabeçalhos indicam que a operação é uma consulta. Substitua `mysqlapicosmosdb.documents.azure.com:443` com o URI para a sua conta do Cosmos DB.

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

A consulta seguinte, mais complexa devolve vários resultados de uma associação:

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

Se os resultados de uma consulta não é possível se encaixam numa única página, a API de REST devolve um token de continuação por meio do `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar os resultados, incluindo o cabeçalho nos resultados da subsequentes. Também pode controlar o número de resultados por página usando o `x-ms-max-item-count` cabeçalho número.

Se uma consulta tem uma função de agregação, como a CONTAGEM, a página de consulta pode retornar um valor parcialmente agregado ao longo de apenas uma página de resultados. Os clientes tem de efetuar uma agregação de segundo nível sobre esses resultados para produzir os resultados finais. Por exemplo, soma sobre as contagens devolvidas nas páginas individuais para devolver a contagem total.

Para gerir a política de consistência de dados para consultas, utilize o `x-ms-consistency-level` cabeçalho como em todos os pedidos de REST API. Consistência da sessão também requer que os ecoar a versão mais recente `x-ms-session-token` cabeçalho de cookie no pedido de consulta. Política de indexação do contentor consultados também pode influenciar a consistência dos resultados da consulta. Com as definições de política para contentores de indexação predefinidas, o índice é sempre atual com o conteúdo do item e os resultados da consulta correspondem a consistência escolhida para dados. Para obter mais informações, consulte [níveis de consistência do Azure Cosmos DB] [níveis de consistência].

Se a política de indexação configurada no contentor não é possível suportar a consulta especificada, o servidor do Azure Cosmos DB devolve 400 "Solicitação incorreta". Esta mensagem de erro, devolve para consultas, com caminhos explicitamente excluídos da indexação. Pode especificar o `x-ms-documentdb-query-enable-scan` cabeçalho para permitir que a consulta para realizar uma análise quando um índice não está disponível.

Pode obter métricas detalhadas na execução da consulta, definindo a `x-ms-documentdb-populatequerymetrics` cabeçalho para `true`. Para obter mais informações, consulte [métricas de consulta SQL para o Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

O SDK do .NET oferece suporte a LINQ e SQL consultar. O exemplo seguinte mostra como executar a consulta de filtro anterior com .NET:

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

O exemplo a seguir compara as duas propriedades de igualdade dentro de cada item e utiliza projeções anônimas.

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

O exemplo seguinte mostra as associações, expressadas por meio de LINQ `SelectMany`.

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

O cliente .NET automaticamente itera em todas as páginas de resultados da consulta no `foreach` bloquear, conforme mostrado no exemplo anterior. As opções de consulta introduzido no [REST API](#REST-API) secção também estão disponíveis no SDK do .NET, utilizando o `FeedOptions` e `FeedResponse` classes no `CreateDocumentQuery` método. Pode controlar o número de páginas utilizando o `MaxItemCount` definição.

Explicitamente também pode controlar a paginação, criando `IDocumentQueryable` utilizando o `IQueryable` objeto, em seguida, ao ler o `ResponseContinuationToken` valores e passá-los de volta como `RequestContinuationToken` no `FeedOptions`. Pode definir `EnableScanInQuery` para ativar a análise, quando a consulta não é suportada pela política de indexação configurada. Para contentores particionadas, pode usar `PartitionKey` para executar a consulta em relação a uma única partição, embora o Azure Cosmos DB pode automaticamente extrair isso o texto da consulta. Pode usar `EnableCrossPartitionQuery` para executar consultas em várias partições.

Para obter mais amostras de .NET com consultas, consulte a [exemplos de .NET do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet) no GitHub.

## <a id="JavaScript-server-side-API"></a>API de JavaScript do lado do servidor

O Azure Cosmos DB fornece um modelo de programação [executar o JavaScript, aplicação com base em](stored-procedures-triggers-udfs.md) lógica diretamente em contentores, utilizando os procedimentos armazenados e acionadores. A lógica de JavaScript registrada ao nível do contentor, em seguida, pode emitir operações de banco de dados nos itens do contentor determinado, encapsulado em ambiente transações ACID.

O exemplo seguinte mostra como utilizar `queryDocuments` na API para fazer consultas a partir do servidor de JavaScript dentro os procedimentos armazenados e acionadores:

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

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Níveis de consistência do Azure Cosmos DB](consistency-levels.md)
