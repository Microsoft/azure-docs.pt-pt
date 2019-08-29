---
title: Aprovisionar débito da base de dados no Azure Cosmos DB
description: Aprenda a aprovisionar o débito ao nível da base de dados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 29bc65c8afaa1fe4bdc39923bd2219184e8b3a96
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093004"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Provisione a taxa de transferência em um banco de dados no Azure Cosmos DB

Este artigo explica como provisionar a taxa de transferência em um banco de dados no Azure Cosmos DB. Pode aprovisionar débito para um único [contentor](how-to-provision-container-throughput.md) ou para uma base de dados e partilhá-la entre os contentores na mesma. Para saber quando usar a taxa de transferência no nível de contêiner e de banco de dados, consulte o artigo [casos de uso para provisionar a taxa de transferência em contêineres e bancos](set-throughput.md) de dados. Você pode provisionar a taxa de transferência no nível do banco de dados usando o portal do Azure ou Azure Cosmos DB SDKs.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionar débito com o portal do Azure

### <a id="portal-sql"></a>API de SQL (Core)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account)ou selecione uma conta existente do Azure Cosmos.

1. Abra o painel **Data Explorer** e selecione **novo banco de dados**. Forneça os seguintes detalhes:

   * Insira uma ID de banco de dados. 
   * Selecione **provisionar taxa de transferência**.
   * Insira uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de tela da caixa de diálogo novo banco de dados](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)


## <a name="provision-throughput-using-azure-cli"></a>Aprovisionar débito com a CLI do Azure

```azcli-interactive
az cosmosdb database create --db-name
                            [--key]
                            [--name]
                            [--resource-group-name]
                            [--subscription]
                            [--throughput]
                            [--url-connection]
```




## <a name="provision-throughput-using-powershell"></a>Provisionar taxa de transferência usando o PowerShell

```azurepowershell-interactive
# Create a database and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName

$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties
```

## <a name="provision-throughput-using-net-sdk"></a>Aprovisionar débito com o SDK do .NET

> [!Note]
> Você pode usar SDKs do cosmos para a API do SQL para provisionar a taxa de transferência para todas as APIs. Opcionalmente, você também pode usar o exemplo a seguir para API do Cassandra.

### <a id="dotnet-all"></a>Todas as APIs
### <a name="net-v2-sdk"></a>SDK do .net v2

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a name="net-v3-sdk"></a>SDK do .net v3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>API para Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Passos seguintes

Consulte os artigos a seguir para saber mais sobre a taxa de transferência provisionada no Azure Cosmos DB:

* [Dimensionar globalmente a taxa de transferência provisionada](scaling-throughput.md)
* [Provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md)
* [Como aprovisionar o débito para um contentor](how-to-provision-container-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
