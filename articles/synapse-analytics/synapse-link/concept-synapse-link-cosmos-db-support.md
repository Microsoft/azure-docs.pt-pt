---
title: Azure Synapse Link para Azure Cosmos DB suportado
description: Conheça a lista atual de ações apoiadas por Azure Synapse Link for Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 1b94d7677026f3695d07be4d83a5059373078c2e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599950"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Azure Synapse Link para Azure Cosmos DB suportado

Este artigo descreve as funcionalidades que são atualmente suportadas no Azure Synapse Link for Azure Cosmos DB. 

## <a name="azure-synapse-support"></a>Suporte azure Synapse

Existem dois tipos de contentores em Azure Cosmos DB:
* Recipiente HTAP - Um recipiente com Ligação Synapse ativado. Este contentor tem loja transacional e loja analítica. 
* Recipiente OLTP - Um contentor com apenas um armazém de transações; Synapse Link não está ativado. 

Pode ligar-se a um recipiente Azure Cosmos DB sem permitir a Ligação Synapse, caso em que só pode ler/escrever na loja transacional. Segue-se uma lista das funcionalidades atualmente suportadas dentro da Ligação Synapse para O Azure Cosmos DB. 

| Categoria              | Descrição |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL sem servidor](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Suporte a tempo de execução** |Suporte para leitura ou escrita por Azure Synapse tempo de execução| ✓ | [Contacte-nos](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Suporte da API da Azure Cosmos DB** |Suporte da API como uma Ligação Synapse| SQL / MongoDB | SQL / MongoDB |
| **Objeto**  |Objetos como uma mesa que pode ser criado, apontando diretamente para o recipiente Azure Cosmos DB| Vista, Tabela | Vista |
| **Leitura**    |Leia os dados de um contentor Da BD Azure Cosmos| OLTP / HTAP | HTAP  |
| **Escrita**   |Escreva dados do tempo de execução num recipiente Azure Cosmos DB| OLTP | n/a |

* Se escrever dados num contentor Azure Cosmos DB da Spark, este processo acontece através da loja transacional da Azure Cosmos DB e terá impacto no desempenho transacional da Azure Cosmos DB consumindo Unidades de Pedido.
* Atualmente, a integração do pool SQL através de tabelas externas não é suportada.

## <a name="supported-code-generated-actions-for-spark"></a>Ações geradas por código suportadas para a Spark

| Gesto              | Descrição |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Carregar para DataFrame** |Carregue e leia dados num Spark DataFrame |X| ✓ |
| **Criar mesa de faísca** |Crie uma tabela apontando para um recipiente Azure Cosmos DB|X| ✓ |
| **Escreva DataFrame para recipiente** |Escreva dados num recipiente|✓| ✓ |
| **Fluxo de carga DataFrame do recipiente** |Dados de streaming usando feed de mudança DB Azure Cosmos|✓| ✓ |
| **Escreva streaming DataFrame para recipiente** |Dados de streaming usando feed de mudança DB Azure Cosmos|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Ações geradas por código suportadas para sem servidor SQL

| Gesto              | Descrição |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Selecione top 100** |Pré-visualizar os 100 itens de um recipiente|X| ✓ |
| **Criar vista** |Crie uma vista para ter diretamente acesso bi em um recipiente através do Synapse SQL|X| ✓ |

## <a name="next-steps"></a>Passos seguintes

* Veja como [ligar-se ao Synapse Link para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Saiba como consultar a loja analítica com a Spark](how-to-query-analytical-store-spark.md)