---
title: Pontos de extremidade regionais para o banco de dados do Azure Cosmos DB Graph
description: Saiba como se conectar ao ponto de extremidade de banco de dados do grafo mais próximo para seu aplicativo
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/09/2019
ms.openlocfilehash: 6bce69889910296f5ff28090e62cfe27c0640d95
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755039"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Pontos de extremidade regionais para Azure Cosmos DB conta do grafo
Azure Cosmos DB banco de dados de grafo é [distribuído globalmente](distribute-data-globally.md) para que os aplicativos possam usar vários pontos de extremidade de leitura. Os aplicativos que precisam de acesso de gravação em vários locais devem habilitar [a funcionalidade de vários mestres](how-to-multi-master.md) .

Motivos para escolher mais de uma região:
1. **Escalabilidade de leitura horizontal** -como a carga do aplicativo aumenta, pode ser prudente rotear o tráfego de leitura para diferentes regiões do Azure.
2. **Latência mais baixa** -você pode reduzir a sobrecarga de latência de rede de cada passagem roteando o tráfego de leitura e gravação para a região do Azure mais próxima.

O requisito de **residência de dados** é obtido definindo Azure Resource Manager política na conta Cosmos DB. O cliente pode limitar regiões nas quais Cosmos DB replica dados.

## <a name="traffic-routing"></a>Roteamento de tráfego

Cosmos DB mecanismo de banco de dados de grafo está em execução em várias regiões, cada um deles contém vários clusters. Cada cluster tem centenas de computadores. Cosmos DB conta do grafo DNS CNAME *AccountName.Gremlin.Cosmos.Azure.com* resolve para DNS um registro de um cluster. Um único endereço IP de um balanceador de carga oculta a topologia de cluster interna.

Um registro DNS CNAME regional é criado para cada região da conta do grafo Cosmos DB. O formato do ponto de extremidade regional é *AccountName-Region.Gremlin.Cosmos.Azure.com*. O segmento de região do ponto de extremidade regional é obtido removendo todos os espaços do nome da [região do Azure](https://azure.microsoft.com/global-infrastructure/regions) . Por exemplo, `"East US 2"` região para `"contoso"` conta de banco de dados global teria um DNS CNAME *contoso-eastus2.Gremlin.Cosmos.Azure.com*

TinkerPop Gremlin Client foi projetado para funcionar com um único servidor. O aplicativo pode usar CNAME de DNS gravável global para tráfego de leitura e gravação. Os aplicativos com reconhecimento de região devem usar o ponto de extremidade regional para tráfego de leitura. Use o ponto de extremidade regional para gravar o tráfego somente se a região específica estiver configurada para aceitar gravações. 

> [!NOTE]
> Cosmos DB mecanismo de grafo pode aceitar a operação de gravação na região de leitura por meio do tráfego de proxy para a região de gravação. Não é recomendável enviar gravações em região somente leitura, pois ela aumenta a latência de passagem e está sujeita às restrições no futuro.

A CNAME da conta do banco de dados global sempre aponta para uma região de gravação válida. Durante o failover do lado do servidor da região de gravação, Cosmos DB atualiza a conta do banco de dados global CNAME para apontar para uma nova região. Se o aplicativo não puder lidar com o roteamento de tráfego após o failover, ele deverá usar o CNAME de DNS da conta do banco de dados global.

> [!NOTE]
> Cosmos DB não roteia o tráfego com base na proximidade geográfica do chamador. Cabe a cada aplicativo selecionar a região correta de acordo com as necessidades exclusivas do aplicativo.

## <a name="portal-endpoint-discovery"></a>Descoberta de ponto de extremidade do portal

A maneira mais fácil de obter a lista de regiões para Azure Cosmos DB conta do Graph é a folha visão geral na portal do Azure. Ele funcionará para aplicativos que não alteram regiões com frequência ou que têm uma maneira de atualizar a lista por meio da configuração do aplicativo.

![Recuperar regiões da conta do grafo de Cosmos DB do portal](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

O exemplo a seguir demonstra os princípios gerais de acesso ao ponto de extremidade Gremlin regional. O aplicativo deve considerar o número de regiões para enviar o tráfego e o número de clientes Gremlin correspondentes para instanciar.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>Descoberta de ponto de extremidade SDK

O aplicativo pode usar [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) para descobrir locais de leitura e gravação para a conta do Graph. Esses locais podem mudar a qualquer momento por meio da reconfiguração manual no lado do servidor ou failover automático.

O SDK do TinkerPop Gremlin não tem uma API para descobrir Cosmos DB regiões de conta do banco de dados do grafo. Aplicativos que precisam de descoberta de ponto de extremidade de tempo de execução precisam hospedar 2 SDKs separados no espaço de processo.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Passos seguintes
* [Como gerenciar o controle de contas de banco de dados](how-to-manage-database-account.md) no Azure Cosmos DB
* [Alta disponibilidade](high-availability.md) no Azure Cosmos DB
* [Distribuição global com Azure Cosmos DB-nos bastidores](global-dist-under-the-hood.md)
* [Exemplos de CLI do Azure](cli-samples.md) para Azure Cosmos DB
