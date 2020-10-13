---
title: Gerir a consistência em Azure Cosmos DB
description: Aprenda a configurar e gerir níveis de consistência em Azure Cosmos DB usando o portal Azure, .NET SDK, Java SDK e vários outros SDKs
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7a07a42e208cb0f6fe91f6a65777fd28155106a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330592"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gerir níveis de consistência no Azure Cosmos DB

Este artigo explica como gerir os níveis de consistência no Azure Cosmos DB. Aprende-se a configurar o nível de consistência padrão, a sobrepor-se à consistência padrão, a gerir manualmente fichas de sessão e a compreender a métrica probabilisticamente limitada (PBS).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

O [nível de consistência padrão](consistency-levels.md) é o nível de consistência que os clientes usam por padrão.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para visualizar ou modificar o nível de consistência predefinido, inscreva-se no portal Azure. Encontre a sua conta Azure Cosmos e abra o **painel de consistência padrão.** Selecione o nível de consistência que deseja como novo padrão e, em seguida, **selecione Guardar**. O portal Azure também fornece uma visualização de diferentes níveis de consistência com notas musicais. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Menu de consistência no portal Azure":::

# <a name="cli"></a>[CLI](#tab/cli)

Crie uma conta Cosmos com consistência de Sessão e, em seguida, atualize a consistência padrão.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Crie uma conta Cosmos com consistência de Sessão e, em seguida, atualize a consistência padrão.

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

Os clientes podem substituir o nível de consistência predefinido que está definido pelo serviço. O nível de consistência pode ser definido a cada pedido, o que substitui o nível de consistência predefinido definido ao nível da conta.

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

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Sincronização](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Java V2 SDKs

# <a name="async"></a>[Async](#tab/api-async)

Async Java V2 SDK (Maven com.microsoft.azure::azure-cosmosdb)

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

# <a name="sync"></a>[Sincronização](#tab/api-sync)

Sync Java V2 SDK (Maven com.microsoft.azure::azure-documentdb)

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

Um dos níveis de consistência no Azure Cosmos DB é a consistência *da sessão.* Este é o nível padrão aplicado às contas cosmos por padrão. Ao trabalhar com a consistência *da Sessão,* o cliente utilizará um token de sessão internamente com cada pedido de leitura/consulta para garantir que o nível de consistência definido seja mantido.

Para gerir os tokens de sessão manualmente, obtenha o sinal da sessão da resposta e desacorda-os por pedido. Se não precisar de gerir os tokens de sessão manualmente, não precisa de usar estas amostras. O SDK acompanha automaticamente os tokens de sessão. Se não definir o token da sessão manualmente, por padrão, o SDK utiliza o token de sessão mais recente.

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

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Sincronização](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Java V2 SDKs

# <a name="async"></a>[Async](#tab/api-async)

Async Java V2 SDK (Maven com.microsoft.azure::azure-cosmosdb)

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

# <a name="sync"></a>[Sincronização](#tab/api-sync)

Sync Java V2 SDK (Maven com.microsoft.azure::azure-documentdb)

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

Quão eventual é a consistência eventual? Para o caso médio, podemos oferecer limites de estagnação no que diz respeito à história e tempo da versão. A métrica [**Probabilisticamente Limitada (PBS)**](https://pbs.cs.berkeley.edu/) tenta quantificar a probabilidade de estagnação e mostra-a como uma métrica. Para ver a métrica pbs, vá à sua conta Azure Cosmos no portal Azure. Abra o painel **métrica** e selecione o **separador Consistência.** Veja o gráfico denominado **Probabilidade de leituras fortemente consistentes com base na sua carga de trabalho (ver PBS)**.

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Menu de consistência no portal Azure":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como gerir conflitos de dados, ou passar para o próximo conceito chave em Azure Cosmos DB. Consulte os seguintes artigos:

* [Níveis de consistência em Azure Cosmos DB](consistency-levels.md)
* [Gerir conflitos entre regiões](how-to-manage-conflicts.md)
* [Criação de partições e distribuição de dados](partition-data.md)
* [Tradeoffs de consistência no design moderno de sistemas de base de dados distribuídos](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Elevada disponibilidade](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
