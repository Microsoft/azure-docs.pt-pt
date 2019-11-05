---
title: Práticas recomendadas de desenvolvimento para o Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações e melhores práticas que deve saber quando desenvolve soluções para o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2debe9000cb60d4dea7395181dfb783fe26586f1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499849"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Práticas recomendadas de desenvolvimento para o Azure SQL Data Warehouse
Este artigo descreve as diretrizes e as práticas recomendadas à medida que você desenvolve sua solução de data warehouse. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajustar o desempenho de consultas com novos aprimoramentos de produtos  
- [Ajuste de desempenho com exibições materializadas](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Ajuste de desempenho com o índice columnstore clusterizado ordenado](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Ajuste de desempenho com cache de conjunto de resultados](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento
Para obter mais informações sobre a redução de custos através da colocação em pausa e do dimensionamento, veja [Gerir a computação](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Manter as estatísticas
Os SQL Data Warehouse do Azure podem ser configurados para detectar e criar estatísticas automaticamente em colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  Recomendamos que você habilite o AUTO_CREATE_STATISTICS para seus bancos de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas nas colunas usadas em suas consultas estejam sempre atualizadas. 

Se você achar que está demorando muito para atualizar todas as suas estatísticas, convém tentar ser mais seletivos sobre quais colunas precisam de atualizações de estatísticas frequentes. Por exemplo, deve atualizar as colunas de data, onde podem ser adicionados diariamente novos valores. **Você obterá mais benefícios com a atualização de estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.**

Consulte também [Gerir estatísticas de tabela][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash
Por predefinição, as tabelas são distribuídas por Round Robin.  Esse design facilita para os usuários começarem a criar tabelas sem a necessidade de decidir como suas tabelas devem ser distribuídas.  As tabelas Round Robin podem ter um desempenho suficiente para algumas cargas de trabalho, mas, na maioria dos casos, selecionar uma coluna de distribuição irá proporcionar um desempenho muito melhor.  O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  Por exemplo, se tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando associa a tabela de pedidos à tabela de transações em order_id, esta consulta torna-se uma consulta pass-through, o que significa que podemos eliminar operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.  Essa explicação aborda apenas a superfície. Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  Consulte os links abaixo para obter muito mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como definir uma tabela distribuída na cláusula WITH da sua instrução CREATE TABLEs.

Consulte também [Descrição geral da tabela][Table overview], [Distribuição da tabela][Table distribution], [Selecionar distribuição da tabela][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Não crie partições em demasia
Embora o particionamento de dados possa ser eficaz para manter seus dados por meio da alternância de partição ou otimização de verificações com a eliminação de partição, ter muitas partições pode retardar suas consultas.  Geralmente, uma estratégia de particionamento de alta granularidade que pode funcionar bem em SQL Server pode não funcionar bem no SQL Data Warehouse.  Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas.  Tenha em atenção que, em segundo plano, o SQL Data Warehouse particiona os dados em 60 bases de dados, pelo que, se criar uma tabela com 100 partições, resulta efetivamente em 6000 partições.  Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  Considere menos granularidade do que a que poderá ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Consulte também [Criação de partições de tabela][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação
As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  Por exemplo, se você tiver uma inserção, que você espera levar 1 hora, se possível, divida a inserção em quatro partes, que será executada em 15 minutos.  Tire partido dos casos especiais de Registo Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para esvaziar tabelas, de forma a reduzir o risco de reversão.  Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  Para tabelas sem partição considere utilizar um CTAS para escrever os dados que pretende manter numa tabela, em vez de utilizar DELETE.  Se um CTAS levar o mesmo período de tempo, será uma operação muito mais segura para ser executada, pois ele tem o registro em log de transações mínimo e poderá ser cancelado rapidamente, se necessário.

Consulte também [Compreender as transações][Understanding transactions], [Otimizar as transações][Optimizing transactions], [Criação de partições de tabela][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Criar tabela ao selecionar (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna
Ao definir o DDL, usar o menor tipo de dados que dará suporte aos seus dados melhorará o desempenho da consulta.  Isto é especialmente importante para colunas CHAR e VARCHAR.  Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Consulte também [Descrição geral da tabela][Table overview], [Tipos de dados de tabela][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster
Os índices columnstore em cluster são uma das formas mais eficientes de armazenar os seus dados no SQL Data Warehouse.  Por predefinição, as tabelas no SQL Data Warehouse são criadas como ColumnStore em Cluster.  Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  Consulte as [Causas de fraca qualidade do índice columnstore][Causes of poor columnstore index quality] no artigo [Índices de tabela][Table indexes], para obter instruções passo a passo sobre como detetar e melhorar a qualidade do segmento para tabelas columnstore em cluster.  Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos médio ou grande para carregar dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) inferiores significa que você deseja atribuir uma classe de recurso maior ao usuário de carregamento.

Uma vez que as tabelas columnstore geralmente não enviam dados para um segmento de columnstore comprimido até que existam mais de 1 milhão de linhas por tabela e cada tabela do SQL Data Warehouse esteja dividida em 60 tabelas, como regra, as tabelas columnstore não beneficiam uma consulta se a tabela não tiver mais de 60 milhões de linhas.  Para tabelas com menos de 60 milhões de linhas, pode não fazer qualquer sentido ter um índice columnstore.  Também poderá não prejudicar.  Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma tabela tiver 100 partições, terá de ter, pelo menos, 6 milhões de linhas para beneficiar de um arquivo de colunas em cluster (60 distribuições * 100 partições * 1 milhão de linhas).  Se a sua tabela não tiver 6 mil milhões de linhas neste exemplo, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados.  Também poderá ser útil experimentar, para ver se consegue obter um melhor desempenho com uma tabela de área dinâmica para dados com índices secundários, em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Consulte também [Índices de tabela][Table indexes], [Guia de índices columnstore][Columnstore indexes guide], [Reconstruir índices columnstore][Rebuilding columnstore indexes]

## <a name="next-steps"></a>Passos seguintes
Se você não encontrar o que está procurando neste artigo, tente usar "Pesquisar documentos" no lado esquerdo desta página para pesquisar todos os documentos do Azure SQL Data Warehouse.  O [Fórum de SQL data warehouse do Azure][Azure SQL Data Warehouse MSDN Forum] é um lugar para você fazer perguntas para outros usuários e para o grupo de produtos SQL data warehouse.  Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se preferir colocar as suas questões no Stack Overflow, também temos um [Fórum do Stack Overflow do Azure SQL Data Warehouse][Azure SQL Data Warehouse Stack Overflow Forum].

Por último, utilize a página [Comentários do Azure SQL Data Warehouse][Azure SQL Data Warehouse Feedback] para fazer pedidos de funcionalidades.  Adicionar os seus pedidos ou votar noutros pedidos que realmente nos ajudam a priorizar funcionalidades.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
