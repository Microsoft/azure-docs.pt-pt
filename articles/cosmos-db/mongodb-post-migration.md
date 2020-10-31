---
title: Etapas de otimização pós-migração com API da Azure Cosmos DB para MongoDB
description: Este doc fornece as técnicas de otimização pós-migração de MongoDB a Azure Cosmos DB's APi para Mongo DB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: jasonh
ms.openlocfilehash: 3b0c291f942cd970f7996f0b9fa599ee8cabfdab
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096449"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de otimização pós-migração ao utilizar a API da Azure Cosmos para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Depois de migrar os dados armazenados na base de dados do MongoDB para a API do Azure Cosmos DB para o MongoDB, pode ligar-se ao Azure Cosmos DB e gerir os dados. Este guia fornece os passos que deve ter em conta após a migração. Veja o [tutorial da API da API da Azure Cosmos para](../dms/tutorial-mongodb-cosmos-db.md) as etapas de migração.

Neste guia, vai aprender a:

- [Ligue a sua aplicação](#connect-your-application)
- [Otimizar a política de indexação](#optimize-the-indexing-policy)
- [Configure distribuição global para A API da Azure Cosmos DB para a MongoDB](#globally-distribute-your-data)
- [Definir nível de consistência](#set-consistency-level)

> [!NOTE]
> O único passo obrigatório após a migração no seu nível de aplicação é alterar a cadeia de ligação na sua aplicação para indicar a sua nova conta DB Azure Cosmos. Todas as outras etapas de migração são recomendadas otimizações.
>

## <a name="connect-your-application"></a>Ligue a sua aplicação

1. Em um novo sinal de janela para o [portal Azure](https://www.portal.azure.com/)
2. A partir do [portal Azure,](https://www.portal.azure.com/)no painel esquerdo abra o menu **Todos os recursos** e encontre a conta DB Azure Cosmos para a qual emigrou os seus dados.
3. Abra a **lâmina de ligação.** O painel do lado direito contém todas as informações de que precisa para se ligar com êxito à sua conta.
4. Utilize as informações de ligação na configuração da sua aplicação (ou noutros locais relevantes) para refletir a API da Azure Cosmos para a ligação mongoDB na sua aplicação.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="A screenshot mostra as definições de uma cadeia de ligação.":::

Para mais detalhes, consulte a [aplicação Connect a MongoDB na página DB do Azure Cosmos.](connect-mongodb-account.md)

## <a name="optimize-the-indexing-policy"></a>Otimizar a política de indexação

Todos os campos de dados são automaticamente indexados, por padrão, durante a migração de dados para Azure Cosmos DB. Em muitos casos, esta política de indexação por defeito é aceitável. Em geral, a remoção de índices otimiza os pedidos de escrita e a política de indexação por defeito (ou seja, indexação automática) otimiza os pedidos de leitura.

Para obter mais informações sobre a indexação, consulte [a indexação de dados na API da Azure Cosmos para a MongoDB,](mongodb-indexing.md) bem como a Indexação em artigos [DB do Azure Cosmos.](index-overview.md)

## <a name="globally-distribute-your-data"></a>Distribuir globalmente os seus dados

A Azure Cosmos DB está disponível em todas as [regiões do Azure](https://azure.microsoft.com/regions/#services) em todo o mundo. Depois de selecionar o nível de consistência padrão para a sua conta DB Azure Cosmos, pode associar uma ou mais regiões Azure (dependendo das suas necessidades globais de distribuição). Para uma elevada disponibilidade e continuidade de negócios, recomendamos sempre correr em pelo menos 2 regiões. Pode rever as dicas para otimizar o [custo das implementações multi-regiões em Azure Cosmos DB](optimize-cost-regions.md).

Para distribuir globalmente os seus dados, consulte [distribuir os dados globalmente na API da Azure Cosmos para a MongoDB.](tutorial-global-distribution-mongodb.md)

## <a name="set-consistency-level"></a>Definir nível de consistência

A Azure Cosmos DB oferece 5 níveis de consistência bem [definidos.](consistency-levels.md) Para ler sobre o mapeamento entre os níveis de consistência do MongoDB e do Azure Cosmos DB, leia os [níveis de consistência e as APIs DB do Cosmos Azure Cosmos](./consistency-levels.md). O nível de consistência predefinido é o nível de consistência da sessão. Alterar o nível de consistência é opcional e pode otimizá-lo para a sua aplicação. Para alterar o nível de consistência utilizando o portal Azure:

1. Aceda à lâmina **de consistência predefinido** em Definições.
2. Selecione o seu [nível de consistência](consistency-levels.md)

A maioria dos utilizadores deixa o seu nível de consistência na definição de consistência da sessão predefinida. No entanto, [existem compensações de disponibilidade e desempenho para vários níveis de consistência.](./consistency-levels.md)

## <a name="next-steps"></a>Passos seguintes

* [Ligar uma aplicação MongoDB ao Azure Cosmos DB](connect-mongodb-account.md)
* [Ligue-se à conta DB da Azure Cosmos usando o Studio 3T](mongodb-mongochef.md)
* [Como distribuir globalmente leituras usando AZure Cosmos DB's API para MongoDB](mongodb-readpreference.md)
* [Dados de expiração com a API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Níveis de consistência em Azure Cosmos DB](consistency-levels.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)