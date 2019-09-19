---
title: Usar tokens de recurso Azure Cosmos DB com o SDK do Gremlin
description: Saiba como criar tokens de recurso e usá-los para acessar o banco de dados de grafo.
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: 6364bd0f762647b5fe9567ed40042a5ad81f97c1
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105027"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Usar tokens de recurso Azure Cosmos DB com o SDK do Gremlin

Este artigo explica como usar [Azure Cosmos DB tokens de recurso](secure-access-to-data.md) para acessar o banco de dados de grafo por meio do SDK do Gremlin.

## <a name="create-a-resource-token"></a>Criar um token de recurso

O SDK do Gremlin do Apache TinkerPop não tem uma API a ser usada para criar tokens de recurso. O termo *token de recurso* é um conceito de Azure Cosmos DB. Para criar tokens de recurso, baixe o [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md). Se seu aplicativo precisar criar tokens de recurso e usá-los para acessar o banco de dados de grafo, ele exigirá dois SDKs separados.

A hierarquia de modelo de objeto acima dos tokens de recurso é ilustrada na seguinte estrutura de tópicos:

- **Conta de Azure Cosmos DB** -a entidade de nível superior que tem um DNS associado a ela (por exemplo `contoso.gremlin.cosmos.azure.com`,).
  - **Banco de dados Azure Cosmos DB**
    - **Usuário**
      - **Permissão**
        - **Token** -uma propriedade de objeto de permissão que denota quais ações são permitidas ou negadas.

Um token de recurso usa o seguinte formato `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`:. Essa cadeia de caracteres é opaca para os clientes e deve ser usada como está, sem modificação ou interpretação.

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

## <a name="use-a-resource-token"></a>Usar um token de recurso
Você pode usar tokens de recurso diretamente como uma propriedade de "senha" ao construir a classe GremlinServer.

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

A mesma abordagem funciona em todos os SDKs do TinkerPop Gremlin.

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

Com uma única conta do Gremlin, você pode emitir um número ilimitado de tokens. No entanto, você pode usar apenas até 100 tokens simultaneamente dentro de 1 hora. Se um aplicativo exceder o limite de token por hora, uma solicitação de autenticação será negada e você receberá a seguinte mensagem de erro: "Limite de token de recurso permitido excedido de 100 que pode ser usado simultaneamente." Ele não funciona para fechar conexões ativas que usam tokens específicos para liberar slots para novos tokens. O mecanismo de banco de dados do Azure Cosmos DB Gremlin mantém o controle de tokens exclusivos durante a hora imediatamente antes da solicitação de autenticação.

## <a name="permission"></a>Permissão

Um erro comum que os aplicativos encontram enquanto estão usando tokens de recurso é "permissões insuficientes fornecidas no cabeçalho de autorização para a solicitação correspondente. Tente novamente com outro cabeçalho de autorização. " Esse erro é retornado quando uma passagem Gremlin tenta gravar uma borda ou um vértice, mas o token de recurso concede somente permissões de *leitura* . Inspecione sua passagem para ver se ela contém qualquer uma das seguintes etapas: *. addV ()* , *. Adde ()* , *. Drop ()* ou *. Property ()* .

## <a name="next-steps"></a>Passos seguintes
* [Controle de acesso baseado em função](role-based-access-control.md) no Azure Cosmos DB
* [Saiba como proteger o acesso aos dados](secure-access-to-data.md) no Azure Cosmos DB
