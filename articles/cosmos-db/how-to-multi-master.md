---
title: Como configurar vários mestres no Azure Cosmos DB
description: Saiba como configurar vários mestres em seus aplicativos no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: e86cacbd76a70c8b114d65a77ff013d32327a2d0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093108"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configurar vários mestres em seus aplicativos que usam Azure Cosmos DB

Depois que uma conta tiver sido criada com várias regiões de gravação habilitadas, você deverá fazer duas alterações em seu aplicativo para o ConnectionPolicy para o DocumentClient para habilitar os recursos de vários mestres e hospedagem múltipla no Azure Cosmos DB. Dentro do ConnectionPolicy, defina UseMultipleWriteLocations como true e passe o nome da região onde o aplicativo é implantado no SetCurrentLocation. Isso preencherá a propriedade PreferredLocations com base na proximidade geográfica do local passado. Se uma nova região for adicionada posteriormente à conta, o aplicativo não precisará ser atualizado ou reimplantado, ele detectará automaticamente a região mais próxima e fará o início automático para ela se ocorrer um evento regional.

> [!Note]
> As contas do cosmos inicialmente configuradas com uma única região de gravação podem ser configuradas para várias regiões de gravação (ou seja, vários mestres) com tempo de inatividade zero. Para saber mais, confira [configurar regiões de várias gravações](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a id="netv2"></a>SDK do .NET v2

Para habilitar vários mestres em seu aplicativo, defina `UseMultipleWriteLocations` como. `true` Além disso, `SetCurrentLocation` defina para a região em que o aplicativo está sendo implantado e onde Azure Cosmos DB é replicado:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>SDK do .NET v3

Para habilitar o multimestre em seu aplicativo, defina `ApplicationRegion` para a região em que o aplicativo está sendo implantado e onde Cosmos DB é replicado:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcionalmente, você pode usar o `CosmosClientBuilder` e `WithApplicationRegion` o para obter o mesmo resultado:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a id="java"></a>SDK do Java Async

Para habilitar o multimestre em seu aplicativo, defina `policy.setUsingMultipleWriteLocations(true)` e defina `policy.setPreferredLocations` como a região em que o aplicativo está sendo implantado e onde Cosmos DB é replicado:

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

## <a id="javascript"></a>SDK do node. js, JavaScript e TypeScript

Para habilitar vários mestres em seu aplicativo, defina `connectionPolicy.UseMultipleWriteLocations` como. `true` Além disso, `connectionPolicy.PreferredLocations` defina para a região em que o aplicativo está sendo implantado e onde Cosmos DB é replicado:

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

## <a id="python"></a>SDK do Python

Para habilitar vários mestres em seu aplicativo, defina `connection_policy.UseMultipleWriteLocations` como. `true` Além disso, `connection_policy.PreferredLocations` defina para a região em que o aplicativo está sendo implantado e onde Cosmos DB é replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passos Seguintes

Leia os seguintes artigos:

* [Usar tokens de sessão para gerenciar a consistência em Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflito e políticas de resolução no Azure Cosmos DB](conflict-resolution-policies.md)
* [Alta disponibilidade no Azure Cosmos DB](high-availability.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Escolha o nível de consistência certo no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Dimensionamento global da taxa de transferência provisionada](scaling-throughput.md)
* [Distribuição global: Under the hood](global-dist-under-the-hood.md) (Como funciona o Azure RMS? Nos bastidores)
