---
title: Como configurar múltiplos principais no Azure Cosmos DB
description: Saiba como configurar múltiplos principais nas suas aplicações no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: e33aa10673f4c4b061f8961959f20b7b3416ec84
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204789"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configurar com vários mestres em seus aplicativos que utilizam o Azure Cosmos DB

Para utilizar a funcionalidade de vários mestre na sua aplicação, tem de ativar o gravações de várias regiões e configurar a multi homing capacidade no Azure Cosmos DB. Para configurar o multi-homing, passe o nome da região em que a aplicação for implementada para a propriedade SetCurrentLocation. Fazendo isso automaticamente preenche a propriedade PreferredLocations com base na proximidade geo localização passado. Se uma nova região mais tarde é adicionada à conta, o aplicativo não tem de ser atualizado ou reimplementada, irá detetar automaticamente a região mais próxima e será automaticamente-home para ele deve ocorrer um evento regional.

## <a id="netv2"></a>.NET SDK v2

Para ativar com vários mestres em seu aplicativo, defina `UseMultipleWriteLocations` para `true`. Além disso, defina `SetCurrentLocation` para a região em que a aplicação está a ser implementada e em que é replicado do Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>SDK de .NET v3 (pré-visualização)

Para ativar com vários mestres em seu aplicativo, defina `UseCurrentRegion` para a região em que a aplicação está a ser implementada e em que é replicado do Cosmos DB:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>SDK do Java Async

Para ativar com vários mestres em seu aplicativo, defina `policy.setUsingMultipleWriteLocations(true)` e defina `policy.setPreferredLocations` para a região em que a aplicação está a ser implementada e em que é replicado do Cosmos DB:

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

## <a id="javascript"></a>NODE. js, JavaScript e TypeScript SDKs

Para ativar com vários mestres em seu aplicativo, defina `connectionPolicy.UseMultipleWriteLocations` para `true`. Além disso, defina `connectionPolicy.PreferredLocations` para a região em que a aplicação está a ser implementada e em que é replicado do Cosmos DB:

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

Para ativar com vários mestres em seu aplicativo, defina `connection_policy.UseMultipleWriteLocations` para `true`. Além disso, defina `connection_policy.PreferredLocations` para a região em que a aplicação está a ser implementada e em que o Cosmos DB é replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passos Seguintes

Leia os artigos seguintes:

* [Utilizar tokens de sessão para gerir a consistência no Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflito e diretivas de resolução no Azure Cosmos DB](conflict-resolution-policies.md)
* [Elevada disponibilidade no Azure Cosmos DB](high-availability.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Escolha o nível certo de consistência no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromissos de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Globalmente dimensionar o débito aprovisionado](scaling-throughput.md)
* [Distribuição global: Under the hood](global-dist-under-the-hood.md) (Como funciona o Azure RMS? Nos bastidores)
