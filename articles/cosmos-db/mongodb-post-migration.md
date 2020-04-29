---
title: Etapas de otimização pós-migração com API da Azure Cosmos DB para MongoDB
description: Este doc fornece as técnicas de otimização pós-migração de MongoDB a APi da Azure Cosmos DB para Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063624"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de otimização pós-migração ao utilizar a API do Azure Cosmos DB para o MongoDB

Depois de migrar os dados armazenados na base de dados da MongoDB para a API da Azure Cosmos DB para o MongoDB, pode ligar-se ao Azure Cosmos DB e gerir os dados. Este guia fornece os passos que deve considerar após a migração. Consulte o [MongoDB migratório para a API do Azure Cosmos DB para o tutorial mongoDB](../dms/tutorial-mongodb-cosmos-db.md) para os passos de migração.

Neste guia, vai aprender a:

- [Ligue a sua aplicação](#connect-your-application)
- [Otimizar a política de indexação](#optimize-the-indexing-policy)
- [Configure distribuição global para API da Azure Cosmos DB para MongoDB](#globally-distribute-your-data)
- [Definir nível de consistência](#set-consistency-level)

> [!NOTE]
> O único passo obrigatório pós-migração no seu nível de candidatura é alterar a cadeia de ligação na sua aplicação para apontar para a sua nova conta Azure Cosmos DB. Todos os outros passos de migração são otimizações recomendadas.
>

## <a name="connect-your-application"></a>Ligue a sua aplicação

1. Em um novo sinal de janela para o [portal Azure](https://www.portal.azure.com/)
2. A partir do [portal Azure,](https://www.portal.azure.com/)no painel esquerdo abra o menu **Todos os recursos** e encontre a conta Azure Cosmos DB para a qual emigrou os seus dados.
3. Abra a lâmina de corda de **ligação.** O painel do lado direito contém todas as informações de que precisa para se ligar com êxito à sua conta.
4. Utilize as informações de ligação na configuração da sua aplicação (ou outros locais relevantes) para refletir a API do Azure Cosmos DB para a ligação MongoDB na sua aplicação.
![Linha de ligação](./media/mongodb-post-migration/connection-string.png)

Para mais detalhes, consulte a [aplicação Connect a MongoDB para a página DD Do Azure Cosmos.](connect-mongodb-account.md)

## <a name="optimize-the-indexing-policy"></a>Otimizar a política de indexação

Todos os campos de dados são automaticamente indexados, por padrão, durante a migração de dados para o Azure Cosmos DB. Em muitos casos, esta política de indexação por incumprimento é aceitável. Em geral, a remoção de índices otimiza os pedidos de escrita e a política de indexação padrão (isto é, indexação automática) otimiza os pedidos de leitura.

Para obter mais informações sobre indexação, consulte a [indexação de dados na API da Azure Cosmos DB para MongoDB,](mongodb-indexing.md) bem como a [Indexação em artigos da Azure Cosmos DB.](index-overview.md)

## <a name="globally-distribute-your-data"></a>Distribua globalmente os seus dados

O Azure Cosmos DB está disponível em todas as [regiões do Azure](https://azure.microsoft.com/regions/#services) em todo o mundo. Depois de selecionar o nível de consistência padrão para a sua conta Azure Cosmos DB, pode associar uma ou mais regiões Azure (dependendo das suas necessidades de distribuição global). Para uma elevada disponibilidade e continuidade de negócios, recomendamos sempre correr em pelo menos 2 regiões. Pode rever as dicas para otimizar o [custo das implantações multi-regiões em Azure Cosmos DB](optimize-cost-regions.md).

Para distribuir globalmente os seus dados, consulte [distribuir dados globalmente na API da Azure Cosmos DB para mongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Definir nível de consistência

O Azure Cosmos DB oferece 5 níveis de consistência bem [definidos.](consistency-levels.md) Para ler sobre o mapeamento entre os níveis de consistência db mongoDB e Azure Cosmos, leia os níveis de [consistência e as APIs DB Do Cosmos Azure.](consistency-levels-across-apis.md) O nível de consistência padrão é o nível de consistência da sessão. Mudar o nível de consistência é opcional e pode otimizá-lo para a sua aplicação. Para alterar o nível de consistência utilizando o portal Azure:

1. Vá para a lâmina de **consistência predefinida** em Definições.
2. Selecione o seu [nível de consistência](consistency-levels.md)

A maioria dos utilizadores deixa o seu nível de consistência na definição de consistência da sessão predefinida. No entanto, existem trocas de [disponibilidade e desempenho para vários níveis](consistency-levels-tradeoffs.md)de consistência.

## <a name="next-steps"></a>Passos seguintes

* [Ligar uma aplicação MongoDB ao Azure Cosmos DB](connect-mongodb-account.md)
* [Ligue-se à conta DB Azure Cosmos usando o Studio 3T](mongodb-mongochef.md)
* [Como distribuir globalmente as leituras usando a API da Azure Cosmos DB para mongoDB](mongodb-readpreference.md)
* [Dados de expiração com a API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Níveis de consistência em Azure Cosmos DB](consistency-levels.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Request Units in Azure Cosmos DB](request-units.md) (Unidades de Pedido no Azure Cosmos DB)