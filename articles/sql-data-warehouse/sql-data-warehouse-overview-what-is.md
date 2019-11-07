---
title: O que é o Azure Synapse Analytics (anteriormente conhecido como SQL DW)?
description: O Azure Synapse Analytics (anteriormente conhecido como SQL DW) é um serviço de análise ilimitado que reúne conjunto de dados corporativos e análise de Big Data.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645520"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>O que é o Azure Synapse Analytics (anteriormente conhecido como SQL DW)?

O Azure Synapse é um serviço de análise ilimitado que reúne conjunto de dados corporativos e análise de Big Data. Ele oferece a você a liberdade de consultar dados sobre seus termos, usando recursos sem servidor sob demanda ou provisionados, em escala. O Azure Synapse traz esses dois mundos junto com uma experiência unificada para ingerir, preparar, gerenciar e fornecer dados para necessidades imediatas de BI e aprendizado de máquina

O Azure Synapse tem quatro componentes:
- Análise de SQL: análise baseada em T-SQL completa – disponibilidade geral
    - Pool do SQL (pague por DWU provisionado) 
    - SQL sob demanda (pagamento por TB processado) – (versão prévia)
- Spark: Apache Spark profundamente integrados (versão prévia) 
- Integração de dados: integração de dados híbridas (versão prévia)
- Studio: experiência do usuário unificada.  (Pré-visualização)

> [!NOTE]
> Para acessar os recursos de visualização do Azure Synapse, solicite o acesso [aqui](https://aka.ms/synapsepreview). A Microsoft fará a triagem de todas as solicitações e responderá assim que possível.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Análise de SQL e pool de SQL no Azure Synapse

A análise de SQL refere-se aos recursos corporativos de data warehousing que estão geralmente disponíveis com o Azure Synapse. 

O pool do SQL representa uma coleção de recursos analíticos que estão sendo provisionados ao usar a análise do SQL. O tamanho do pool do SQL é determinado pelas DWU (unidades de data warehouse).

Importe Big Data com consultas T-SQL simples do [polybase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) e, em seguida, use o poder do MPP para executar análises de alto desempenho. À medida que você se integra e analisa, a análise do SQL se tornará a única versão da verdade que sua empresa pode contar para obter informações mais rápidas e robustas.  

## <a name="key-component-of-a-big-data-solution"></a>Componente-chave de uma solução de Big Data

O data warehousing é um componente fundamental de uma solução de Big Data de ponta a ponta baseada em nuvem.

![Solução de armazém de dados](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Numa solução de dados da cloud, os dados são ingeridos em arquivos de macrodados a partir de várias origens. Uma vez num arquivo de macrodados, os algoritmos do Hadoop, do Spark e de machine learning preparam os dados. Quando os dados estiverem prontos para análise complexa, o SQL Analytics usará o polybase para consultar os repositórios de Big Data. O polybase usa consultas T-SQL padrão para colocar os dados em tabelas de análise de SQL.
 
A análise do SQL armazena dados em tabelas relacionais com armazenamento de coluna. Este formato reduz significativamente os custos de armazenamento de dados e melhora o desempenho das consultas. Depois que os dados são armazenados, você pode executar a análise em grande escala. Em comparação com os sistemas de bases de dados tradicionais, as consultas de análise terminam em segundos em vez de minutos ou horas em vez de dias. 

Os resultados das análises podem ser enviados para aplicações ou bases de dados de relatórios em todo o mundo. Em seguida, os analistas empresariais podem obter informações para tomar decisões mais adequadas.

## <a name="next-steps"></a>Passos seguintes

- Explore a [arquitetura do Azure Synapse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Criar rapidamente um pool do SQL](create-data-warehouse-portal.md)
- [Carregar dados de exemplo][load sample data].
- Explorar [vídeos](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Ou então, veja alguns desses outros recursos do Azure Synapse.  
* Pesquisar [Blogues]
* Enviar [Pedidos de funcionalidades]
* Pesquisar [Blogues da Equipa Customer Advisory]
* [Criar um pedido de suporte]
* Pesquisar no [Fórum do MSDN]
* Pesquisar [Fórum do Stack Overflow]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar um pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
