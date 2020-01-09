---
title: Etapas de otimização após a migração com a API do Azure Cosmos DB para MongoDB
description: Este documento fornece as técnicas de otimização pós-migração do MongoDB para a APi de Azure Cosmos DB para o Mongo DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c521546bedf1ebfd42bce4c50aa79b199553fd5a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441554"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de otimização após a migração ao usar a API do Azure Cosmos DB para MongoDB 

Depois de migrar os dados armazenados no banco de dados MongoDB para a API do Azure Cosmos DB para MongoDB, você pode se conectar a Azure Cosmos DB e gerenciar os dados. Este guia fornece as etapas que você deve considerar após a migração. Consulte o [tutorial migrar o MongoDB para a API do Azure Cosmos DB para MongoDB](../dms/tutorial-mongodb-cosmos-db.md) para as etapas de migração.

Neste guia, vai aprender a:
- [Conectar seu aplicativo](#connect-account)
- [Otimizar a política de indexação](#indexing)
- [Configurar a distribuição global para a API do Azure Cosmos DB para MongoDB](#distribute-data)
- [Definir nível de consistência](#consistency)

> [!NOTE]
> A única etapa obrigatória de pós-atualização no nível do aplicativo é alterar a cadeia de conexão em seu aplicativo para apontar para sua nova conta de Azure Cosmos DB. Todas as outras etapas de migração são otimizações recomendadas.
>

## <a id="connect-account"></a>Conectar seu aplicativo 

1. Em uma nova janela, entre no [portal do Azure](https://www.portal.azure.com/)
2. No [portal do Azure](https://www.portal.azure.com/), no painel esquerdo, abra o menu **todos os recursos** e localize a conta de Azure Cosmos DB para a qual você migrou seus dados.
3. Abra a folha **cadeia de conexão** . O painel do lado direito contém todas as informações de que precisa para se ligar com êxito à sua conta.
4. Use as informações de conexão na configuração do aplicativo (ou em outros locais relevantes) para refletir a API do Azure Cosmos DB para a conexão do MongoDB em seu aplicativo. 
![cadeia de conexão](./media/mongodb-post-migration/connection-string.png)

Para obter mais detalhes, consulte a página [conectar um aplicativo MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) .

## <a id="indexing"></a>Otimizar a política de indexação

Todos os campos de dados são indexados automaticamente, por padrão, durante a migração de dados para Azure Cosmos DB. Em muitos casos, essa política de indexação padrão é aceitável. Em geral, a remoção de índices otimiza as solicitações de gravação e ter a política de indexação padrão (ou seja, a indexação automática) otimiza as solicitações de leitura.

Para obter mais informações sobre indexação, consulte [indexação de dados na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md) , bem como a [indexação em artigos Azure Cosmos DB](index-overview.md) .

## <a id="distribute-data"></a>Distribua seus dados globalmente

O Azure Cosmos DB está disponível em todas as [regiões do Azure](https://azure.microsoft.com/regions/#services) em todo o mundo. Depois de selecionar o nível de consistência padrão para sua conta de Azure Cosmos DB, você pode associar uma ou mais regiões do Azure (dependendo das suas necessidades de distribuição global). Para alta disponibilidade e continuidade dos negócios, sempre recomendamos a execução em pelo menos duas regiões. Você pode examinar as dicas para [otimizar o custo de implantações de várias regiões no Azure Cosmos DB](optimize-cost-regions.md).

Para distribuir seus dados globalmente, consulte [distribuir dados globalmente na API do Azure Cosmos DB para o MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Definir nível de consistência
O Azure Cosmos DB oferece 5 níveis de [consistência](consistency-levels.md)bem definidos. Para ler sobre o mapeamento entre os níveis de consistência do MongoDB e do Azure Cosmos DB, leia [níveis de consistência e APIs de Azure Cosmos DB](consistency-levels-across-apis.md). O nível de consistência padrão é o nível de consistência da sessão. Alterar o nível de consistência é opcional e você pode otimizá-lo para seu aplicativo. Para alterar o nível de consistência usando portal do Azure:

1. Vá para a folha **consistência padrão** em configurações.
2. Selecione seu [nível de consistência](consistency-levels.md)

A maioria dos usuários deixa seu nível de consistência na configuração de consistência da sessão padrão. No entanto, há [compensações de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Passos seguintes

* [Ligar uma aplicação MongoDB ao Azure Cosmos DB](connect-mongodb-account.md)
* [Conectar-se à conta do Azure Cosmos DB usando o Studio 3T](mongodb-mongochef.md)
* [Como distribuir globalmente as leituras usando a API do Azure Cosmos DB para MongoDB](mongodb-readpreference.md)
* [Dados de expiração com a API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Request Units in Azure Cosmos DB](request-units.md) (Unidades de Pedido no Azure Cosmos DB)





