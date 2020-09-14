---
title: Saiba como mover uma conta DB do Azure Cosmos para outra região
description: Saiba como mover uma conta DB do Azure Cosmos para outra região
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059452"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Como mover uma conta DB do Azure Cosmos para outra região

Este artigo descreve como mover uma região onde os dados são replicados em Azure Cosmos DB ou como migrar os dados de meta da conta (Azure Resource Manager), bem como os dados de uma região para outra.

## <a name="move-data-from-one-region-to-another"></a>Mover dados de uma região para outra

O Azure Cosmos DB suporta a replicação de dados de forma nativa, pelo que a deslocação de dados de uma região para outra é simples e pode ser realizada utilizando o portal Azure PowerShell ou Azure CLI e envolve os seguintes passos:

1. Adicione uma nova região à conta

    Para adicionar uma nova região a uma conta DB Azure Cosmos ver, [adicionar/remover regiões a uma conta DB Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. Realizar um failover manual para a nova região

    Nas situações em que a região que está a ser removida é atualmente a região de escrita para a conta, terá de iniciar uma falha na nova região acima acrescentada. Esta é uma operação de inatividade zero. Se você estiver movendo uma região de leitura em uma conta multi-região você pode saltar este passo. Para iniciar uma falha, [execute o failover manual numa conta Azure Cosmos](how-to-manage-database-account.md#manual-failover)

1. Remover a região original

    Para remover uma região de uma conta DB Azure Cosmos ver, [adicionar/remover regiões para uma conta DB Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Migrar dados de conta DB da Azure Cosmos

A Azure Cosmos DB não suporta nativos dados de metades de conta migratória de uma região para outra. Para migrar tanto os dados de meta da conta como os dados dos clientes de uma região para outra, deve ser criada uma nova conta na região desejada e, em seguida, os dados devem ser copiados manualmente. Uma migração quase zero para a API SQL requer a utilização do [ChangeFeed](change-feed.md) ou de uma ferramenta que o alavanca. Se migrar a API mongoDB, Cassandra ou outra API ou para saber mais sobre opções ao migrar dados entre contas ver [Opções para migrar os seus dados no local ou em nuvem para Azure Cosmos DB](cosmosdb-migrationchoices.md). Os passos abaixo demonstram como migrar uma conta DB Azure Cosmos para a API SQL e seus dados de uma região para outra:

1. Criar uma nova conta DB Azure Cosmos na região desejada

    Para criar uma nova conta através do portal Azure, PowerShell ou CLI ver, [Crie uma conta DB Azure Cosmos](how-to-manage-database-account.md#create-an-account).

1. Criar uma nova base de dados e recipiente

    Para criar uma nova base de dados e um recipiente ver, [crie um recipiente Azure Cosmos](how-to-create-container.md)

1. Migrar dados com a ferramenta Azure Cosmos DB Live Data Migrator

    Para migrar dados com quase zero tempo de inatividade consulte [a ferramenta migradora de dados ao vivo do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Cadeia de ligação de aplicação de atualização

    Com a ferramenta migradora viva ainda em funcionamento, atualize as informações de ligação na nova implementação das suas aplicações. Os pontos finais e as chaves da sua aplicação podem ser recuperados a partir do portal Azure.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados NoSQL":::

1. Redirecionar pedidos para nova aplicação

    Uma vez que a nova aplicação esteja ligada à Azure Cosmos DB, pode redirecionar os pedidos do cliente para a sua nova implementação.

1. Eliminar todos os recursos que já não sejam necessários

    Com os pedidos agora totalmente redirecionados para a nova instância, pode então eliminar a antiga conta DB do Azure Cosmos e a ferramenta Migradora de Dados Ao Vivo.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações e exemplos sobre como gerir a conta Azure Cosmos, bem como bases de dados e contentores, leia os seguintes artigos:

* [Gerir uma conta do Azure Cosmos](how-to-manage-database-account.md)
* [Mude o feed em Azure Cosmos DB](change-feed.md)
