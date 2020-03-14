---
title: O que é Azure Synapse Analytics (anteriormente SQL DW)?
description: Azure Synapse Analytics (anteriormente SQL DW) é um serviço de análise ilimitado que reúne o armazenamento de dados da empresa e a análise de Big Data.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240577"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>O que é Azure Synapse Analytics (anteriormente SQL DW)?

O Azure Synapse é um serviço de análise ilimitado que junta o armazenamento de dados empresariais e a análise de macrodados. Dá-lhe a liberdade de consultar dados nos seus termos, através de recursos a pedido ou aprovisionados sem servidor, em escala. Azure Synapse reúne estes dois mundos com uma experiência unificada para ingerir, preparar, gerir e servir dados para as necessidades imediatas de BI e machine learning

Azure Synapse tem quatro componentes:
- SQL Analytics: Análise completa baseada em T-SQL – Geralmente Disponível
    - Piscina SQL (pagamento por DWU provisionado) 
    - SQL on-demand (pagamento por TB processado) – (Pré-visualização)
- Faísca: Faísca Apache profundamente integrada (Pré-visualização) 
- Integração de Dados: Integração de dados híbridos (Pré-visualização)
- Estúdio: Experiência unificada do utilizador.  (Pré-visualização)

> [!NOTE]
> Para aceder às funcionalidades de pré-visualização do Azure Synapse, solicite acesso [aqui.](https://aka.ms/synapsepreview) A Microsoft triagemtodos os pedidos e responderá o mais rapidamente possível.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics e piscina SQL em Azure Synapse

O SQL Analytics refere-se às funcionalidades de armazenamento de dados da empresa que estão geralmente disponíveis no Azure Synapse. 

A piscina SQL representa uma coleção de recursos analíticos que estão a ser provisionados ao utilizar o SQL Analytics. O tamanho da piscina SQL é determinado por Unidades de Armazenamento de Dados (DWU).

Importar big data com consultas simples [polyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL e, em seguida, usar o poder de MPP para executar análises de alto desempenho. À medida que se integra e analisa, o SQL Analytics tornar-se-á a versão única da verdade com que o seu negócio pode contar para insights mais rápidos e robustos.  

## <a name="key-component-of-a-big-data-solution"></a>Componente chave de uma solução de big data

O armazenamento de dados é um componente chave de uma solução de big data baseada em nuvem.

![Solução de armazém de dados](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Numa solução de dados da cloud, os dados são ingeridos em arquivos de macrodados a partir de várias origens. Uma vez num arquivo de macrodados, os algoritmos do Hadoop, do Spark e de machine learning preparam os dados. Quando os dados estão prontos para análise complexa, o SQL Analytics utiliza a PolyBase para consultar as grandes lojas de dados. A PolyBase utiliza consultas T-SQL padrão para introduzir os dados em tabelas SQL Analytics.
 
A SQL Analytics armazena dados em tabelas relacionais com armazenamento colunar. Este formato reduz significativamente os custos de armazenamento de dados e melhora o desempenho das consultas. Uma vez armazenados os dados, pode executar analítica em larga escala. Em comparação com os sistemas de bases de dados tradicionais, as consultas de análise terminam em segundos em vez de minutos ou horas em vez de dias. 

Os resultados das análises podem ser enviados para aplicações ou bases de dados de relatórios em todo o mundo. Em seguida, os analistas empresariais podem obter informações para tomar decisões mais adequadas.

## <a name="next-steps"></a>Passos Seguintes

- Explore [a arquitetura Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Crie rapidamente [uma piscina SQL](create-data-warehouse-portal.md)
- [Carregar dados da amostra](sql-data-warehouse-load-sample-databases.md).
- Explore [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Ou olhe para alguns destes outros recursos Azure Synapse.  
* Blogs [de](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) pesquisa
* Enviar um [pedido de Recurso](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Criar um pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
* Pesquisar [fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Fórum [de transbordo de pilha de pesquisa](https://stackoverflow.com/questions/tagged/azure-sqldw)