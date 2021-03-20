---
title: Produção de provisão sobre os recursos da Azure Cosmos DB Cassandra API
description: Saiba como providenciar contentores, bases de dados e produção de escala automática nos recursos Azure Cosmos DB Cassandra API. Utilizará o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086283"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Base de dados, contentores ou produção de escala automática nos recursos API API da Azure Cosmos DB Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo explica como provisão para a produção na Azure Cosmos DB Cassandra API. Pode providenciar padrão (manual) ou produção de escala automática num contentor ou numa base de dados e partilhá-la entre os recipientes dentro da base de dados. Você pode provistuar produção usando o portal Azure CLI, ou Azure Cosmos DB SDKs.

Se estiver a utilizar uma API diferente, consulte [a API SQL,](how-to-provision-container-throughput.md) [API para MongoDB,](how-to-provision-throughput-mongodb.md)artigos [da Gremlin API](how-to-provision-throughput-gremlin.md) para prever o rendimento.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione Nova **Tabela**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar um novo espaço-chave ou a utilizar um existente. Selecione a opção **de produção da base de dados De provisionar** se pretender obter a produção no nível do espaço-chave.
   * Introduza o ID da tabela dentro do comando CQL.
   * Introduza um valor chave primário (por exemplo, `/userrID` ).
   * Introduza uma produção que pretende prever (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Screenshot do Data Explorer, ao criar uma nova coleção com produção de nível de base de dados":::

> [!Note]
> Se estiver a aagarr a produção num recipiente numa conta Azure Cosmos configurada com a API cassandra, use `/myPrimaryKey` para o caminho chave da partição.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Provisão para uma tabela Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Comandos semelhantes podem ser emitidos através de qualquer controlador compatível com CQL.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Alterar ou alterar a produção para uma tabela Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

O comando semelhante pode ser executado através de qualquer controlador compatível com CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Os modelos do Gestor de Recursos Azure podem ser usados para obter rendimento de escala automática na base de dados ou recursos de nível de contentor para todas as APIs DB de Azure Cosmos. Consulte [os modelos do Azure Resource Manager para amostras do Azure Cosmos.](templates-samples-cassandra.md)

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI pode ser usado para provisões de viação de escala automática numa base de dados ou recursos de nível de contentor para todas as APIs Azure Cosmos DB. Para amostras consulte [amostras do Azure CLI para Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell pode ser utilizado para a disponibilização de produção de escala automática numa base de dados ou recursos de nível de contentor para todas as APIs DB do Azure Cosmos. Para amostras consulte [as amostras Azure PowerShell para Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para saber sobre o fornecimento de produção em Azure Cosmos DB:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)