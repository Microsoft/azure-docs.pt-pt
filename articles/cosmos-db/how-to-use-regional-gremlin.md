---
title: Pontos finais regionais para base de dados do Gráfico DB da Azure Cosmos
description: Saiba como ligar-se ao ponto final da base de dados do Gráfico mais próximo para a sua aplicação
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 7aa1e0aa6bbbee9d40eb0d48318a8e2908a75f9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78897868"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Pontos finais regionais para a conta Do Gráfico DB da Azure Cosmos
A base de dados Azure Cosmos DB Graph está [distribuída globalmente](distribute-data-globally.md) para que as aplicações possam usar vários pontos finais de leitura. As aplicações que necessitem de acesso por escrito em vários locais devem permitir a capacidade [multi-master.](how-to-multi-master.md)

Razões para escolher mais do que uma região:
1. **Escalabilidade de leitura horizontal** - à medida que a carga de aplicação aumenta, pode ser prudente encaminhar o tráfego para diferentes regiões de Azure.
2. **Latência mais baixa** - você pode reduzir a latência da rede de cada transversal através do encaminhamento de leitura e escrita de tráfego para a região de Azure mais próxima.

**O** requisito de residência de dados é alcançado através da definição da política do Gestor de Recursos Azure na conta Cosmos DB. O cliente pode limitar regiões nas quais a Cosmos DB replica dados.

## <a name="traffic-routing"></a>Encaminhamento de tráfego

O motor de base de dados Cosmos DB Graph está a funcionar em várias regiões, cada uma das quais contém vários clusters. Cada aglomerado tem centenas de máquinas. Cosmos DB Graph conta DNS CNAME *accountname.gremlin.cosmos.azure.com* resolve ao DNS Um registo de um cluster. Um único endereço IP de um equilibrador de carga esconde topologia interna do cluster.

Um registo regional do DNS CNAME é criado para todas as regiões da conta Cosmos DB Graph. O formato do ponto final regional é *accountname-region.gremlin.cosmos.azure.com.* O segmento regional do ponto final regional é obtido removendo todos os espaços do nome da região de [Azure.](https://azure.microsoft.com/global-infrastructure/regions) Por exemplo, `"East US 2"` `"contoso"` a região da conta de base de dados global teria um DNS CNAME *contoso-eastus2.gremlin.cosmos.azure.com*

O cliente TinkerPop Gremlin foi concebido para trabalhar com um único servidor. A aplicação pode usar DNS CNAME global para ler e escrever tráfego. As aplicações conscientes da região devem utilizar pontos finais regionais para a leitura do tráfego. Utilize o ponto final regional para escrever o tráfego apenas se uma região específica estiver configurada para aceitar escritos. 

> [!NOTE]
> O motor Cosmos DB Graph pode aceitar a operação de escrita na região de leitura, procurando tráfego para escrever região. Não é aconselhável enviar escritos para a região apenas de leitura, uma vez que aumenta a latência transversal e está sujeito a restrições no futuro.

A conta de base de dados global CNAME sempre aponta para uma região de escrita válida. Durante o failover do lado do servidor da região de escrita, cosmos DB atualiza a conta de base de dados global CNAME para apontar para uma nova região. Se a aplicação não conseguir lidar com o reencaminhamento de tráfego após a falha, deve utilizar a conta de base de dados global DNS CNAME.

> [!NOTE]
> Cosmos DB não encaminha o tráfego com base na proximidade geográfica do chamador. Cabe a cada aplicação selecionar a região certa de acordo com as necessidades únicas de aplicação.

## <a name="portal-endpoint-discovery"></a>Descoberta de ponto final do portal

A maneira mais fácil de obter a lista de regiões para a conta Azure Cosmos DB Graph é a lâmina de visão geral no portal Azure. Funcionará para aplicações que não alterem muitas vezes as regiões, ou que tenham uma forma de atualizar a lista através da configuração da aplicação.

![Recuperar regiões da conta Cosmos DB Graph do portal](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Exemplo abaixo demonstra princípios gerais de acesso ao ponto final regional de Gremlin. A aplicação deve considerar o número de regiões para enviar o tráfego para e o número de clientes Gremlin correspondentes para instantaneamente.

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

## <a name="sdk-endpoint-discovery"></a>Descoberta de ponto final do SDK

A aplicação pode usar [o Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) para descobrir locais de leitura e escrita para a conta Graph. Estas localizações podem ser alteradas a qualquer momento através da reconfiguração manual do lado do servidor ou da falha automática.

TinkerPop Gremlin SDK não tem uma API para descobrir regiões de contas de dados de dados cosmos DB Graph. As aplicações que precisam de ser descobertas no ponto final de execução precisam de acolher 2 SDKs separados no espaço de processo.

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
* [Como gerir o controlo](how-to-manage-database-account.md) de contas de base de dados em Azure Cosmos DB
* [Alta disponibilidade](high-availability.md) em Azure Cosmos DB
* [Distribuição global com Azure Cosmos DB - sob o capot](global-dist-under-the-hood.md)
* [Amostras azure CLI](cli-samples.md) para Azure Cosmos DB
