---
title: O que é o Azure SQL Data Warehouse? | Microsoft Docs
description: Nível empresarial base de dados distribuída que pode processar volumes de petabytes de dados relacionais e não relacionais. É o primeiro armazém do setor na cloud dados com a capacidade de aumentar, diminuir e colocar em pausa em segundos.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428025"
---
# <a name="what-is-azure-sql-data-warehouse"></a>O que é o Azure SQL Data Warehouse?

SQL Data Warehouse é um com base na cloud empresarial armazém de dados (EDW) que utiliza em grande escala paralela de processamento (MPP) para executar rapidamente consultas complexas em petabytes de dados. Utilize o SQL Data Warehouse como um componente fundamental de uma solução de macrodados. Importe grandes volumes de dados para o SQL Data Warehouse com simples [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) consultas de T-SQL e, em seguida, utilize o poder do MPP para executar análises de elevado desempenho. À medida que integra e analisa, o armazém de dados tornar-se-á a única versão real com que a sua empresa pode contar para obter informações.  

## <a name="key-component-of-big-data-solution"></a>Componente fundamental da solução de macrodados

O SQL Data Warehouse é um componente fundamental de uma solução de macrodados ponto a ponto na cloud.

![Solução de armazém de dados](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Numa solução de dados da cloud, os dados são ingeridos em arquivos de macrodados a partir de várias origens. Uma vez num arquivo de macrodados, os algoritmos do Hadoop, do Spark e de machine learning preparam os dados. Quando os dados estão prontos para análises complexas, o SQL Data Warehouse utiliza o PolyBase para consultar os arquivos de macrodados. O PolyBase utiliza consultas de T-SQL padrão para colocar os dados no SQL Data Warehouse.
 
O SQL Data Warehouse armazena os dados em tabelas relacionais através do armazenamento em colunas. Este formato reduz significativamente os custos de armazenamento de dados e melhora o desempenho das consultas. Depois de os dados serem armazenados no SQL Data Warehouse, pode executar análises em grande escala. Em comparação com os sistemas de bases de dados tradicionais, as consultas de análise terminam em segundos em vez de minutos ou horas em vez de dias. 

Os resultados das análises podem ser enviados para aplicações ou bases de dados de relatórios em todo o mundo. Em seguida, os analistas empresariais podem obter informações para tomar decisões mais adequadas.

## <a name="next-steps"></a>Passos Seguintes

- Explore [arquitetura do Azure SQL Data Warehouse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Rapidamente [criar um SQL Data Warehouse][create a SQL Data Warehouse]
- [Carregar dados de exemplo][load sample data].
- Explore [vídeos](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  
* Pesquisa [Blogues]
* Submeter um [pedidos de funcionalidades]
* Pesquisa [Blogues da Equipa Customer Advisory]
* [Criar um pedido de suporte]
* Pesquisa [fórum do MSDN]
* Pesquisa [fórum do Stack Overflow]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar um pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
