---
title: Azure Synapse Link (pré-visualização) para funcionalidades suportadas pelo Azure Cosmos DB
description: Compreenda a lista atual de ações apoiadas pela Azure Synapse Link para Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: c9f6a61dfa688980fa6400a2fa93ab8862798750
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194778"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure Synapse Link (pré-visualização) para funcionalidades suportadas pelo Azure Cosmos DB

Este artigo descreve as funcionalidades que são atualmente suportadas no Azure Synapse Link para o Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Suporte Azure Synapse

Existem dois tipos de contentores em Azure Cosmos DB:
* Recipiente HTAP - Um recipiente com Ligação Synapse ativado. Este recipiente tem loja transacional e loja analítica. 
* Recipiente OLTP - Um recipiente com apenas loja de transações; A Ligação Synapse não está ativada. 

> [!IMPORTANT]
> A Azure Synapse Link for Azure Cosmos DB é atualmente suportado para espaços de trabalho que não têm gerido rede virtual ativada. 

Pode ligar-se a um recipiente DB Azure Cosmos sem permitir a Synapse Link, caso em que só pode ler/escrever na loja transacional. Segue-se uma lista das funcionalidades atualmente suportadas no Synapse Link para Azure Cosmos DB. 

| Categoria              | Description |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL sem servidor](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Suporte ao tempo de execução** |Suporte para leitura ou escrita por Azure Synapse tempo de execução| ✓ | [Contacte-nos](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Suporte da Azure Cosmos DB API** |Suporte da API como link sinapse| SQL / MongoDB | SQL / MongoDB |
| **Objeto**  |Objetos como uma tabela que pode ser criada, apontando diretamente para o contentor DB Azure Cosmos| Vista, Tabela | Vista |
| **Leitura**    |Leia os dados de um contentor DB Azure Cosmos| OLTP / HTAP | HTAP  |
| **Escrita**   |Escreva dados do tempo de execução para um contentor DB Azure Cosmos| OLTP | n/a |

* Se escrever dados num contentor DB Azure Cosmos da Spark, este processo acontece através da loja transacional da Azure Cosmos DB e impactará o desempenho transacional da Azure Cosmos DB consumindo Unidades de Pedido.
* A integração da piscina SQL através de mesas externas não é suportada atualmente.

## <a name="supported-code-generated-actions-for-spark"></a>Ações geradas por código para faíscas

| Gesto              | Description |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Carregar para DataFrame** |Carregue e leia dados num DataFrame de Faísca |X| ✓ |
| **Criar mesa spark** |Crie uma tabela que aponte para um recipiente DB Azure Cosmos|X| ✓ |
| **Escreva DataFrame para o recipiente** |Escrever dados num recipiente|✓| ✓ |
| **Carregar o streaming de dados Do recipiente** |Os dados de fluxo usando o feed de alteração DB do Azure Cosmos|✓| ✓ |
| **Escrever streaming DataFrame para contentor** |Os dados de fluxo usando o feed de alteração DB do Azure Cosmos|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Ações geradas por código suportadas para o sql sem servidor

| Gesto              | Description |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Selecione top 100** |Pré-visualização top 100 itens de um recipiente|X| ✓ |
| **Criar vista** |Crie uma vista para ter acesso diretamente ao BI num contentor através do SQL synapse|X| ✓ |

## <a name="next-steps"></a>Passos seguintes

* Veja como [ligar-se à Synapse Link para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Saiba como consultar a loja de analítica com a Spark](how-to-query-analytical-store-spark.md)