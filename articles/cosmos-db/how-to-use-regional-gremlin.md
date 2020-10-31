---
title: Pontos finais regionais para a base de dados de gráficos DB do Azure Cosmos
description: Saiba como ligar-se ao ponto final da base de dados de gráfico mais próximo para a sua aplicação
author: jasonwhowell
ms.author: jasonh
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/09/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 9350682f7c636979df4dcde0c43a3b4941ad6ebb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085773"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Pontos finais regionais para conta DB de Azure Cosmos
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

A base de dados do Azure Cosmos DB Graph é [distribuída globalmente](distribute-data-globally.md) para que as aplicações possam usar vários pontos finais de leitura. As aplicações que precisam de escrever acesso em vários locais devem permitir a capacidade [de escrita de várias regiões.](how-to-multi-master.md)

Razões para escolher mais do que uma região:
1. **Escalabilidade de leitura horizontal** - à medida que a carga de aplicação aumenta, pode ser prudente fazer a leitura do tráfego para diferentes regiões de Azure.
2. **Menor latência** - você pode reduzir a latência da rede por cima de cada travessia através do encaminhamento ler e escrever tráfego para a região de Azure mais próxima.

O requisito **de residência de dados** é alcançado através da definição da política do Gestor de Recursos Azure na conta de DB da Cosmos. O cliente pode limitar as regiões em que a Cosmos DB replica dados.

## <a name="traffic-routing"></a>Encaminhamento de tráfego

O motor da base de dados Cosmos DB Graph está a funcionar em várias regiões, cada uma das quais contém vários clusters. Cada aglomerado tem centenas de máquinas. Cosmos DB Graph conta DNS CNAME *accountname.gremlin.cosmos.azure.com* resolve para DNS Um registo de um cluster. Um único endereço IP de um equilibrador de carga esconde a topologia interna do cluster.

Um registo regional de DNS CNAME é criado para todas as regiões da conta Cosmos DB Graph. O formato do ponto final regional é *accountname-region.gremlin.cosmos.azure.com* . O segmento regional do ponto final regional é obtido removendo todos os espaços do nome da região de [Azure.](https://azure.microsoft.com/global-infrastructure/regions) Por exemplo, `"East US 2"` a região para `"contoso"` a conta global de bases de dados teria um *CONTOSO-EASTUS2.GREMLIN.COSMOS.AZURE.COM DENS CNAME*

O cliente TinkerPop Gremlin foi concebido para trabalhar com um único servidor. A aplicação pode usar DNS CNAME global para ler e escrever tráfego. Os pedidos de conhecimento da região devem utilizar o ponto final regional para a leitura do tráfego. Utilize o ponto final regional para escrever tráfego apenas se uma região específica estiver configurada para aceitar escritas. 

> [!NOTE]
> O motor Cosmos DB Graph pode aceitar a operação de escrita na região de leitura, procurando tráfego para escrever região. Não é aconselhável enviar escritos para a região só de leitura, uma vez que aumenta a latência transversal e está sujeito a restrições no futuro.

A conta de base de dados global CNAME aponta sempre para uma região de escrita válida. Durante a falha do lado do servidor da região de escrita, a Cosmos DB atualiza a conta global de base de dados CNAME para apontar para uma nova região. Se a aplicação não conseguir lidar com o reencaminhamento de tráfego após o failover, deve utilizar a conta de base de dados global DNS CNAME.

> [!NOTE]
> Cosmos DB não encaminha o tráfego com base na proximidade geográfica do chamador. Cabe a cada aplicação selecionar a região certa de acordo com as necessidades únicas da aplicação.

## <a name="portal-endpoint-discovery"></a>Descoberta do ponto final do portal

A maneira mais fácil de obter a lista de regiões para a conta DB de Azure Cosmos é a lâmina geral no portal Azure. Funcionará para aplicações que não mudam frequentemente de regiões, ou que têm uma forma de atualizar a lista através da configuração da aplicação.

:::image type="content" source="./media/how-to-use-regional-gremlin/get-end-point-portal.png " alt-text="Recuperar regiões da conta de GráficoS de Cosmos do portal":::

O exemplo a seguir demonstra princípios gerais de acesso ao ponto final regional de Gremlin. A aplicação deve considerar o número de regiões para enviar o tráfego e o número de clientes gremlin correspondentes para instantaneamente.

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

## <a name="sdk-endpoint-discovery"></a>Descoberta do ponto final da SDK

A aplicação pode usar [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) para descobrir locais de leitura e escrita para conta Graph. Estas localizações podem ser alteradas a qualquer momento através da reconfiguração manual do lado do servidor ou da falha automática.

A TinkerPop Gremlin SDK não tem uma API para descobrir regiões de conta de base de dados do Cosmos DB Graph. As aplicações que precisam de descoberta de ponto final em tempo de execução precisam de acolher 2 SDKs separados no espaço do processo.

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
* [Como gerir o controlo de contas de base de dados](how-to-manage-database-account.md) em Azure Cosmos DB
* [Alta disponibilidade](high-availability.md) em Azure Cosmos DB
* [Distribuição global com Azure Cosmos DB - sob o capot](global-dist-under-the-hood.md)
* [Amostras de Azure CLI](cli-samples.md) para Azure Cosmos DB
