---
title: Como configurar multi-mestre em Azure Cosmos DB
description: Aprenda a configurar o multi-master para as suas aplicações utilizando diferentes SDKs em Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873646"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configure multi-master nas suas aplicações que usam O Azure Cosmos DB

Uma vez criada uma conta com várias regiões de escrita ativadas, deve efazer duas alterações na sua aplicação à Política de Ligação para o Cliente documental para permitir as capacidades multi-master e multi-homing em Azure Cosmos DB. Dentro da Política de Ligação, desloque as Localizações de Utilização Múltiplas Escritas para verdadeira e passe o nome da região onde a aplicação é implantada para SetCurrentLocation. Isto irá povoar a propriedade PreferredLocations com base na geo-proximidade da localização passada. Se uma nova região for adicionada posteriormente à conta, o pedido não terá de ser atualizado ou redistribuído, detetará automaticamente a região mais próxima e irá realizar-se automaticamente caso ocorra um evento regional.

> [!Note]
> As contas cosmos inicialmente configuradas com uma região de escrita única podem ser configuradas para várias regiões de escrita (ou seja, multi-master) com zero tempo de descida. Para saber mais, [Configure regiões de múltiplas escritas](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Para ativar o multi-master `UseMultipleWriteLocations` na `true`sua aplicação, definido para . Além `SetCurrentLocation` disso, definida para a região em que a aplicação está a ser implantada e onde o Azure Cosmos DB é replicado:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

Para permitir o multi-master `ApplicationRegion` na sua aplicação, definido para a região em que a aplicação está a ser implementada e onde cosmos DB é replicado:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcionalmente, pode usar `CosmosClientBuilder` `WithApplicationRegion` o e obter o mesmo resultado:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>SDK do Java Async

Para permitir o multi-master `policy.setUsingMultipleWriteLocations(true)` na `policy.setPreferredLocations` sua aplicação, definir e definir para a região em que a aplicação está sendo implementada e onde cosmos DB é replicado:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript e TypeScript SDKs

Para ativar o multi-master `connectionPolicy.UseMultipleWriteLocations` na `true`sua aplicação, definido para . Além `connectionPolicy.PreferredLocations` disso, definida para a região em que a aplicação está a ser implantada e onde a Cosmos DB é replicada:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Para ativar o multi-master `connection_policy.UseMultipleWriteLocations` na `true`sua aplicação, definido para . Além `connection_policy.PreferredLocations` disso, definida para a região em que a aplicação está a ser implantada e onde o Cosmos DB é replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passos seguintes

Leia os seguintes artigos:

* [Use tokens de sessão para gerir a consistência em Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflitos e políticas de resolução em Azure Cosmos DB](conflict-resolution-policies.md)
* [Alta disponibilidade em Azure Cosmos DB](high-availability.md)
* [Níveis de consistência em Azure Cosmos DB](consistency-levels.md)
* [Escolha o nível de consistência certo em Azure Cosmos DB](consistency-levels-choosing.md)
* [Consistência, disponibilidade e trocas de desempenho em Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Trocas de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Dimensionamento global de débito aprovisionado](scaling-throughput.md)
* [Distribuição global: Sob o capot](global-dist-under-the-hood.md)
