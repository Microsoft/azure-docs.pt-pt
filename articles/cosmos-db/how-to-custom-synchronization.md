---
title: Como implementar a sincronização personalizada para otimizar para maior disponibilidade e desempenho no Azure Cosmos DB
description: Saiba como implementar a sincronização personalizada para otimizar a disponibilidade e o desempenho mais altos no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 0f630c2139d1d7d391d6c5578e5e7f378e56dcb4
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013776"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implemente a sincronização personalizada para otimizar para maior disponibilidade e desempenho

O Azure Cosmos DB oferece [cinco níveis de consistência bem definidos](consistency-levels.md) para que você escolha entre equilibrar a compensação entre consistência, desempenho e disponibilidade. A consistência forte ajuda a garantir que os dados sejam replicados de forma síncrona e permanentemente persistiram em todas as regiões em que a conta do Azure Cosmos está disponível. Essa configuração fornece o nível mais alto de durabilidade, mas acompanha o custo de desempenho e disponibilidade. Se você quiser que seu aplicativo controle ou relaxe a durabilidade dos dados para atender às necessidades do aplicativo sem comprometer a disponibilidade, poderá usar a *sincronização personalizada* na camada do aplicativo para atingir o nível de durabilidade desejado.

A imagem a seguir descreve visualmente o modelo de sincronização personalizado:

![Sincronização personalizada](./media/how-to-custom-synchronization/custom-synchronization.png)

Nesse cenário, um contêiner Cosmos do Azure é replicado globalmente em várias regiões em vários continentes. O uso de uma consistência forte para todas as regiões neste cenário afeta o desempenho. Para garantir um nível mais alto de durabilidade dos dados sem comprometer a latência de gravação, o aplicativo pode usar dois clientes que compartilham o mesmo [token de sessão](how-to-manage-consistency.md#utilize-session-tokens).

O primeiro cliente pode gravar dados na região local (por exemplo, oeste dos EUA). O segundo cliente (por exemplo, no leste dos EUA) é um cliente de leitura que é usado para garantir a sincronização. Ao fluir o token de sessão da resposta de gravação para a leitura a seguir, a leitura garante a sincronização de gravações no leste dos EUA. Azure Cosmos DB garante que as gravações sejam vistas por pelo menos uma região. Eles têm a garantia de sobreviver a uma interrupção regional se a região de gravação original ficar inativa. Nesse cenário, todas as gravações são sincronizadas para o leste dos EUA, reduzindo a latência de emprego de consistência forte em todas as regiões. Em um cenário de vários mestres, em que as gravações ocorrem em todas as regiões, você pode estender esse modelo para sincronizar com várias regiões em paralelo.

## <a name="configure-the-clients"></a>Configurar os clientes

O exemplo a seguir mostra uma camada de acesso a dados que instancia dois clientes para sincronização personalizada:

### <a name="net-v2-sdk"></a>SDK do .net v2
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

### <a name="net-v3-sdk"></a>SDK do .net v3
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
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementar sincronização personalizada

Depois que os clientes são inicializados, o aplicativo pode executar gravações na região local (oeste dos EUA) e forçar a sincronização das gravações para o leste dos EUA, como a seguir.

### <a name="net-v2-sdk"></a>SDK do .net v2
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

### <a name="net-v3-sdk"></a>SDK do .net v3
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

Você pode estender o modelo para sincronizar em várias regiões em paralelo.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a distribuição global e a consistência em Azure Cosmos DB, leia estes artigos:

* [Escolha o nível de consistência certo no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md)
* [Particionamento e distribuição de dados no Azure Cosmos DB](partition-data.md)
