---
title: Criar um recipiente em Azure Cosmos DB Cassandra API
description: Saiba como criar um recipiente em Azure Cosmos DB Cassandra API utilizando o portal Azure, .NET, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 01030a563c15eee7786058c2eae30d23803dfe42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101651"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>Criar um recipiente em Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB Cassandra API. Mostra como criar um recipiente utilizando o portal Azure, Azure CLI, PowerShell ou SDKs suportados. Este artigo demonstra como criar um recipiente, especificar a chave de partição e a produção de provisão.

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB Cassandra API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](how-to-create-container-mongodb.md) [Gremlin API,](how-to-create-container-gremlin.md) [Table API](how-to-create-container-table.md)e [ARTIGOS API SQL](how-to-create-container.md) para criar o recipiente.

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>Criar com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-cassandra-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione Nova **Tabela**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar um novo espaço-chave ou a utilizar um existente.
   * Introduza um nome de tabela.
   * Introduza as propriedades e especifique uma chave primária.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Screenshot de Cassandra API, Adicionar caixa de diálogo de mesa":::

> [!NOTE]
> Para a API para Cassandra, a chave primária é utilizada como a chave de partição.

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>Criar usando .NET SDK

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

Se encontrar exceção no tempo limite ao criar uma coleção, faça uma operação de leitura para validar se a coleção foi criada com sucesso. A operação de leitura abre uma exceção até que a operação de criação de recolha seja bem sucedida. Para a lista de códigos de estado suportados pela operação de criação consulte os Códigos de Estado HTTP para o artigo [DB do Azure Cosmos.](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando O Azure CLI

[Crie uma tabela Cassandra com Azure CLI](./scripts/cli/cassandra/create.md). Para uma lista de todas as amostras do Azure CLI em todas as APIs do Azure Cosmos ver, [amostras de Azure CLI para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar com o PowerShell

[Crie uma tabela Cassandra com PowerShell](./scripts/powershell/cassandra/create.md). Para uma lista de todas as amostras powerShell em todas as APIs do Azure Cosmos ver, [Amostras powerShell](powershell-samples.md)

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](./account-databases-containers-items.md)