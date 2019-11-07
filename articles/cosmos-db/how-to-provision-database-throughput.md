---
title: Aprovisionar débito da base de dados no Azure Cosmos DB
description: Aprenda a aprovisionar o débito ao nível da base de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 64a8bc9f4c9f5192dad22cb55cb4d2c4816d4fa5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575148"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Provisione a taxa de transferência em um banco de dados no Azure Cosmos DB

Este artigo explica como provisionar a taxa de transferência em um banco de dados no Azure Cosmos DB. Pode aprovisionar débito para um único [contentor](how-to-provision-container-throughput.md) ou para uma base de dados e partilhá-la entre os contentores na mesma. Para saber quando usar a taxa de transferência no nível de contêiner e de banco de dados, consulte o artigo [casos de uso para provisionar a taxa de transferência em contêineres e bancos](set-throughput.md) de dados. Você pode provisionar a taxa de transferência no nível do banco de dados usando o portal do Azure ou Azure Cosmos DB SDKs.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionar débito com o portal do Azure

### <a id="portal-sql"></a>API de SQL (Core)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account)ou selecione uma conta existente do Azure Cosmos.

1. Abra o painel **Data Explorer** e selecione **novo banco de dados**. Forneça os seguintes detalhes:

   * Insira uma ID de banco de dados.
   * Selecione **provisionar taxa de transferência**.
   * Insira uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Captura de tela da caixa de diálogo novo banco de dados](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Provisionar taxa de transferência usando o CLI do Azure ou o PowerShell

Para criar um banco de dados com taxa de transferência compartilhada, consulte

* [Criar um banco de dados usando CLI do Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Criar um banco de dados usando o PowerShell](manage-with-powershell.md#create-db-ru)

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
Um comando semelhante pode ser executado por meio de qualquer driver compatível com CQL. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Passos seguintes

Consulte os artigos a seguir para saber mais sobre a taxa de transferência provisionada no Azure Cosmos DB:

* [Dimensionar globalmente a taxa de transferência provisionada](scaling-throughput.md)
* [Provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md)
* [Como aprovisionar o débito para um contentor](how-to-provision-container-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
