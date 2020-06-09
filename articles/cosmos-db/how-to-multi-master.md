---
title: Como configurar multi-mestre em Azure Cosmos DB
description: Aprenda a configurar multi-mestre para as suas aplicações utilizando diferentes SDKs em Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: tracking-python
ms.openlocfilehash: 62dedd4cf91143ee7b31b92880135ac6c7953ef9
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561235"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configure multi-mestre nas suas aplicações que usam Azure Cosmos DB

Uma vez criada uma conta com múltiplas regiões de escrita ativadas, deve escoar duas alterações na sua aplicação à ConnectionPolicy para o DocumentClient para ativar as capacidades multi-master e multi-homing em Azure Cosmos DB. Dentro da ConnectionPolicy, deite UseMultipleWriteLocations para verdadeiro e passe o nome da região onde a aplicação é implantada para SetCurrentLocation. Isto irá povoar a propriedade PreferredLocations com base na geo-proximidade a partir da localização passada. Se uma nova região for posteriormente adicionada à conta, a aplicação não terá de ser atualizada ou redistribuída, irá automaticamente detetar a região mais próxima e irá alocar-se automaticamente a ela caso ocorra um evento regional.

> [!Note]
> As contas cosmos inicialmente configuradas com uma única região de escrita podem ser configuradas para várias regiões de escrita (ou seja, multi-master) com tempo de insuporte zero. Para saber mais ver, [configuure regiões de escrita múltipla](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Para ativar o multi-mestre na sua aplicação, desatado `UseMultipleWriteLocations` para `true` . Além disso, definido `SetCurrentLocation` para a região em que a aplicação está sendo implementada e onde Azure Cosmos DB é replicado:

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

Para ativar o multi-mestre na sua aplicação, desloque-se `ApplicationRegion` à região em que a aplicação está a ser implementada e onde a Cosmos DB é replicada:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcionalmente, pode utilizar o `CosmosClientBuilder` e para obter o mesmo `WithApplicationRegion` resultado:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>SDK do Java Async

Para ativar o multi-mestre na sua aplicação, desloque-se `policy.setUsingMultipleWriteLocations(true)` e desloque-se `policy.setPreferredLocations` para a região em que a aplicação está a ser implementada e onde a Cosmos DB é replicada:

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

Para ativar o multi-mestre na sua aplicação, desatado `connectionPolicy.UseMultipleWriteLocations` para `true` . Além disso, definido `connectionPolicy.PreferredLocations` para a região em que a aplicação está a ser implementada e onde a Cosmos DB é replicada:

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

## <a name="python-sdk"></a><a id="python"></a>SDK Python

Para ativar o multi-mestre na sua aplicação, desatado `connection_policy.UseMultipleWriteLocations` para `true` . Além disso, definido `connection_policy.PreferredLocations` para a região em que a aplicação está sendo implementada e onde cosmos DB é replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passos seguintes

Leia os seguintes artigos:

* [Use fichas de sessão para gerir a consistência em Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflitos e políticas de resolução em Azure Cosmos DB](conflict-resolution-policies.md)
* [Alta disponibilidade em Azure Cosmos DB](high-availability.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Escolha o nível de consistência certo no Azure Cosmos DB](consistency-levels-choosing.md)
* [Consistência, disponibilidade e desempenho em Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Disponibilidade e compensações de desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Dimensionamento global de débito aprovisionado](scaling-throughput.md)
* [Distribuição global: Sob o capuz](global-dist-under-the-hood.md)
