---
title: O que é Azure Synapse Analytics (anteriormente SQL DW)?
description: A azure Synapse Analytics (anteriormente SQL DW) é um serviço de análise ilimitado que reúne o armazenamento de dados da empresa e a análise de Big Data.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ce4efe0250ea1b85e2e2d77b7ee69bdf3dbcab26
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480400"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>O que é Azure Synapse Analytics (anteriormente SQL DW)?

> [!NOTE]
>Explore a [documentação Azure Synapse (pré-visualização de espaços de trabalho).](../overview-what-is.md)
>

Azure Synapse é um serviço de análise que reúne armazenamento de dados da empresa e análise de Big Data. Dá-lhe a liberdade de consultar dados nos seus termos, através de recursos a pedido ou aprovisionados sem servidor, em escala. O Azure Synapse Analytics junta estes dois mundos com uma experiência unificada para ingerir, preparar, gerir e apresentar dados mais para as suas necessidades imediatas de BI e de machine learning.

Azure Synapse tem quatro componentes:

- Sinapse SQL: Análise completa baseada em T-SQL – Geralmente Disponível
  - Piscina SQL (pagamento por DWU a provisionado)
  - SQL on demand (pagamento por TB processado) (pré-visualização)
- Faísca: Faísca Apache profundamente integrada (pré-visualização)
- Pipelines synapse: Integração de dados híbridos (pré-visualização)
- Estúdio: Experiência unificada do utilizador. (pré-visualização)

## <a name="synapse-sql-pool-in-azure-synapse"></a>Piscina Sinapse SQL em Azure Synapse

O pool SYNAPSE SQL refere-se às funcionalidades de armazenamento de dados da empresa que estão geralmente disponíveis em Azure Synapse.

A piscina SQL representa uma coleção de recursos analíticos que estão sendo a provisionados ao utilizar o SQL da Sinapse. O tamanho da piscina SQL é determinado por Unidades de Armazenagem de Dados (DWU).

Importe big data com consultas simples [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL e, em seguida, use a potência do motor de consulta distribuído para executar análises de alto desempenho. À medida que integra e analisa os dados, o Synapse SQL tornar-se-á a única versão da verdade com que o seu negócio pode contar para insights mais rápidos e robustos. 

## <a name="key-component-of-a-big-data-solution"></a>Componente chave de uma solução de big data

O armazenamento de dados é um componente chave de uma solução de big data baseada na nuvem e de ponta a ponta.

![Solução de armazém de dados](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

Numa solução de dados na cloud, os dados são ingeridos em arquivos de macrodados de várias origens. Assim que estiverem num arquivo de macrodados, o Hadoop, o Spark e os algoritmos de machine learning preparam os dados. Quando os dados estão prontos para análise complexa, o pool Synapse SQL utiliza a PolyBase para consultar as grandes lojas de dados. A PolyBase utiliza consultas T-SQL padrão para trazer os dados para as mesas de bilhar Synapse SQL.

A piscina Sinaapse SQL armazena dados em tabelas relacionais com armazenamento de colunaar. Este formato reduz significativamente os custos de armazenamento de dados e melhora o desempenho das consultas. Quando os dados estiverem armazenados, poderá executar análises em grande escala. Comparativamente aos sistemas de bases de dados tradicionais, as consultas de análise são concluídas em segundos em vez de minutos, ou horas em vez de dias.

Os resultados das análises podem ser enviados para aplicações ou bases de dados de relatórios em todo o mundo. Em seguida, os analistas empresariais podem obter informações para tomar decisões mais adequadas.

## <a name="next-steps"></a>Passos seguintes

- Explore [a arquitetura Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Crie rapidamente [uma piscina SQL](create-data-warehouse-portal.md)
- [Carregar dados de amostras.](load-data-from-azure-blob-storage-using-polybase.md)
- Explore [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Ou olhe para alguns destes outros recursos da Azure Synapse.

- Pesquisar [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Submeter um [Pedido de Recurso](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Crie um bilhete de apoio](sql-data-warehouse-get-started-create-support-ticket.md)
- Pesquisar [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- Fórum [de Fluxo de Pilha de](https://stackoverflow.com/questions/tagged/azure-sqldw) Pesquisa
