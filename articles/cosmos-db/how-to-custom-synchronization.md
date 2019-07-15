---
title: Como implementar personalizado de sincronização para otimizar para elevada disponibilidade e desempenho no Azure Cosmos DB
description: Saiba como implementar a sincronização personalizada para otimizar para elevada disponibilidade e desempenho no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: de66149a2ea3e01e62aa8e33ea5a99121a21524f
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986086"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementar a sincronização personalizada para otimizar para elevada disponibilidade e desempenho

Azure Cosmos DB oferece [cinco níveis de consistência bem definidos](consistency-levels.md) à sua escolha de equilibrar o compromisso entre disponibilidade, desempenho e consistência. Consistência forte ajuda a garantir que os dados são replicados de forma síncrona e mantidos de maneira duradoura em cada região em que a conta do Cosmos do Azure está disponível. Esta configuração fornece o nível mais elevado de durabilidade, mas impacta o desempenho e disponibilidade. Se pretender que o seu aplicativo para controlar ou relaxe durabilidade dos dados de acordo com o aplicativo precisa sem comprometer a disponibilidade, pode utilizar *personalizado de sincronização* na camada da aplicação para alcançar o nível de durabilidade que.

A imagem seguinte mostra visualmente o modelo personalizado de sincronização:

![Personalizado de sincronização](./media/how-to-custom-synchronization/custom-synchronization.png)

Neste cenário, um contentor do Cosmos do Azure é replicado globalmente em várias regiões em vários continentes. Usando a consistência forte para todas as regiões neste cenário afeta o desempenho. Para garantir um nível mais elevado de durabilidade dos dados sem comprometer a latência de escrita, o aplicativo pode usar dois clientes que partilham o mesmo [token de sessão](how-to-manage-consistency.md#utilize-session-tokens).

O primeiro cliente possa escrever dados para a região local (por exemplo, e.u.a. Centro-Oeste). O segundo cliente (por exemplo, nos EUA Leste) é um cliente de leitura que é utilizado para garantir a sincronização. Ao enviar o token de sessão da resposta de escrita para a leitura seguinte, a leitura garante a sincronização de escritas, EUA Leste. O Azure Cosmos DB garante que as escritas são visualizadas pelo menos uma região. É garantido que sobrevivem a uma falha regional, se a região de escrita original ficar inativo. Neste cenário, a cada gravação está sincronizada com E.U. a leste, reduzindo a latência de emprego de consistência forte em todas as regiões. Num cenário de vários mestre, onde ocorrem escritas em cada região, é possível estender esse modelo para sincronizar em várias regiões em paralelo.

## <a name="configure-the-clients"></a>Configurar os clientes

O exemplo seguinte mostra uma camada de acesso de dados que cria uma instância de dois clientes para personalizado de sincronização:

### <a name="net-v2-sdk"></a>.NET V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.NET V3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementar sincronização personalizada

Depois dos clientes são inicializados, o aplicativo pode realizar escritas para a região local (E.u.a. oeste) e a sincronização de força as gravações em E.u.a. leste da seguinte forma.

### <a name="net-v2-sdk"></a>.NET V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.NET V3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

Pode estender o modelo de sincronização em várias regiões em paralelo.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a distribuição global e consistência no Azure Cosmos DB, leia os seguintes artigos:

* [Escolha o nível certo de consistência no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromissos de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Gerir a consistência no Azure Cosmos DB](how-to-manage-consistency.md)
* [Distribuição de criação de partições e os dados no Azure Cosmos DB](partition-data.md)
