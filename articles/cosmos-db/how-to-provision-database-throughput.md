---
title: Aprovisionar débito da base de dados no Azure Cosmos DB
description: Saiba como fornecer a produção ao nível da base de dados em Azure Cosmos DB utilizando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 0ba86d6955805fc97743a1a51aa586b179d82235
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82869865"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Provisão para uma base de dados em Azure Cosmos DB

Este artigo explica como fornecer o resultado numa base de dados em Azure Cosmos DB. Pode aprovisionar débito para um único [contentor](how-to-provision-container-throughput.md) ou para uma base de dados e partilhá-la entre os contentores na mesma. Para saber quando utilizar o nível de contentore e a entrada de nível de base de dados, consulte os [casos de utilização para o fornecimento](set-throughput.md) de entrada em contentores e bases de dados. Pode fornecer a entrada de nível de base de dados utilizando o portal Azure ou o Azure Cosmos DB SDKs.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionar débito com o portal do Azure

### <a name="sql-core-api"></a><a id="portal-sql"></a>API de SQL (Core)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione New **Database**. Forneça os seguintes detalhes:

   * Introduza uma identificação da base de dados.
   * Selecione **a entrada de provisionamento**.
   * Introduza uma entrada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Screenshot da nova caixa de diálogo da base de dados](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Produção de provisão utilizando o Azure CLI ou o PowerShell

Para criar uma base de dados com a ver de entrada partilhada,

* [Criar uma base de dados usando o Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Criar uma base de dados usando powershell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Aprovisionar débito com o SDK do .NET

> [!Note]
> Você pode usar SDKs Cosmos para SQL API para fornecer entrada para todas as APIs. Também pode utilizar opcionalmente o seguinte exemplo para a Cassandra API.

### <a name="all-apis"></a><a id="dotnet-all"></a>Todas as APIs

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API de Cassandra

Um comando semelhante pode ser executado através de qualquer controlador compatível com cql.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para conhecer sobre a provisão disponibilizada em Azure Cosmos DB:

* [Dimensionamento global de débito aprovisionado](scaling-throughput.md)
* [Aprovisionar débito em contentores e bases de dados](set-throughput.md)
* [Como aprovisionar o débito para um contentor](how-to-provision-container-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
