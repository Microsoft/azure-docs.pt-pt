---
title: Mover uma conta DB Azure Cosmos para outra região
description: Saiba como mover uma conta DB do Azure Cosmos para outra região.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342033"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Mover uma conta DB Azure Cosmos para outra região
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como:

- Mova uma região onde os dados são replicados em Azure Cosmos DB.
- Migrar dados e dados da conta migrar (Azure Resource Manager) e dados de uma região para outra.

## <a name="move-data-from-one-region-to-another"></a>Mover dados de uma região para outra

O Azure Cosmos DB suporta a replicação de dados de forma nativa, pelo que mover dados de uma região para outra é simples. Pode fazê-lo utilizando o portal Azure PowerShell ou o Azure CLI. Envolve os seguintes passos:

1. Adicione uma nova região à conta.

    Para adicionar uma nova região a uma conta DB da Azure Cosmos, consulte [regiões de Adicionar/remover a uma conta DB da Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Execute um failover manual para a nova região.

    Quando a região que está a ser removida é atualmente a região de escrita para a conta, você precisará iniciar um fracasso para a nova região adicionada no passo anterior. Esta é uma operação de tempo zero. Se você está movendo uma região de leitura em uma conta de várias regiões, você pode saltar este passo. 
    
    Para iniciar uma falha, consulte [Executar o failover manual numa conta Azure Cosmos](how-to-manage-database-account.md#manual-failover).

1. Remova a região original.

    Para remover uma região de uma conta DB da Azure Cosmos, consulte [regiões adicionais/removidas da sua conta DB Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Migrar metadados de conta DB da Azure Cosmos

A Azure Cosmos DB não suporta nativo metadados de conta migratória de uma região para outra. Para migrar tanto os metadados de conta como os dados dos clientes de uma região para outra, é necessário criar uma nova conta na região desejada e, em seguida, copiar os dados manualmente. 

Uma migração quase zero para o API SQL requer a utilização do feed de [alteração](change-feed.md) ou de uma ferramenta que o utilize. Se estiver a migrar a API mongodb, a API de Cassandra ou outra API, ou a aprender mais sobre opções de migração de dados entre contas, consulte [Opções para migrar os seus dados no local ou em nuvem para Azure Cosmos DB](cosmosdb-migrationchoices.md). 

Os seguintes passos demonstram como migrar uma conta DB do Azure Cosmos para a API SQL e os seus dados de uma região para outra:

1. Crie uma nova conta DB Azure Cosmos na região desejada.

    Para criar uma nova conta através do portal Azure, PowerShell ou do Azure CLI, consulte [Criar uma conta DB Azure Cosmos](how-to-manage-database-account.md#create-an-account).

1. Criar uma nova base de dados e um contentor.

    Para criar uma nova base de dados e um recipiente, consulte [criar um recipiente Azure Cosmos.](how-to-create-container.md)

1. Migrar dados utilizando a ferramenta Azure Cosmos DB Live Data Migrator.

    Para migrar dados com quase zero tempo de inatividade, consulte [a ferramenta Azure Cosmos DB Live Data Migrator](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Atualize a cadeia de ligação da aplicação.

    Com a ferramenta Migrador de Dados Vivos ainda em funcionamento, atualize as informações de ligação na nova implementação da sua aplicação. Pode recuperar os pontos finais e as chaves da sua aplicação a partir do portal Azure.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controlo de acesso no portal Azure, demonstrando segurança na base de dados NoSQL.":::

1. Redirecione os pedidos para a nova aplicação.

    Depois de a nova aplicação estar ligada à Azure Cosmos DB, pode redirecionar os pedidos do cliente para a sua nova implementação.

1. Elimine todos os recursos que já não precisar.

    Com pedidos agora totalmente redirecionados para a nova instância, pode eliminar a antiga conta DB do Azure Cosmos e a ferramenta Migradora de Dados Ao Vivo.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos sobre como gerir a conta da Azure Cosmos, bem como bases de dados e contentores, leia os seguintes artigos:

* [Gerir uma conta do Azure Cosmos](how-to-manage-database-account.md)
* [Mude o feed em Azure Cosmos DB](change-feed.md)
