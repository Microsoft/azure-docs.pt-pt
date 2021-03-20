---
title: O que é a piscina SQL dedicada (anteriormente SQL DW)?
description: Piscina de SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics é a funcionalidade de armazenamento de dados da empresa em Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9077ce35065b1bf45646496cc4c43d6def82d958
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685230"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>O que é a piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics?

O Azure Synapse Analytics é um serviço de análise que combina o armazenamento de dados empresariais e a análise de Macrodados. Pool DE SQL dedicado (anteriormente SQL DW) refere-se às funcionalidades de armazenamento de dados da empresa que estão disponíveis no Azure Synapse Analytics.



![Piscina SQL dedicada (anteriormente SQL DW) em relação a Azure Synapse](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



Piscina DE SQL dedicada (anteriormente SQL DW) representa uma coleção de recursos analíticos que são a provisionados ao utilizar o SQL synapse. O tamanho de uma piscina SQL dedicada (anteriormente SQL DW) é determinado por Unidades de Armazenamento de Dados (DWU).

Uma vez criado o seu pool DE SQL dedicado, você pode importar big data com consultas simples [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL, e, em seguida, usar a potência do motor de consulta distribuído para executar análises de alto desempenho. À medida que integra e analisa os dados, o pool de SQL dedicado (anteriormente SQL DW) tornar-se-á a única versão da verdade com que o seu negócio pode contar para insights mais rápidos e robustos.

> [!NOTE]
>Explore a [documentação Azure Synapse Analytics](../overview-what-is.md).
> 

## <a name="key-component-of-a-big-data-solution"></a>Componente chave de uma solução de big data

O armazenamento de dados é um componente chave de uma solução de big data baseada na nuvem e de ponta a ponta.

![Solução de armazém de dados](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

Numa solução de dados na cloud, os dados são ingeridos em arquivos de macrodados de várias origens. Assim que estiverem num arquivo de macrodados, o Hadoop, o Spark e os algoritmos de machine learning preparam os dados. Quando os dados estão prontos para análise complexa, o pool de SQL dedicado utiliza a PolyBase para consultar as grandes lojas de dados. A PolyBase utiliza consultas padrão T-SQL para trazer os dados para mesas dedicadas de piscina SQL (anteriormente SQL DW).

Piscina SQL dedicada (anteriormente SQL DW) armazena dados em tabelas relacionais com armazenamento colunar. Este formato reduz significativamente os custos de armazenamento de dados e melhora o desempenho das consultas. Quando os dados estiverem armazenados, poderá executar análises em grande escala. Comparativamente aos sistemas de bases de dados tradicionais, as consultas de análise são concluídas em segundos em vez de minutos, ou horas em vez de dias.

Os resultados das análises podem ser enviados para aplicações ou bases de dados de relatórios em todo o mundo. Em seguida, os analistas empresariais podem obter informações para tomar decisões mais adequadas.

## <a name="next-steps"></a>Passos seguintes

- Explore [a arquitetura Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Crie rapidamente [uma piscina SQL dedicada](create-data-warehouse-portal.md)
- [Carregar dados de amostras.](./load-data-from-azure-blob-storage-using-copy.md)
- Explore [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Ou olhe para alguns destes outros recursos da Azure Synapse.

- Pesquisar [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Submeter um [Pedido de Recurso](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Crie um bilhete de apoio](sql-data-warehouse-get-started-create-support-ticket.md)
- Pesquisar [Microsoft Q&Uma página de perguntas](/answers/topics/azure-synapse-analytics.html)
- Fórum [de Fluxo de Pilha de](https://stackoverflow.com/questions/tagged/azure-sqldw) Pesquisa