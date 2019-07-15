---
title: Aprovisionar débito da base de dados no Azure Cosmos DB
description: Aprenda a aprovisionar o débito ao nível da base de dados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: e44017ea2df24445c28361a0f3d7fb2671317b6d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000651"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Débito de aprovisionar uma base de dados no Azure Cosmos DB

Este artigo explica como aprovisionar o débito, numa base de dados no Azure Cosmos DB. Pode aprovisionar débito para um único [contentor](how-to-provision-container-throughput.md) ou para uma base de dados e partilhá-la entre os contentores na mesma. Para saber quando utilizar o débito de nível de contêiner e ao nível da base de dados, veja a [casos de utilização para o aprovisionamento de débito em contentores e bases de dados](set-throughput.md) artigo. Pode aprovisionar o débito de nível de base de dados com o portal do Azure ou SDKs do Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionar débito com o portal do Azure

### <a id="portal-sql"></a>API de SQL (Core)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account), ou selecione uma conta do Cosmos do Azure existente.

1. Abra o **Data Explorer** painel e selecione **nova base de dados**. Indique os detalhes seguintes:

   * Introduza um ID de base de dados. 
   * Selecione **débito aprovisionar**.
   * Introduza uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Caixa de diálogo de captura de ecrã da nova base de dados](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-powershell"></a>Débito de aprovisionamento com o PowerShell

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
> Pode utilizar SDKs do Cosmos para a API de SQL para aprovisionar débito para todas as APIs. Pode, opcionalmente, utilize o seguinte exemplo para a API de Cassandra também.

### <a id="dotnet-all"></a>Todas as APIs
### <a name="net-v2-sdk"></a>.NET V2 SDK

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

### <a name="net-v3-sdk"></a>.NET V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>API para Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre o débito aprovisionado no Azure Cosmos DB:

* [Dimensione globalmente o débito aprovisionado](scaling-throughput.md)
* [Débito de aprovisionar em contentores e bases de dados](set-throughput.md)
* [Como aprovisionar o débito para um contentor](how-to-provision-container-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)