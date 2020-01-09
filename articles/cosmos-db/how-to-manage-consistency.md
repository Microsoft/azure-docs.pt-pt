---
title: Gerenciar a consistência no Azure Cosmos DB
description: Saiba como configurar e gerenciar níveis de consistência no Azure Cosmos DB usando portal do Azure, SDK do .net, SDK do Java e vários outros SDKs
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 68be15e1ffd9093ab67fc046edaad991d633ca7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445373"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gerir níveis de consistência no Azure Cosmos DB

Este artigo explica como gerenciar níveis de consistência no Azure Cosmos DB. Você aprende como configurar o nível de consistência padrão, substituir a consistência padrão, gerenciar manualmente os tokens de sessão e entender a métrica do PBS (Probabilistic Bounded desatualização).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

O [nível de consistência padrão](consistency-levels.md) é o nível de consistência que os clientes usam por padrão. Os clientes sempre podem substituí-lo.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

Este exemplo cria uma nova conta do Azure cosmos com várias regiões de gravação habilitadas, nas regiões leste dos EUA e oeste dos EUA. O nível de consistência padrão é definido como consistência da *sessão* .

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Portal do Azure

Para exibir ou modificar o nível de consistência padrão, entre no portal do Azure. Localize sua conta do Azure Cosmos e abra o painel de **consistência padrão** . Selecione o nível de consistência desejado como o novo padrão e, em seguida, selecione **salvar**. O portal do Azure também fornece uma visualização de diferentes níveis de consistência com notas musicais. 

![Menu de consistência no portal do Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Substituir o nível de consistência predefinido

Os clientes podem substituir o nível de consistência predefinido que está definido pelo serviço. O nível de consistência pode ser definido em um por solicitação, que substitui o nível de consistência padrão definido no nível da conta.

### <a id="override-default-consistency-dotnet"></a>SDK DO .NET V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>SDK DO .NET V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
```

### <a id="override-default-consistency-java-async"></a>SDK do Java Async

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

### <a id="override-default-consistency-java-sync"></a>SDK do Java Sync

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>SDK do Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Utilizar tokens de sessões

Um dos níveis de consistência no Azure Cosmos DB é a consistência da *sessão* . Esse é o nível padrão aplicado às contas do cosmos por padrão. Ao trabalhar com consistência de *sessão* , o cliente usará um token de sessão internamente com cada solicitação de leitura/consulta para garantir que o nível de consistência definido seja mantido.

Para gerenciar os tokens de sessão manualmente, obtenha o token de sessão da resposta e defina-os por solicitação. Se você não precisar gerenciar os tokens de sessão manualmente, não precisará usar esses exemplos. O SDK mantém o controle de tokens de sessão automaticamente. Se você não definir o token de sessão manualmente, por padrão, o SDK usará o token de sessão mais recente.

### <a id="utilize-session-tokens-dotnet"></a>SDK DO .NET V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>SDK DO .NET V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```

### <a id="utilize-session-tokens-java-async"></a>SDK do Java Async

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

### <a id="utilize-session-tokens-java-sync"></a>SDK do Java Sync

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>SDK do Python

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

Como a eventualidade é a consistência eventual? Para o caso médio, podemos oferecer limites de desatualização em relação ao histórico e à hora da versão. A métrica do PBS (desatualização [**vinculada) do Probabilistic**](https://pbs.cs.berkeley.edu/) tenta quantificar a probabilidade de desatualização e a mostra como uma métrica. Para exibir a métrica do PBS, vá para sua conta do cosmos do Azure no portal do Azure. Abra o painel **métricas** e selecione a guia **consistência** . Examine o grafo chamado **probabilidade de leituras altamente consistentes com base em sua carga de trabalho (consulte PBS)** .

![Grafo do PBS no portal do Azure](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como gerenciar conflitos de dados ou vá para o próximo conceito de chave em Azure Cosmos DB. Consulte os seguintes artigos:

* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Gerenciar conflitos entre regiões](how-to-manage-conflicts.md)
* [Criação de partições e distribuição de dados](partition-data.md)
* [Compensações de consistência no design de sistemas de bancos de dados distribuídos modernos](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Elevada disponibilidade](high-availability.md)
* [SLA de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
