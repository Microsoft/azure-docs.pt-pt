---
title: Criar um recipiente em Azure Cosmos DB API para MongoDB
description: Saiba como criar um recipiente em Azure Cosmos DB API para a MongoDB utilizando o portal Azure, .NET, Java, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101600"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Criar um recipiente em Azure Cosmos DB API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo explica as diferentes formas de criar um recipiente em Azure Cosmos DB API para a MongoDB. Mostra como criar um recipiente utilizando o portal Azure, Azure CLI, PowerShell ou SDKs suportados. Este artigo demonstra como criar um recipiente, especificar a chave de partição e a produção de provisão.

Este artigo explica as diferentes formas de criar um recipiente em Azure Cosmos DB API para a MongoDB. Se estiver a utilizar uma API diferente, consulte [a SQL API,](how-to-create-container.md) [a Cassandra API,](how-to-create-container-cassandra.md) [a Gremlin API](how-to-create-container-gremlin.md)e os artigos [da Table API](how-to-create-container-table.md) para criar o recipiente.

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Criar com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Explorador de Dados** e selecione **Novo Recipiente**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza a identificação do contentor.
   * Introduza uma chave de fragmentos.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Screenshot de Azure Cosmos DB API para MongoDB, adicionar caixa de diálogo de recipiente":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Criar usando .NET SDK

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> O protocolo de fio MongoDB não compreende o conceito de [Unidades](request-units.md)de Pedido . Para criar uma nova coleção com produção a fortalhada, utilize o portal Azure ou Cosmos DB SDKs para API SQL.

Se encontrar exceção no tempo limite ao criar uma coleção, faça uma operação de leitura para validar se a coleção foi criada com sucesso. A operação de leitura abre uma exceção até que a operação de criação de recolha seja bem sucedida. Para a lista de códigos de estado suportados pela operação de criação consulte os Códigos de Estado HTTP para o artigo [DB do Azure Cosmos.](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando O Azure CLI

[Crie uma coleção para Azure Cosmos DB para MongoDB API com Azure CLI](./scripts/cli/mongodb/create.md). Para uma lista de todas as amostras do Azure CLI em todas as APIs do Azure Cosmos ver, [amostras de Azure CLI para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar com o PowerShell

[Crie uma coleção para Azure Cosmos DB para MongoDB API com PowerShell](./scripts/powershell/mongodb/create.md). Para uma lista de todas as amostras powerShell em todas as APIs do Azure Cosmos ver, [Amostras powerShell](powershell-samples.md)

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Criar um recipiente usando modelos de Gestor de Recursos Azure

[Crie uma coleção para Azure Cosmos DB para MongoDB API com modelo de Gestor de Recursos](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](./account-databases-containers-items.md)