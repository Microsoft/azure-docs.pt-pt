---
title: Gerir a consistência em Azure Cosmos DB
description: Saiba como configurar e gerir níveis de consistência em Azure Cosmos DB utilizando o portal Azure, .NET SDK, Java SDK e vários outros SDKs
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: mjbrown
ms.openlocfilehash: 28266471fb1e440a45e412ee889e0706cfc2ce49
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82870089"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gerir níveis de consistência no Azure Cosmos DB

Este artigo explica como gerir os níveis de consistência no Azure Cosmos DB. Aprende-se a configurar o nível de consistência predefinido, a anular a consistência predefinida, a gerir manualmente os tokens da sessão e a compreender a métrica Probabilistly Bounded Staleness (PBS).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

O [nível de consistência padrão](consistency-levels.md) é o nível de consistência que os clientes usam por padrão.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para visualizar ou modificar o nível de consistência predefinido, inscreva-se no portal Azure. Encontre a sua conta Azure Cosmos e abra o painel de **consistência Padrão.** Selecione o nível de consistência que deseja como o novo padrão e, em seguida, selecione **Guardar**. O portal Azure também fornece uma visualização de diferentes níveis de consistência com notas musicais. 

![Menu de consistência no portal Azure](./media/how-to-manage-consistency/consistency-settings.png)

# <a name="cli"></a>[CLI](#tab/cli)

Crie uma conta Cosmos com consistência de Session e, em seguida, atualize a consistência padrão.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Crie uma conta Cosmos com consistência de Session e, em seguida, atualize a consistência padrão.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Substituir o nível de consistência predefinido

Os clientes podem substituir o nível de consistência predefinido que está definido pelo serviço. O nível de consistência pode ser definido a cada pedido, que sobrepõe o nível de consistência predefinido estabelecido ao nível da conta.

> [!TIP]
> A consistência só pode ser **descontraída** ao nível do pedido. Para passar de uma consistência mais fraca para uma consistência mais forte, atualize a consistência padrão para a conta Cosmos.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-sdk"></a><a id="override-default-consistency-java"></a>SDK Java

# <a name="java-async-sdk"></a>[Java Async SDK](#tab/javaasync)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="java-sync-sdk"></a>[Java sync SDK](#tab/javasync)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>SDK Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Utilizar tokens de sessões

Um dos níveis de consistência no Azure Cosmos DB é a consistência da *sessão.* Este é o nível padrão aplicado às contas da Cosmos por padrão. Ao trabalhar com a consistência da *Sessão,* o cliente utilizará um token de sessão internamente com cada pedido de leitura/consulta para garantir que o nível de consistência definido seja mantido.

Para gerir os tokens da sessão manualmente, obtenha o token da sessão da resposta e despeça-os por pedido. Se não precisa de gerir os tokens da sessão manualmente, não precisa de usar estas amostras. O SDK acompanha automaticamente as fichas de sessão. Se não definir a sessão de forma manual, por padrão, o SDK utiliza o token de sessão mais recente.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-sdk"></a><a id="utilize-session-tokens-java"></a>SDK Java

# <a name="java-async-sdk"></a>[Java Async SDK](#tab/javaasync)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="java-sync-sdk"></a>[Java sync SDK](#tab/javasync)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>SDK Python

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Monitorizar a métrica de Estagnação Limitada Probabilisticamente (PBS)

Como eventual é a consistência? Para o caso médio, podemos oferecer limites de estagnação no que diz respeito à história e tempo da versão. A métrica [**Probabilistly Bounded Staleness (PBS)**](https://pbs.cs.berkeley.edu/) tenta quantificar a probabilidade de estagnação e mostra-a como uma métrica. Para ver a métrica da PBS, vá à sua conta Azure Cosmos no portal Azure. Abra o painel de **Métricas** e selecione o separador **Consistência.** Veja o gráfico denominado **Probabilidade de leituras fortemente consistentes com base na sua carga de trabalho (ver PBS)**.

![Gráfico PBS no portal Azure](./media/how-to-manage-consistency/pbs-metric.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como gerir conflitos de dados, ou passar para o próximo conceito chave em Azure Cosmos DB. Consulte os seguintes artigos:

* [Níveis de consistência em Azure Cosmos DB](consistency-levels.md)
* [Gerir conflitos entre regiões](how-to-manage-conflicts.md)
* [Criação de partições e distribuição de dados](partition-data.md)
* [Trocas de consistência no design moderno de sistemas de base de dados distribuídos](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Alta disponibilidade](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
