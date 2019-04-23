---
title: Passos de pós-migração otimização quando utilizar a API do Azure Cosmos DB para o MongoDB
description: Este documento fornece as técnicas de otimização de pós-migração do MongoDB à APi do Azure Cosmos DB para Mongo DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014316"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Passos de pós-migração otimização quando utilizar a API do Azure Cosmos DB para o MongoDB 

Depois de migrar os dados armazenados na base de dados do MongoDB à API do Azure Cosmos DB para o MongoDB, pode ligar ao Azure Cosmos DB e gerir os dados. Este guia fornece os passos que deve considerar após a migração. Consulte a [migrar MongoDB à API do Azure Cosmos DB para o tutorial de MongoDB](../dms/tutorial-mongodb-cosmos-db.md) para obter os passos de migração.

Neste guia, vai aprender a:
- [Ligar a sua aplicação](#connect-account)
- [Otimizar a política de indexação](#indexing)
- [Configurar a distribuição global para a API do Azure Cosmos DB para o MongoDB](#distribute-data)
- [Nível de consistência do conjunto](#consistency)

> [!NOTE]
> O passo de pós-migração apenas obrigatório no seu nível de aplicativo está mudando a cadeia de ligação na sua aplicação para apontar para a sua nova conta do Azure Cosmos DB. Todos os outros passos de migração são recomendados otimizações.
>

## <a id="connect-account"></a>Ligar a sua aplicação 

1. Numa nova janela de sessão para o [portal do Azure](https://www.portal.azure.com/)
2. Do [portal do Azure](https://www.portal.azure.com/), no painel esquerdo, abra o **todos os recursos** menu e localize a conta do Azure Cosmos DB para a qual migrou seus dados.
3. Abra o **cadeia de ligação** painel. O painel do lado direito contém todas as informações de que precisa para se ligar com êxito à sua conta.
4. Utilize as informações de ligação na configuração da sua aplicação (ou outros lugares relevantes) para refletir a API do Azure Cosmos DB para a ligação do MongoDB na sua aplicação. 
![Cadeia de ligação](./media/mongodb-post-migration/connection-string.png)

Para obter mais detalhes, consulte a [ligar uma aplicação MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) página.

## <a id="indexing"></a>Otimizar a política de indexação

Todos os campos de dados são automaticamente indexados, por predefinição, durante a migração de dados para o Azure Cosmos DB. Em muitos casos, esta política de indexação predefinida é aceitável. Em geral, remover índices otimiza a pedidos de escrita e ter a predefinição (ou seja, a indexação automática) de política de indexação otimiza a solicitações de leitura.

Para obter mais informações sobre indexação, consulte [dados indexar na API do Azure Cosmos DB para o MongoDB](mongodb-indexing.md) , bem como a [indexação no Azure Cosmos DB](index-overview.md) artigos.

## <a id="distribute-data"></a>Distribua globalmente os seus dados

O Azure Cosmos DB está disponível em todos os [regiões do Azure](https://azure.microsoft.com/regions/#services) em todo o mundo. Depois de selecionar o nível de consistência predefinido para a sua conta do Azure Cosmos DB, pode associar uma ou mais regiões do Azure (consoante as suas necessidades de distribuição global). Para elevada disponibilidade e continuidade do negócio, recomendamos sempre em execução em, pelo menos, 2 regiões. Pode rever as dicas para [otimizar o custo de implementações em várias regiões do Azure Cosmos DB](optimize-cost-regions.md).

Para distribuir globalmente os seus dados, veja [distribuir dados globalmente na API do Azure Cosmos DB para o MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Nível de consistência do conjunto
O Azure Cosmos DB oferece 5 bem definidos [níveis de consistência](consistency-levels.md). Para ler mais sobre o mapeamento entre os níveis de consistência do MongoDB e o Azure Cosmos DB, leia [níveis de consistência e APIs do Azure Cosmos DB](consistency-levels-across-apis.md). O nível de consistência predefinido é o nível de consistência da sessão. Alterar o nível de consistência é opcional e pode otimizá-lo para a sua aplicação. Para alterar o nível de consistência com o portal do Azure:

1. Vá para o **consistência predefinida** painel em definições.
2. Selecione seu [nível de consistência](consistency-levels.md)

A maioria dos usuários deixe seu nível de consistência na predefinição de consistência de sessão. No entanto, existem [desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Passos Seguintes

* [Ligar uma aplicação MongoDB ao Azure Cosmos DB](connect-mongodb-account.md)
* [Ligar à conta do Azure Cosmos DB, utilizar o Studio 3T](mongodb-mongochef.md)
* [Como distribuir globalmente lê com a API do Azure Cosmos DB do MongoDB](mongodb-readpreference.md)
* [Expirar dados com a API do Azure Cosmos DB para o MongoDB](mongodb-time-to-live.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de pedido no Azure Cosmos DB](request-units.md)





