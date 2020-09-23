---
title: Azure Synapse Link (pré-visualização) para funcionalidades suportadas pelo Azure Cosmos DB
description: Compreenda a lista atual de ações apoiadas pela Azure Synapse Link para Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 336409b8b6f804b224b87d5fb11fded0654b8619
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895529"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure Synapse Link (pré-visualização) para funcionalidades suportadas pelo Azure Cosmos DB

Este artigo descreve as funcionalidades que são atualmente suportadas no Azure Synapse Link para o Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Suporte Azure Synapse

Existem dois tipos de contentores em Azure Cosmos DB:
* Recipiente HTAP - Um recipiente com Ligação Synapse ativado. Este recipiente tem loja transacional e loja analítica. 
* Recipiente OLTP - Um recipiente com Ligação Synaspe não está ativado. Este recipiente tem apenas loja transacional e nenhuma loja analítica.

> [!IMPORTANT]
> A azure Synapse Link for Azure Cosmos DB é atualmente suportado em espaços de trabalho da Synapse que não têm gerido a rede virtual ativada. 

Pode ligar-se a um recipiente DB Azure Cosmos sem permitir a Synapse Link, caso em que só pode ler/escrever na loja transacional. Segue-se a lista das funcionalidades atualmente suportadas no Synapse Link para Azure Cosmos DB. 

| Categoria              | Descrição |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL sem servidor](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Suporte ao tempo de execução** |Suportado tempo de execução Azure Synapse para aceder a Azure Cosmos DB| ✓ | [Contacte-nos](mailto:cosmosdbsynapselink@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB) |
| **Suporte da Azure Cosmos DB API** | Suportado Azure Cosmos DB API tipo | SQL / MongoDB | SQL / MongoDB |
| **Objeto**  |Objetos como uma tabela que pode ser criada, apontando diretamente para o contentor DB Azure Cosmos| Dataframe, Ver, Tabela | Vista |
| **Ler**    | Tipo de recipiente DB Azure Cosmos que pode ser lido | OLTP / HTAP | HTAP  |
| **Escrita**   | O tempo de execução do Azure Synapse pode ser usado para escrever dados num contentor DB Azure Cosmos | Yes | No |

* Se escrever dados num contentor DB Azure Cosmos da Spark, isso acontece através da loja transacional da Azure Cosmos DB e impactará o desempenho das cargas de trabalho transacionais na Azure Cosmos DB e consumirá Unidades de Pedido.
* A integração da piscina Sinaapse SQL através de tabelas externas não é suportada atualmente.

## <a name="supported-code-generated-actions-for-spark"></a>Ações geradas por código para faíscas

| Gesto              | Descrição |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Carregar para DataFrame** |Carregue e leia dados num DataFrame de Faísca |✓| ✓ |
| **Criar mesa spark** |Crie uma tabela que aponte para um recipiente DB Azure Cosmos|✓| ✓ |
| **Escreva DataFrame para o recipiente** |Escrever dados num recipiente|✓| ✓ |
| **Carregar o streaming de dados Do recipiente** |Os dados de fluxo usando o feed de alteração DB do Azure Cosmos|✓| ✓ |
| **Escrever streaming DataFrame para contentor** |Os dados de fluxo usando o feed de alteração DB do Azure Cosmos|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Ações geradas por código suportadas para o sql sem servidor

| Gesto              | Descrição |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Explorar dados** |Explore dados de um recipiente com sintaxe T-SQL familiar e inferência automática de esquemas|X| ✓ |
| **Criar vistas e construir relatórios de BI** |Crie uma vista SQL para ter acesso direto a um recipiente para BI através do sinapse SQL sem servidor |X| ✓ |
| **Junte fontes de dados díspares juntamente com dados da Cosmos DB** | Armazenar os resultados dos dados de leitura de consulta de contentores da Cosmos DB juntamente com dados no Armazenamento Azure Blob ou no Armazenamento do Lago de Dados Azure utilizando o CETAS |X| ✓ |

## <a name="next-steps"></a>Passos seguintes

* Veja como [ligar-se à Synapse Link para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Saiba como consultar a loja de analítica com a Spark](how-to-query-analytical-store-spark.md)
