---
title: Como configurar múltiplos principais no Azure Cosmos DB
description: Saiba como configurar múltiplos principais nas suas aplicações no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054597"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Como configurar vários mestres em seus aplicativos que utilizam o Azure Cosmos DB

Para utilizar a funcionalidade de vários mestre na sua aplicação, terá de ativar as gravações de várias regiões e configurar o recurso multi homing no Azure Cosmos DB. Multi-homing é configurada ao definir a região onde a aplicação é implementada.

## <a id="netv2"></a>.NET SDK v2

Para ativar múltiplos principais no seu conjunto de aplicativos `UseMultipleWriteLocations` true e configurar `SetCurrentLocation` para a região em que o aplicativo está sendo implantado e é replicado do Azure Cosmos DB.

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

Para ativar a configurar com vários mestres em seus aplicativos `UseCurrentRegion` para a região em que o aplicativo está sendo implantado e Cosmos DB é replicado.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>SDK do Java Async

Para ativar múltiplos principais no seu conjunto de aplicativos `policy.setUsingMultipleWriteLocations(true)` e configure `policy.setPreferredLocations` para a região em que o aplicativo está sendo implantado e Cosmos DB é replicado.

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

## <a id="javascript"></a>SDK do node. js, JavaScript, TypeScript

Para ativar múltiplos principais no seu conjunto de aplicativos `connectionPolicy.UseMultipleWriteLocations` true e configurar `connectionPolicy.PreferredLocations` para a região em que o aplicativo está sendo implantado e Cosmos DB é replicado.

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

Para ativar múltiplos principais no seu conjunto de aplicativos `connection_policy.UseMultipleWriteLocations` true e configurar `connection_policy.PreferredLocations` para a região em que o aplicativo está sendo implantado e Cosmos DB é replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passos Seguintes

Em seguida pode ler os artigos seguintes:

* [Utilizar tokens de sessão para o gerenciamento de consistência no Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflito e diretivas de resolução no Azure Cosmos DB](conflict-resolution-policies.md)
* [Elevada disponibilidade no Azure Cosmos DB](high-availability.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Escolher o nível certo de consistência no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromissos de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Globalmente dimensionar o débito aprovisionado](scaling-throughput.md)
* [Distribuição global - sob definições avançadas](global-dist-under-the-hood.md)
