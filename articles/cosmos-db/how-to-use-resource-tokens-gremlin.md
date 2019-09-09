---
title: Azure Cosmos DB tokens de recurso com Gremlin
description: Saiba como criar tokens de recurso e usá-los para acessar o banco de dados de grafo
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806912"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Azure Cosmos DB tokens de recurso com Gremlin
Este artigo explica como usar [Cosmos DB tokens de recurso](secure-access-to-data.md) para acessar o banco de dados de grafo por meio do SDK do Gremlin.

## <a name="create-a-resource-token"></a>Criar um token de recurso

O SDK do TinkerPop Gremlin não tem uma API para criar tokens de recurso. O token de recurso é um conceito de Cosmos DB. Para criar tokens de recurso, baixe [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Se seu aplicativo precisar criar tokens de recurso e usá-los para acessar o banco de dados de grafo, ele precisará de dois SDKs separados.

Hierarquia de modelo de objeto acima dos tokens de recurso:
- **Cosmos DB conta** -entidade de nível superior que tem o DNS associado a ela, por exemplo`contoso.gremlin.cosmos.azure.com`
  - **Banco de dados Cosmos DB**
    - **Usuário**
      - **Permissão**
        - *Token* -uma propriedade do objeto de **permissão** que denota quais ações são permitidas ou negadas.

O token de recurso tem `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`um formato. Essa cadeia de caracteres é opaca para os clientes e deve ser usada no estado em que se encontra sem modificação ou interpretação.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Usar um token de recurso
Tokens de recurso podem ser usados diretamente como a propriedade "password" `GremlinServer` ao construir classe.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

A mesma abordagem funciona em todos os SDKs do TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limite

Uma única conta Gremlin pode emitir um número ilimitado de tokens; no entanto, apenas até **100** tokens podem ser usados simultaneamente em **1 hora**. Se o aplicativo exceder o limite de token por hora, a solicitação de autenticação `"Exceeded allowed resource token limit of 100 that can be used concurrently"`será negada com a mensagem de erro. Fechar conexões ativas com tokens específicos para liberar slots para novos tokens não será proveitosa. Cosmos DB mecanismo de banco de dados Gremlin mantém o controle de tokens distintos na última hora antes da solicitação de autenticação.

## <a name="permission"></a>Permissão

Os aplicativos de erro comuns surgem durante o uso `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`de tokens de recurso. Esse erro é retornado quando o Gremlin percorre tentativas de gravar uma borda ou um vértice, mas o token `Read` de recurso concede permissões somente. Inspecione sua passagem se ela contiver qualquer uma das seguintes etapas: `.addV()` `.drop()`, `.addE()`, ou `.property()`.

## <a name="next-steps"></a>Passos seguintes
* [Controle de acesso baseado em função](role-based-access-control.md) no Azure Cosmos DB
* [Saiba como proteger o acesso aos dados](secure-access-to-data.md) no Azure Cosmos DB
