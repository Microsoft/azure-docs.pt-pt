---
title: Saiba como gerir consistência no Azure Cosmos DB
description: Saiba como gerir consistência no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 5b43d822bf29ce07f292403a3a24ad8c13964038
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389126"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gerir níveis de consistência no Azure Cosmos DB

Este artigo explica como gerir níveis de consistência no Azure Cosmos DB. Saiba como configurar o nível de consistência predefinido, substituir a consistência predefinida, manualmente Gerir tokens de sessões e compreender a métrica de Roleta estagnação limitada (PBS).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência predefinido

O [predefinido de nível de consistência](consistency-levels.md) é o nível de consistência que os clientes utilizam por predefinição. Os clientes sempre podem substituí-la.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Este exemplo cria uma nova conta do Cosmos do Azure com várias regiões de escrita ativados, nas regiões E.U.A. leste e E.U.A. oeste. O nível de consistência predefinido está definido como *sessão* consistência.

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

Para ver ou modificar o nível predefinido de consistência, inicie sessão no portal do Azure. Localize a conta do Cosmos do Azure e abra o **consistência predefinida** painel. Selecione o nível de consistência que pretende como a nova predefinição e, em seguida, selecione **guardar**. O portal do Azure também fornece uma visualização dos níveis de consistência diferentes com notas de música. 

![Menu de consistência no portal do Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Substituir o nível de consistência predefinido

Os clientes podem substituir o nível de consistência predefinido que está definido pelo serviço. Nível de consistência pode ser definido num por solicitação, que substitui o nível de consistência predefinido definido ao nível da conta.

### <a id="override-default-consistency-dotnet"></a>SDK do .NET

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
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
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>SDK do Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Utilizar tokens de sessões

Um dos níveis de consistência no Azure Cosmos DB é *sessão* consistência. Este é o nível de predefinição aplicado a contas do Cosmos por predefinição. Ao trabalhar com *sessão* consistência, o cliente irá utilizar um token de sessão internamente com cada solicitação de consulta/leitura para garantir que o nível de consistência do conjunto é mantido.

Gerir os tokens de sessão manualmente, obter a sessão do token da resposta e defini-los por pedido. Se não precisar de gerir manualmente os tokens de sessão, não precisa de utilizar estes exemplos. O SDK controla de tokens de sessão automaticamente. Se não definir o token de sessão manualmente, por predefinição, o SDK utiliza o token de sessão mais recente.

### <a id="utilize-session-tokens-dotnet"></a>SDK do .NET

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
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

Como eventual é a consistência eventual? Para o caso médio, pode oferecemos dos limites de envelhecimento relativamente ao histórico de versões e a hora. O [ **Roleta estagnação limitada (PBS)** ](https://pbs.cs.berkeley.edu/) métrica tenta quantificar a probabilidade de envelhecimento e mostra-a como uma métrica. Para ver a métrica de PBS, aceda à sua conta do Cosmos do Azure no portal do Azure. Abra o **métricas** painel e selecione o **consistência** separador. Observe o gráfico com o nome **probabilidade de leituras fortemente consistentes com base na carga de trabalho (Consulte PBS)** .

![Gráfico de PBS no portal do Azure](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como gerir os conflitos de dados ou avançar para o próximo conceito-chave no Azure Cosmos DB. Consulte os seguintes artigos:

* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Gerir conflitos entre regiões](how-to-manage-conflicts.md)
* [Criação de partições e distribuição de dados](partition-data.md)
* [Vantagens e desvantagens de consistência no design de sistemas de banco de dados distribuída Moderno](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Elevada disponibilidade](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
