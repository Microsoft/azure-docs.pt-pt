---
title: Aprovisionar débito da base de dados no Azure Cosmos DB
description: Aprenda a aprovisionar o débito ao nível da base de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 8c2fe8d42ded5a072a2211b102736326554bc4f4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57239891"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Aprovisione o débito para a base de dados no Azure Cosmos DB

Este artigo explica como aprovisionar débito para uma base de dados no Azure Cosmos DB. Pode aprovisionar débito para um único [contentor](how-to-provision-container-throughput.md) ou para uma base de dados e partilhá-la entre os contentores na mesma. Para saber quando utilizar o débito de nível de contêiner e ao nível da base de dados, veja a [casos de utilização para o aprovisionamento de débito em contentores e bases de dados](set-throughput.md) artigo. Pode aprovisionar o débito de nível de base de dados com o portal do Azure ou SDKs do Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Débito de aprovisionamento através do portal do Azure

### <a id="portal-sql"></a>API de SQL (Core)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova base de dados**. Indique os detalhes seguintes:

   * Introduza um ID de base de dados. 
   * Selecione **débito aprovisionar**.
   * Introduza uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Caixa de diálogo de captura de ecrã da nova base de dados](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>Débito de aprovisionamento com o .NET SDK

> [!Note]
> Utilize a API de SQL para aprovisionar débito para todas as APIs. Pode, opcionalmente, utilize o seguinte exemplo para a API de Cassandra também.

### <a id="dotnet-all"></a>Todas as APIs

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>API para Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre o aprovisionamento de débito no Azure Cosmos DB:

* [Como aprovisionar o débito para um contentor](how-to-provision-container-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
