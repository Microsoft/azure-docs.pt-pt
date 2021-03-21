---
title: Funcionalidades suportadas do Azure Synapse Link para o Azure Cosmos DB
description: Compreenda a lista atual de ações apoiadas pela Azure Synapse Link para Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: cb2cadadacd914bfa5473b512255c1ab0f856150
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666313"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Funcionalidades suportadas do Azure Synapse Link para o Azure Cosmos DB

Este artigo descreve as funcionalidades que são atualmente suportadas no Azure Synapse Link para o Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Suporte Azure Synapse

Existem dois tipos de contentores em Azure Cosmos DB:
* Recipiente HTAP - Um recipiente com Ligação Synapse ativado. Este recipiente tem loja transacional e loja analítica. 
* Recipiente OLTP - Um recipiente com Ligação Synaspe não está ativado. Este recipiente tem apenas loja transacional e nenhuma loja analítica.

> [!IMPORTANT]
> A azure Synapse Link for Azure Cosmos DB é atualmente suportado em espaços de trabalho da Synapse que não têm gerido a rede virtual ativada. 

Pode ligar-se a um recipiente DB Azure Cosmos sem permitir a Ligação Synapse. Neste cenário, só pode ler/escrever na loja transacional. Segue-se uma lista das funcionalidades atualmente suportadas no Synapse Link para Azure Cosmos DB. 

| Categoria              | Descrição |[Piscina de faíscas Apache](../sql/on-demand-workspace-overview.md) | [Piscina SQL sem servidor](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Suporte ao tempo de execução** |Suportado tempo de execução Azure Synapse para aceder a Azure Cosmos DB| ✓ | ✓ |
| **Suporte da Azure Cosmos DB API** | Suportado Azure Cosmos DB API tipo | SQL / MongoDB | SQL / MongoDB |
| **Objeto**  |Objetos como uma tabela que pode ser criada, apontando diretamente para o contentor DB Azure Cosmos| Dataframe, Ver, Tabela | Vista |
| **Ler**    | Tipo de recipiente DB Azure Cosmos que pode ser lido | OLTP / HTAP | HTAP  |
| **Escrever**   | O tempo de execução do Azure Synapse pode ser usado para escrever dados num contentor DB Azure Cosmos | Yes | No |

* Se escrever dados num contentor DB Azure Cosmos da Spark, este processo acontece através da loja transacional da Azure Cosmos DB. Irá impactar o desempenho transacional da Azure Cosmos DB consumindo Unidades de Pedido.
* A integração dedicada da piscina SQL através de mesas externas não é suportada atualmente.
 
## <a name="supported-code-generated-actions-for-spark"></a>Ações geradas por código para faíscas

| Gesto              | Description |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Carregar para DataFrame** |Carregue e leia dados num DataFrame de Faísca |✓| ✓ |
| **Criar mesa spark** |Crie uma tabela que aponte para um recipiente DB Azure Cosmos|✓| ✓ |
| **Escreva DataFrame para o recipiente** |Escrever dados num recipiente|✓| ✓ |
| **Carregar o streaming de dados Do recipiente** |Os dados de fluxo usando o feed de alteração DB do Azure Cosmos|✓| ✓ |
| **Escrever streaming DataFrame para contentor** |Os dados de fluxo usando o feed de alteração DB do Azure Cosmos|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Ações geradas por código suportadas para piscina SQL sem servidor

| Gesto              | Description |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Explorar dados** |Explore dados de um recipiente com sintaxe T-SQL familiar e inferência automática de esquemas|X| ✓ |
| **Criar vistas e construir relatórios de BI** |Crie uma vista SQL para ter acesso direto a um recipiente para BI através de piscina SQL sem servidor |X| ✓ |
| **Junte fontes de dados díspares juntamente com dados da Cosmos DB** | Armazenar os resultados dos dados de leitura de consulta de contentores da Cosmos DB juntamente com dados no Armazenamento Azure Blob ou no Armazenamento do Lago de Dados Azure utilizando o CETAS |X| ✓ |

## <a name="next-steps"></a>Passos seguintes

* Veja como [ligar-se à Synapse Link para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Saiba como consultar a Cosmos DB Analytical Store com Faísca](how-to-query-analytical-store-spark.md)