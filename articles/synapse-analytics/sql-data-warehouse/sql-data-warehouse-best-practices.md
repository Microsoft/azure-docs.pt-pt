---
title: Boas práticas para SQL Analytics em Azure Synapse Analytics (anteriormente SQL DW)
description: Recomendações e boas práticas para o desenvolvimento de soluções para a SQL Analytics em Azure Synapse Analytics (anteriormente SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 44dbc03a41cfde94c344ae331b21d7536778050c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619102"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Boas práticas para SQL Analytics em Azure Synapse Analytics (anteriormente SQL DW)

Este artigo é uma coleção de boas práticas para ajudá-lo a alcançar o melhor desempenho a partir da sua implementação [SQL Analytics.](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)  O objetivo deste artigo é dar-lhe alguma orientação básica e destacar áreas importantes de foco.  Cada secção apresenta-o a um conceito e depois aponta-o para artigos mais detalhados que cobrem o conceito em maior profundidade. A sequência de tópicos está na ordem da importância. 

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução de custos através da colocação em pausa e do dimensionamento, veja [Gerir a computação](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Manter as estatísticas
O Azure SQL Data Warehouse pode ser configurado para detetar e criar estatísticas automaticamente sobre colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  Recomendamos que ative AUTO_CREATE_STATISTICS para as suas bases de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas sobre as colunas utilizadas nas suas consultas estão sempre atualizadas. 

Se descobrir que está a demorar muito tempo a atualizar todas as suas estatísticas, talvez queira tentar ser mais seletiva sobre quais as colunas que precisam de atualizações estatísticas frequentes. Por exemplo, deve atualizar as colunas de data, onde podem ser adicionados diariamente novos valores. **Obterá o maior benefício com estatísticas atualizadas sobre colunas envolvidas em juntas, colunas utilizadas na cláusula WHERE e colunas encontradas no GRUPO BY.**

Consulte também [Gerir estatísticas de tabela][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilize DMVs para monitorizar e otimizar as suas consultas
O SQL Analytics tem vários DMVs que podem ser usados para monitorizar a execução de consultas.  O artigo abaixo sobre a monitorização apresenta instruções passo a passo sobre como ver os detalhes de uma consulta em execução.  Para encontrar rapidamente as consultas nestas DMVs, pode ajudar se utilizar a opção LABEL com as suas consultas.

Consulte também [Monitorizar a carga de trabalho com DMVs][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>Desempenho de consulta de sintonização com novas melhorias do produto
- [Otimização do desempenho com vistas materializadas](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Otimização do desempenho com índice columnstore em cluster ordenado](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Otimização do desempenho com a colocação em cache dos resultados](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes
Um carregamento único para uma pequena tabela com uma instrução INSERT ou mesmo um recarregamento periódico de uma consulta pode ajustar-se às suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  No entanto, se precisar de carregar milhares ou milhões de linhas ao longo do dia, poderá achar que o singleton INSERTS não é suficiente para acompanhar o ritmo.  Ao invés, desenvolva os seus processos de modo a que escrevam num ficheiro e outro processo carregue periodicamente este ficheiro.

Consulte também [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilize o PolyBase para carregar e exportar dados rapidamente

 A SQL Analytics suporta a carregamento e exportação de dados através de várias ferramentas, incluindo a Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta pode ser suficiente para as suas necessidades.  No entanto, quando estiver a carregar ou a exportar grandes volumes de dados ou se for preciso um desempenho rápido, o PolyBase é a melhor escolha.  
 
 A PolyBase foi concebida para alavancar a arquitetura MPP (Massively Parallel Processing) e irá carregar e exportar magnitudes de dados mais rapidamente do que qualquer outra ferramenta.  Os carregamentos do PolyBase podem ser executados com CTAS ou INSERT INTO.  **Utilizar CTAS irá minimizar o registo de transações e será a forma mais rápida de carregar os dados.** 
 
  A Azure Data Factory também suporta cargas PolyBase e pode obter um desempenho semelhante ao CTAS.  O PolyBase suporta uma variedade de formatos de ficheiro, incluindo ficheiros Gzip.  **Para maximizar a entrada ao utilizar ficheiros de texto gzip, separe ficheiros em 60 ou mais ficheiros para maximizar o paralelismo da sua carga.**  Para um débito total mais rápido, considere carregar dados em simultâneo.

Consulte também [dados de carga,][Load data] [Guia para utilização][Guide for using PolyBase]de padrões e estratégias de carregamento de [piscinaS SQL,][Azure SQL Data Warehouse loading patterns and strategies]Dados de Carga com Fábrica de [Dados Azure,][Load Data with Azure Data Factory]Mova dados com fábrica de [dados Azure, CRIE][Move data with Azure Data Factory] [FORMATO DE ARQUIVO EXTERNO,][CREATE EXTERNAL FILE FORMAT] [Crie tabela como selecionado (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas
Apesar do Polybase, também conhecido como tabelas externas, poder ser a forma mais rápida de carregar dados, não é o ideal para consultas. Atualmente, as tabelas polybase suportam apenas ficheiros de blob Azure e armazenamento do Lago De Dados Azure. Estes ficheiros não têm quaisquer recursos de computação a apoiá-los.  

Como resultado, a SQL Analytics não pode descarregar este trabalho e, portanto, deve ler todo o ficheiro carregando-o para tempdb para ler os dados.  Portanto, se tiver várias consultas a estes dados, é melhor carregar estes dados uma vez e que as consultas utilizem a tabela local.

Consulte também o [Guia para utilizar o PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash
Por predefinição, as tabelas são distribuídas por Round Robin.  Isto torna mais fácil para os utilizadores começarem a criar tabelas, sem terem de decidir sobre como as respetivas tabelas devem ser distribuídas.  As tabelas Round Robin podem ter um desempenho suficiente para algumas cargas de trabalho, mas, na maioria dos casos, selecionar uma coluna de distribuição irá proporcionar um desempenho muito melhor.  O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  

Por exemplo, se tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando associa a tabela de pedidos à tabela de transações em order_id, esta consulta torna-se uma consulta pass-through, o que significa que podemos eliminar operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.  

Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  Consulte os links abaixo para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como como definir uma tabela distribuída na cláusula COM da sua declaração CREATE TABLE.

Consulte também [Descrição geral da tabela][Table overview], [Distribuição da tabela][Table distribution], [Selecionar distribuição da tabela][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Não crie partições em demasia
Embora a partilha de dados possa ser eficaz para manter os seus dados através da troca de divisórias ou otimização de digitalizações com a eliminação da divisória, ter demasiadas divisórias pode abrandar as suas consultas.  Muitas vezes, uma estratégia de partição de alta granularidade, que pode funcionar bem no SQL Server pode não funcionar bem no SQL Analytics.  

Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas.  Tenha em mente que nos bastidores, a SQL Analytics partilha os seus dados em 60 bases de dados, por isso, se criar uma tabela com 100 divisórias, isto resulta em 6000 divisórias.  

Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  Considere menos granularidade do que a que poderá ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Consulte também [Criação de partições de tabela][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação
As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se tiver um INSERTo que espere demorar 1 hora, se possível, separe o INSirem em quatro partes, que funcionarão cada uma em 15 minutos.  Tire partido dos casos especiais de Registo Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para esvaziar tabelas, de forma a reduzir o risco de reversão.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  

Para tabelas não partitivas, considere utilizar um CTAS para escrever os dados que pretende manter numa tabela em vez de utilizar o DELETE.  Se um CTAS demorar o mesmo tempo, é uma operação muito mais segura para ser executada, uma vez que tem o mínimo de registo de transações e pode ser cancelado rapidamente, se necessário.

Consulte também [Compreender as transações][Understanding transactions], [Otimizar as transações][Optimizing transactions], [Criação de partições de tabela][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Criar tabela ao selecionar (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Reduzir o tamanho dos resultados da consulta  
Este passo ajuda-o a evitar problemas do lado do cliente causados por um grande resultado de consulta.  Pode editar a sua consulta para reduzir o número de linhas devolvidas. Algumas ferramentas de geração de consultas permitem-lhe adicionar sintaxe "Top N" a cada consulta.  Também pode CETAS a consulta resultar numa tabela temporária e, em seguida, utilizar a exportação da PolyBase para o processamento de nível inferior.

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna
Ao definir o seu DDL, utilizando o menor tipo de dados que irá suportar os seus dados irá melhorar o desempenho da consulta.  Isto é especialmente importante para colunas CHAR e VARCHAR.  Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Consulte também [Descrição geral da tabela][Table overview], [Tipos de dados de tabela][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilize tabelas temporárias de área dinâmica para dados em dados transitórios
Quando estiver temporariamente a aterrar dados, poderá descobrir que usar uma tabela de heap tornará o processo global mais rápido.  Se estiver a carregar dados apenas para testá-los antes de executar mais transformações, será muito mais rápido carregar a tabela para a tabela de área dinâmica para dados, do que carregar os dados para uma tabela columnstore em cluster.  

Além disso, carregar dados para uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela para o armazenamento permanente.  As tabelas temporárias começam com um "#" e só são acessíveis pela sessão que o criou, pelo que só podem funcionar em cenários limitados.   As tabelas de área dinâmica para dados são definidas na cláusula WITH de uma CREATE TABLE.  Se utilizar uma tabela temporária, não se esqueça de também criar estatísticas nessa tabela temporária.

Consulte também [Tabelas temporárias][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster
Os índices de lojas de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados no SQL Analytics.  Por padrão, as tabelas no SQL Analytics são criadas como ColumnStore Clustered.  Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  

Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  Consulte as [Causas de fraca qualidade do índice columnstore][Causes of poor columnstore index quality] no artigo [Índices de tabela][Table indexes], para obter instruções passo a passo sobre como detetar e melhorar a qualidade do segmento para tabelas columnstore em cluster.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs dos utilizadores que estão na classe média ou grande de recursos para carregar dados. A utilização de [unidades](what-is-a-data-warehouse-unit-dwu-cdwu.md) de armazém de dados mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

Uma vez que as tabelas de lojas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimido até que existam mais de 1 milhão de linhas por tabela e cada tabela SQL Analytics é dividida em 60 tabelas, por regra, as tabelas de lojas de colunas não beneficiarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  Para tabelas com menos de 60 milhões de linhas, pode não fazer qualquer sentido ter um índice columnstore.  Também poderá não prejudicar.  

Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma tabela tiver 100 partições, terá de ter, pelo menos, 6 milhões de linhas para beneficiar de um arquivo de colunas em cluster (60 distribuições * 100 partições * 1 milhão de linhas).  

Se a sua tabela não tiver 6 mil milhões de linhas neste exemplo, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados.  Também poderá ser útil experimentar, para ver se consegue obter um melhor desempenho com uma tabela de área dinâmica para dados com índices secundários, em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Consulte também [Índices de tabela][Table indexes], [Guia de índices columnstore][Columnstore indexes guide], [Reconstruir índices columnstore][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilize a classe de recursos maior para melhorar o desempenho da consulta
O SQL Analytics utiliza grupos de recursos como forma de alocar memória a consultas.  Fora da caixa, todos os utilizadores são atribuídos à classe de pequenos recursos, que concede 100 MB de memória por distribuição.  Uma vez que existem sempre 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total é de 6000 MB ou apenas abaixo de 6 GB em todo o sistema.  

Algumas consultas, como associações grandes ou carregamentos para tabelas columnstore em cluster, irão beneficiar de alocações de memória superiores.  Algumas consultas, como análises puras, não serão beneficiadas.  Por outro lado, a utilização de classes de recursos maiores reduz a conmoedação, pelo que deverá ter em consideração este impacto antes de transferir todos os seus utilizadores para uma grande classe de recursos.

Veja também [Classes de recursos para gestão de cargas de trabalho](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Utilize a Classe de Recursos Mais Pequena para Aumentar a Simultaneidade
Se está a notar que as consultas de utilizador parecem ter um atraso longo, pode ser que os seus utilizadores estejam a funcionar em classes de recursos maiores e estão a consumir muitas faixas de condivisões, fazendo com que outras consultas façam fila.  Para ver se as consultas dos utilizadores estão colocadas em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se são devolvidas linhas.

Veja também [Classes de recursos para gestão de cargas de trabalho](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Outros recursos
Consulte também o nosso artigo [Resolução de problemas][Troubleshooting], para ver problemas e soluções comuns.

Se não encontrou o que procura neste artigo, tente usar a "Search for docs" no lado esquerdo desta página para pesquisar todos os documentos azure synapse.  O [Azure Synapse Forum][Azure SQL Data Warehouse MSDN Forum] é um local para fazer perguntas a outros utilizadores e ao Grupo de Produtos Azure Synapse. 

Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se prefere fazer as suas perguntas sobre o Stack Overflow, também temos um Fórum de Transbordo de [Pilha synapse Azure.][Azure SQL Data Warehouse Stack Overflow Forum]

Por fim, utilize a página de [Feedback Do Synapse Azure][Azure SQL Data Warehouse Feedback] para fazer pedidos de funcionalidades.  Adicionar os seus pedidos ou votar noutros pedidos que realmente nos ajudam a priorizar funcionalidades.

<!--Image references-->

<!--Article references-->
[Create a support ticket]:sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]:sql-data-warehouse-develop-ctas.md
[Table overview]:sql-data-warehouse-tables-overview.md
[Table data types]:sql-data-warehouse-tables-data-types.md
[Table distribution]:sql-data-warehouse-tables-distribute.md
[Table indexes]:sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]:sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]:sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]:sql-data-warehouse-tables-partition.md
[Manage table statistics]:sql-data-warehouse-tables-statistics.md
[Temporary tables]:sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]:guidance-for-loading-data.md
[Load data]:design-elt-data-loading.md
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]:load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]:sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]:sql-data-warehouse-develop-transactions.md
[Optimizing transactions]:sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]:sql-data-warehouse-troubleshoot.md
[LABEL]:sql-data-warehouse-develop-label.md

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
