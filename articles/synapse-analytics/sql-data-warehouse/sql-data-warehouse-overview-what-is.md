---
title: O que é Azure Synapse Analytics (anteriormente SQL DW)?
description: Azure Synapse Analytics (anteriormente SQL DW) é um serviço de análise ilimitado que reúne o armazenamento de dados da empresa e a análise de Big Data.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 3363c4f5828f412bd11ca57e3c5f9013910a8055
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586450"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>O que é Azure Synapse Analytics (anteriormente SQL DW)?

O Azure Synapse é um serviço de análise ilimitado que junta o armazenamento de dados empresariais e a análise de macrodados. Dá-lhe a liberdade de consultar dados nos seus termos, através de recursos a pedido ou aprovisionados sem servidor, em escala. Azure Synapse reúne estes dois mundos com uma experiência unificada para ingerir, preparar, gerir e servir dados para as necessidades imediatas de BI e machine learning

Azure Synapse tem quatro componentes:
- Synapse SQL: Análise completa baseada em T-SQL – Geralmente Disponível
    - Piscina SQL (pagamento por DWU provisionado) 
    - SQL on-demand (pagamento por TB processado) – (Pré-visualização)
- Faísca: Faísca Apache profundamente integrada (Pré-visualização) 
- Integração de Dados: Integração de dados híbridos (Pré-visualização)
- Estúdio: Experiência unificada do utilizador.  (Pré-visualização)

> [!NOTE]
> Para aceder às funcionalidades de pré-visualização do Azure Synapse, solicite acesso [aqui.](https://aka.ms/synapsepreview) A Microsoft triagemtodos os pedidos e responderá o mais rapidamente possível.

## <a name="synapse-sql-pool-in-azure-synapse"></a>Piscina SQL synapse em Azure Synapse

O pool SQL da Synapse refere-se às funcionalidades de armazenamento de dados da empresa que estão geralmente disponíveis em Azure Synapse. 

A piscina SQL representa uma coleção de recursos analíticos que estão a ser provisionados ao utilizar o SYnapse SQL. O tamanho da piscina SQL é determinado por Unidades de Armazenamento de Dados (DWU).

Importar big data com consultas simples [polyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL e, em seguida, usar o poder de MPP para executar análises de alto desempenho. À medida que se integra e analisa, o pool SQL da Synapse tornar-se-á a única versão da verdade com que o seu negócio pode contar para insights mais rápidos e robustos.  

## <a name="key-component-of-a-big-data-solution"></a>Componente chave de uma solução de big data

O armazenamento de dados é um componente chave de uma solução de big data baseada em nuvem.

![Solução de armazém de dados](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Numa solução de dados da cloud, os dados são ingeridos em arquivos de macrodados a partir de várias origens. Uma vez num arquivo de macrodados, os algoritmos do Hadoop, do Spark e de machine learning preparam os dados. Quando os dados estão prontos para análise complexa, o pool SQL synapse usa a PolyBase para consultar as grandes lojas de dados. A PolyBase utiliza consultas Padrão T-SQL para trazer os dados para mesas de bilhar Synapse SQL.
 
Piscina Synapse SQL armazena dados em tabelas relacionais com armazenamento colunaar. Este formato reduz significativamente os custos de armazenamento de dados e melhora o desempenho das consultas. Quando os dados estiverem armazenados, poderá executar análises em grande escala. Em comparação com os sistemas de bases de dados tradicionais, as consultas de análise terminam em segundos em vez de minutos ou horas em vez de dias. 

Os resultados das análises podem ser enviados para aplicações ou bases de dados de relatórios em todo o mundo. Em seguida, os analistas empresariais podem obter informações para tomar decisões mais adequadas.

## <a name="next-steps"></a>Passos seguintes

- Explore [a arquitetura Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Crie rapidamente [uma piscina SQL](create-data-warehouse-portal.md)
- [Carregar dados da amostra](load-data-from-azure-blob-storage-using-polybase.md).
- Explore [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Ou olhe para alguns destes outros recursos Azure Synapse.  
* Blogs [de](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) pesquisa
* Enviar um [pedido de Recurso](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Criar um bilhete de apoio](sql-data-warehouse-get-started-create-support-ticket.md)
* Pesquisar [fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Fórum [de transbordo de pilha de pesquisa](https://stackoverflow.com/questions/tagged/azure-sqldw)
