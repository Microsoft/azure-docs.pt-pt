---
title: Práticas recomendadas para análise de SQL no Azure Synapse Analytics (antigo SQL DW)
description: Recomendações e práticas recomendadas para o desenvolvimento de soluções para análise de SQL no Azure Synapse Analytics (anteriormente conhecido como SQL DW).
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646181"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Práticas recomendadas para análise de SQL no Azure Synapse Analytics (antigo SQL DW)

Este artigo é uma coleção de práticas recomendadas para ajudá-lo a obter o desempenho ideal da sua implantação do [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) .  A finalidade deste artigo é fornecer algumas diretrizes básicas e destacar áreas importantes de foco.  Cada seção apresenta um conceito e, em seguida, aponta para artigos mais detalhados que abordam o conceito em mais detalhes. A sequência de tópicos é a ordem de importância. 

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução de custos através da colocação em pausa e do dimensionamento, veja [Gerir a computação](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Manter as estatísticas
Os SQL Data Warehouse do Azure podem ser configurados para detectar e criar estatísticas automaticamente em colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  Recomendamos que você habilite AUTO_CREATE_STATISTICS para seus bancos de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas nas colunas usadas em suas consultas estejam sempre atualizadas. 

Se você achar que está demorando muito para atualizar todas as suas estatísticas, convém tentar ser mais seletivos sobre quais colunas precisam de atualizações de estatísticas frequentes. Por exemplo, deve atualizar as colunas de data, onde podem ser adicionados diariamente novos valores. **Você obterá mais benefícios com a atualização de estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.**

Consulte também [Gerir estatísticas de tabela][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilize DMVs para monitorizar e otimizar as suas consultas
A análise de SQL tem várias DMVs que podem ser usadas para monitorar a execução da consulta.  O artigo abaixo sobre a monitorização apresenta instruções passo a passo sobre como ver os detalhes de uma consulta em execução.  Para encontrar rapidamente as consultas nestas DMVs, pode ajudar se utilizar a opção LABEL com as suas consultas.

Consulte também [Monitorizar a carga de trabalho com DMVs][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajustar o desempenho de consultas com novos aprimoramentos de produtos
- [Ajuste de desempenho com exibições materializadas](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Ajuste de desempenho com o índice columnstore clusterizado ordenado](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Ajuste de desempenho com cache de conjunto de resultados](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes
Um carregamento único para uma pequena tabela com uma instrução INSERT ou mesmo um recarregamento periódico de uma consulta pode ajustar-se às suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  No entanto, se precisar de carregar milhares ou milhões de linhas ao longo do dia, poderá achar que o singleton INSERTS não é suficiente para acompanhar o ritmo.  Ao invés, desenvolva os seus processos de modo a que escrevam num ficheiro e outro processo carregue periodicamente este ficheiro.

Consulte também [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilize o PolyBase para carregar e exportar dados rapidamente

 A análise do SQL dá suporte ao carregamento e exportação de dados por meio de várias ferramentas, incluindo Azure Data Factory, polybase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta pode ser suficiente para as suas necessidades.  No entanto, quando estiver a carregar ou a exportar grandes volumes de dados ou se for preciso um desempenho rápido, o PolyBase é a melhor escolha.  
 
 O polybase foi projetado para aproveitar a arquitetura MPP (processamento paralelo maciço) e carregará e exportará as magnitudes de dados mais rápido do que qualquer outra ferramenta.  Os carregamentos do PolyBase podem ser executados com CTAS ou INSERT INTO.  **Utilizar CTAS irá minimizar o registo de transações e será a forma mais rápida de carregar os dados.** 
 
  O Azure Data Factory também dá suporte a cargas de polybase e pode alcançar um desempenho semelhante ao CTAS.  O PolyBase suporta uma variedade de formatos de ficheiro, incluindo ficheiros Gzip.  **Para maximizar a taxa de transferência ao usar arquivos de texto gzip, divida arquivos em 60 ou mais arquivos para maximizar o paralelismo de sua carga.**  Para um débito total mais rápido, considere carregar dados em simultâneo.

Consulte também [carregar dados][Load data], [guia para usar o polybase][Guide for using PolyBase], [padrões e estratégias de carregamento do pool SQL][Azure SQL Data Warehouse loading patterns and strategies], [carregar dados com Azure data Factory][Load Data with Azure Data Factory], [mover dados com Azure data Factory][Move data with Azure Data Factory], [criar formato de arquivo externo][CREATE EXTERNAL FILE FORMAT], [criar tabela como SELECT (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas
Apesar do Polybase, também conhecido como tabelas externas, poder ser a forma mais rápida de carregar dados, não é o ideal para consultas. Atualmente, as tabelas do polybase dão suporte apenas a arquivos de blob do Azure e armazenamento de Azure Data Lake. Estes ficheiros não têm quaisquer recursos de computação a apoiá-los.  

Como resultado, a análise do SQL não pode descarregar esse trabalho e, portanto, deve ler o arquivo inteiro carregando-o para tempdb a fim de ler os dados.  Portanto, se tiver várias consultas a estes dados, é melhor carregar estes dados uma vez e que as consultas utilizem a tabela local.

Consulte também o [Guia para utilizar o PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash
Por predefinição, as tabelas são distribuídas por Round Robin.  Isto torna mais fácil para os utilizadores começarem a criar tabelas, sem terem de decidir sobre como as respetivas tabelas devem ser distribuídas.  As tabelas Round Robin podem ter um desempenho suficiente para algumas cargas de trabalho, mas, na maioria dos casos, selecionar uma coluna de distribuição irá proporcionar um desempenho muito melhor.  O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  

Por exemplo, se tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando associa a tabela de pedidos à tabela de transações em order_id, esta consulta torna-se uma consulta pass-through, o que significa que podemos eliminar operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.  

Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  Consulte os links abaixo para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como definir uma tabela distribuída na cláusula WITH de sua instrução CREATE TABLE.

Consulte também [Descrição geral da tabela][Table overview], [Distribuição da tabela][Table distribution], [Selecionar distribuição da tabela][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Não crie partições em demasia
Embora o particionamento de dados possa ser eficaz para manter seus dados por meio da alternância de partição ou otimização de verificações com a eliminação de partição, ter muitas partições pode retardar suas consultas.  Geralmente, uma estratégia de particionamento de alta granularidade, que pode funcionar bem em SQL Server pode não funcionar bem na análise de SQL.  

Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas.  Lembre-se de que, em segundo plano, a análise do SQL particiona seus dados para você em 60, portanto, se você criar uma tabela com 100 partições, isso realmente resultará em 6000 partições.  

Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  Considere menos granularidade do que a que poderá ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Consulte também [Criação de partições de tabela][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação
As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se você tiver uma inserção que espera levar 1 hora, se possível, divida a inserção em quatro partes, que será executada em 15 minutos.  Tire partido dos casos especiais de Registo Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para esvaziar tabelas, de forma a reduzir o risco de reversão.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  

Para tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS levar o mesmo período de tempo, será uma operação muito mais segura para ser executada, pois ele tem o registro em log de transações mínimo e poderá ser cancelado rapidamente, se necessário.

Consulte também [Compreender as transações][Understanding transactions], [Otimizar as transações][Optimizing transactions], [Criação de partições de tabela][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Criar tabela ao selecionar (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Reduzir tamanhos de resultados de consulta  
Esta etapa ajuda a evitar problemas do lado do cliente causados por grandes resultados de consulta.  Você pode editar sua consulta para reduzir o número de linhas retornadas. Algumas ferramentas de geração de consulta permitem que você adicione a sintaxe "Top N" a cada consulta.  Você também pode CETAS o resultado da consulta a uma tabela temporária e, em seguida, usar a exportação do polybase para o processamento de nível inferior.

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna
Ao definir o DDL, usar o menor tipo de dados que dará suporte aos seus dados melhorará o desempenho da consulta.  Isto é especialmente importante para colunas CHAR e VARCHAR.  Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Consulte também [Descrição geral da tabela][Table overview], [Tipos de dados de tabela][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilize tabelas temporárias de área dinâmica para dados em dados transitórios
Quando os dados são temporários temporariamente, você pode descobrir que usar uma tabela de heap tornará o processo geral mais rápido.  Se estiver a carregar dados apenas para testá-los antes de executar mais transformações, será muito mais rápido carregar a tabela para a tabela de área dinâmica para dados, do que carregar os dados para uma tabela columnstore em cluster.  

Além disso, carregar dados para uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela para o armazenamento permanente.  As tabelas temporárias começam com um "#" e só podem ser acessadas pela sessão que a criou, para que elas só possam funcionar em cenários limitados.   As tabelas de área dinâmica para dados são definidas na cláusula WITH de uma CREATE TABLE.  Se utilizar uma tabela temporária, não se esqueça de também criar estatísticas nessa tabela temporária.

Consulte também [Tabelas temporárias][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster
Os índices columnstore clusterizados são uma das maneiras mais eficientes de armazenar seus dados na análise de SQL.  Por padrão, as tabelas na análise de SQL são criadas como ColumnStore clusterizado.  Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  

Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  Consulte as [Causas de fraca qualidade do índice columnstore][Causes of poor columnstore index quality] no artigo [Índices de tabela][Table indexes], para obter instruções passo a passo sobre como detetar e melhorar a qualidade do segmento para tabelas columnstore em cluster.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos médio ou grande para carregar dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) inferiores significa que você deseja atribuir uma classe de recurso maior ao usuário de carregamento.

Como as tabelas columnstore geralmente não enviam dados para um segmento columnstore compactado até que haja mais de 1 milhão linhas por tabela e cada tabela de análise de SQL seja particionada em tabelas 60, como uma regra prática, as tabelas columnstore não beneficiarão uma consulta, a menos que a tabela tem mais de 60 milhões linhas.  Para tabelas com menos de 60 milhões de linhas, pode não fazer qualquer sentido ter um índice columnstore.  Também poderá não prejudicar.  

Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma tabela tiver 100 partições, terá de ter, pelo menos, 6 milhões de linhas para beneficiar de um arquivo de colunas em cluster (60 distribuições * 100 partições * 1 milhão de linhas).  

Se a sua tabela não tiver 6 mil milhões de linhas neste exemplo, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados.  Também poderá ser útil experimentar, para ver se consegue obter um melhor desempenho com uma tabela de área dinâmica para dados com índices secundários, em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Consulte também [Índices de tabela][Table indexes], [Guia de índices columnstore][Columnstore indexes guide], [Reconstruir índices columnstore][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilize a classe de recursos maior para melhorar o desempenho da consulta
A análise de SQL usa grupos de recursos como uma maneira de alocar memória para consultas.  Para uso pronto, todos os usuários são atribuídos à classe de recursos pequena, que concede 100 MB de memória por distribuição.  Uma vez que existem sempre 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total é de 6000 MB ou apenas abaixo de 6 GB em todo o sistema.  

Algumas consultas, como associações grandes ou carregamentos para tabelas columnstore em cluster, irão beneficiar de alocações de memória superiores.  Algumas consultas, como análises puras, não serão beneficiadas.  No lado do inverso, a utilização de classes de recursos maiores reduz a simultaneidade, portanto, você desejará considerar esse impacto antes de mover todos os usuários para uma classe de recursos grande.

Veja também [Classes de recursos para gestão de cargas de trabalho](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Utilize a Classe de Recursos Mais Pequena para Aumentar a Simultaneidade
Se você estiver percebendo que as consultas do usuário parecem ter um longo atraso, pode ser que os usuários estejam executando em classes de recursos maiores e consumam muitos slots de simultaneidade, fazendo com que outras consultas sejam enfileiradas.  Para ver se as consultas dos utilizadores estão colocadas em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se são devolvidas linhas.

Consulte também [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md), [Sys. dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Outros recursos
Consulte também o nosso artigo [Resolução de problemas][Troubleshooting], para ver problemas e soluções comuns.

Se você não encontrou o que está procurando neste artigo, tente usar "Pesquisar documentos" no lado esquerdo desta página para pesquisar todos os documentos do Azure Synapse.  O [Fórum do Azure Synapse][Azure SQL Data Warehouse MSDN Forum] é um lugar para você fazer perguntas para outros usuários e para o grupo de produtos do Azure Synapse. 

Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se você preferir fazer suas perguntas em Stack Overflow, também temos um [Fórum do Azure Synapse Stack Overflow][Azure SQL Data Warehouse Stack Overflow Forum].

Por fim, use a página de [comentários do Azure Synapse][Azure SQL Data Warehouse Feedback] para fazer solicitações de recursos.  Adicionar os seus pedidos ou votar noutros pedidos que realmente nos ajudam a priorizar funcionalidades.

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
