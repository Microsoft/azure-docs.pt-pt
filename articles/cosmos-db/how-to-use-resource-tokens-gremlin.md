---
title: Use fichas de recurso Azure Cosmos DB com o Gremlin SDK
description: Aprenda a criar fichas de recursos e use-as para aceder à base de dados do Graph.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 42f3c7f3351bddab429489dccf28587549d76e18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78897838"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Use fichas de recurso Azure Cosmos DB com o Gremlin SDK

Este artigo explica como usar fichas de [recursos Da Azure Cosmos DB](secure-access-to-data.md) para aceder à base de dados do Graph através do Gremlin SDK.

## <a name="create-a-resource-token"></a>Criar um símbolo de recurso

O Apache TinkerPop Gremlin SDK não tem uma API para usar para criar tokens de recursos. O termo *recurso é* um conceito De Db Azure Cosmos. Para criar tokens de recursos, descarregue o [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Se a sua aplicação precisar de criar tokens de recursos e usá-los para aceder à base de dados do Graph, requer dois SDKs separados.

A hierarquia do modelo de objeto acima das fichas de recurso é ilustrada no seguinte esboço:

- **Conta Azure Cosmos DB** - A entidade de alto nível que `contoso.gremlin.cosmos.azure.com`tem um DNS associado a ele (por exemplo, ).
  - **Base de dados Azure Cosmos DB**
    - **Utilizador**
      - **Permissão**
        - **Token** - Uma propriedade de objeto de permissão que denota que ações são permitidas ou negadas.

Um token de recurso `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`utiliza o seguinte formato: . Esta cadeia é opaca para os clientes e deve ser usada como está, sem modificação ou interpretação.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Use um símbolo de recurso
Pode utilizar fichas de recurso diretamente como uma propriedade "password" quando construir a classe GremlinServer.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

A mesma abordagem funciona em todos os SDKs TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limite

Com uma única conta Gremlin, pode emitir um número ilimitado de fichas. No entanto, só pode utilizar até 100 fichas em simultâneo dentro de 1 hora. Se uma aplicação exceder o limite do token por hora, um pedido de autenticação é negado e recebe a seguinte mensagem de erro: "Limite de recurso de 100 que pode ser usado simultaneamente." Não funciona para fechar ligações ativas que usam fichas específicas para libertar slots para novos tokens. O motor de base de dados Azure Cosmos DB Gremlin regista fichas únicas durante a hora imediatamente anterior ao pedido de autenticação.

## <a name="permission"></a>Permissão

Um erro comum que as aplicações encontram enquanto usam fichas de recurso é: "Permissões insuficientes fornecidas no cabeçalho de autorização para o pedido correspondente. Por favor, tente novamente com outro cabeçalho de autorização. Este erro é devolvido quando um Gremlin tenta escrever uma borda ou um vértice, mas o recurso *concede* apenas permissões. Inspecione a sua travessia para ver se contém algum dos seguintes passos: *.addV()* *.addE()*, *.drop() ou* *.property()*.

## <a name="next-steps"></a>Passos seguintes
* [Controlo de acesso baseado em funções](role-based-access-control.md) em Azure Cosmos DB
* [Saiba como garantir o acesso aos dados](secure-access-to-data.md) no Azure Cosmos DB
