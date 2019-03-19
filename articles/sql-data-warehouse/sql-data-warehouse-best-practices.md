---
title: Melhores práticas para o Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações e melhores práticas que deve saber quando desenvolve soluções para o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 11/26/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 519eae012db30d772a388865380e4909d91b711a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878147"
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Melhores práticas do Azure SQL Data Warehouse
Este artigo é uma coleção de melhores práticas para ajudá-lo a alcançar um desempenho ideal do Azure SQL Data Warehouse.  Alguns dos conceitos neste artigo são básicos e fáceis de explicar, outros conceitos são mais avançados e apenas falamos por alto neste artigo.  O objetivo deste artigo é proporcionar alguma orientação básica e consciencialização de áreas de foco importantes ao criar o seu armazém de dados.  Cada secção apresenta um conceito e, em seguida, encaminha-o para artigos mais detalhados que abordam o conceito de forma mais aprofundada.

Se está a começar a utilizar o Azure SQL Data Warehouse, não deixe que este artigo o intimide.  A sequência dos tópicos está maioritariamente por ordem de importância.  Se começar por se concentrar nos primeiros conceitos, estará no bom caminho.  Quando estiver mais familiarizado e à vontade com a utilização do SQL Data Warehouse, regresse e veja mais alguns conceitos.  Não irá demorar muito tempo para que tudo faça sentido.

Para carregar a documentação de orientação, veja [Guidance for loading data (Documentação de orientação para carregar dados)](guidance-for-loading-data.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento
Para obter mais informações sobre a redução de custos através da colocação em pausa e do dimensionamento, veja [Gerir a computação](sql-data-warehouse-manage-compute-overview.md). 


## <a name="maintain-statistics"></a>Manter as estatísticas
Ao contrário do SQL Server, que deteta e cria ou atualiza automaticamente as estatísticas em colunas, o SQL Data Warehouse requer a manutenção manual das estatísticas.  Embora planeemos alterar esta situação no futuro, por agora, deve realizar a manutenção das estatísticas para garantir que os planos do SQL Data Warehouse são otimizados.  Os planos criados pelo otimizador só serão bons consoante as estatísticas disponíveis.  **Criar estatísticas de amostragem em cada coluna é uma forma fácil de começar com as estatísticas.**  É igualmente importante atualizar as estatísticas à medida que ocorrem alterações significativas nos seus dados.  Poderá realizar uma abordagem conservadora para atualizar as estatísticas diariamente ou após cada carregamento.  Há sempre uma relação entre o desempenho e o custo para criar e atualizar as estatísticas. Se achar que está a demorar demasiado tempo a manter todas as estatísticas, deve tentar ser mais seletivo sobre as colunas que têm estatísticas ou as colunas que precisam de atualização frequente.  Por exemplo, deve atualizar as colunas de data, onde podem ser adicionados diariamente novos valores. **Irá beneficiar mais com estatísticas em colunas envolvidas em associações, colunas utilizadas na cláusula WHERE e colunas que se encontram em GROUP BY.**

Veja também [Manage table statistics][Manage table statistics] (Gerir estatísticas de tabela), [CREATE STATISTICS][CREATE STATISTICS] e [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes
Um carregamento único para uma pequena tabela com uma instrução INSERT ou mesmo um recarregamento periódico de uma consulta pode ajustar-se às suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  No entanto, se precisar de carregar milhares ou milhões de linhas ao longo do dia, poderá achar que o singleton INSERTS não é suficiente para acompanhar o ritmo.  Ao invés, desenvolva os seus processos de modo a que escrevam num ficheiro e outro processo carregue periodicamente este ficheiro.

Veja também [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilize o PolyBase para carregar e exportar dados rapidamente
O SQL Data Warehouse suporta o carregamento e a exportação de dados através de várias ferramentas, incluindo o Azure Data Factory, o PolyBase e o BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta pode ser suficiente para as suas necessidades.  No entanto, quando estiver a carregar ou a exportar grandes volumes de dados ou se for preciso um desempenho rápido, o PolyBase é a melhor escolha.  O PolyBase foi concebido para tirar partido da arquitetura MPP (Processamento Paralelo Massivo) do SQL Data Warehouse e, por conseguinte, irá carregar e exportar magnitudes de dados mais rapidamente do que qualquer outra ferramenta.  Os carregamentos do PolyBase podem ser executados com CTAS ou INSERT INTO.  **Utilizar CTAS irá minimizar o registo de transações e será a forma mais rápida de carregar os dados.**  O Azure Data Factory também suporta carregamentos do PolyBase e pode alcançar um desempenho semelhante como CTAS.  O PolyBase suporta uma variedade de formatos de ficheiro, incluindo ficheiros Gzip.  **Para maximizar o débito ao utilizar ficheiros de texto gzip, divida os ficheiros em 60 ficheiros ou mais de modo a maximizar o paralelismo do seu carregamento.**  Para um débito total mais rápido, considere carregar dados em simultâneo.

Veja também [Load data][Load data] (Carregar dados), [Guide for using PolyBase][Guide for using PolyBase] (Manual para utilizar o PolyBase), [Azure SQL Data Warehouse loading patterns and strategies][Azure SQL Data Warehouse loading patterns and strategies] (Estratégias e padrões de carregamento do Azure SQL Data Warehouse), [Load Data with Azure Data Factory][Load Data with Azure Data Factory] (Carregar Dados com o Azure Data Factory, [Move data with Azure Data Factory][Move data with Azure Data Factory] (Mover dados com o Azure Data Factory), [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT] e [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas
Apesar do Polybase, também conhecido como tabelas externas, poder ser a forma mais rápida de carregar dados, não é o ideal para consultas. Atualmente as tabelas Polybase do SQL Data Warehouse só suportam ficheiros de blob do Azure e armazenamento do Azure Data Lake. Estes ficheiros não têm quaisquer recursos de computação a apoiá-los.  Como resultado, o SQL Data Warehouse não pode descarregar este trabalho e, por conseguinte, tem de ler o ficheiro completo ao carregá-lo no tempdb para ler os dados.  Portanto, se tiver várias consultas a estes dados, é melhor carregar estes dados uma vez e que as consultas utilizem a tabela local.

Veja também [Guide for using PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash
Por predefinição, as tabelas são distribuídas por Round Robin.  Isto torna mais fácil para os utilizadores começarem a criar tabelas, sem terem de decidir sobre como as respetivas tabelas devem ser distribuídas.  As tabelas Round Robin podem ter um desempenho suficiente para algumas cargas de trabalho, mas, na maioria dos casos, selecionar uma coluna de distribuição irá proporcionar um desempenho muito melhor.  O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  Por exemplo, se tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando associa a tabela de pedidos à tabela de transações em order_id, esta consulta torna-se uma consulta pass-through, o que significa que podemos eliminar operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.  Esta é uma explicação apenas aborda superficialmente. Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  Veja as ligações abaixo para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como para definir uma tabela distribuída na cláusula WITH da instrução CREATE TABLE.

Veja também [Table overview][Table overview] (Descrição geral das tabelas), [Table distribution][Table distribution] (Distribuição de tabelas), [Selecting table distribution][Selecting table distribution] (Selecionar a distribuição de tabelas), [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Não crie partições em demasia
Embora o particionamento de dados possa ser muito eficaz na manutenção dos seus dados, com a mudança de partições ou a otimização de análises com a eliminação de partições, demasiadas partições podem abrandar as suas consultas.  Muitas vezes, uma estratégia de particionamento de elevada granularidade que poderá funcionar bem no SQL Server, pode não funcionar tão bem no SQL Data Warehouse.  Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas.  Tenha em atenção que, em segundo plano, o SQL Data Warehouse particiona os dados em 60 bases de dados, pelo que, se criar uma tabela com 100 partições, resulta efetivamente em 6000 partições.  Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  Considere menos granularidade do que a que poderá ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Veja também [Table partitioning][Table partitioning] (Criação de partições de tabelas)

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação
As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  Por exemplo, se tiver uma INSERT que deverá demorar 1 hora, se possível, divida a INSERT em 4 partes, que serão executadas em 15 minutos cada uma.  Tire partido dos casos especiais de Registo Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para esvaziar tabelas, de forma a reduzir o risco de reversão.  Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  Para tabelas sem partição considere utilizar um CTAS para escrever os dados que pretende manter numa tabela, em vez de utilizar DELETE.  Se um CTAS demorar o mesmo tempo, é uma operação mais segura uma vez que tem um registo de transações mínimo e pode ser cancelada rapidamente se for necessário.

Veja também [Understanding transactions][Understanding transactions], [Optimizing transactions][Optimizing transactions], [Table partitioning][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE] e [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna
Quando definir a DDL, utilize o tipo de dados mais pequeno que suporte os dados, de forma a melhorar o desempenho das consultas.  Isto é especialmente importante para colunas CHAR e VARCHAR.  Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Veja também [Table overview][Table overview], [Table data types][Table data types] (Tipos de dados de tabela) e [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilize tabelas temporárias de área dinâmica para dados em dados transitórios
Quando está temporariamente a colocar dados no SQL Data Warehouse, utilizar uma tabela de área dinâmica para dados irá tornar o processo geral mais rápido.  Se estiver a carregar dados apenas para testá-los antes de executar mais transformações, será muito mais rápido carregar a tabela para a tabela de área dinâmica para dados, do que carregar os dados para uma tabela columnstore em cluster.  Além disso, carregar dados para uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela para o armazenamento permanente.  As tabelas temporárias começam com um "#" e apenas estão acessíveis pela sessão que a criou, pelo que só poderão funcionar em cenários limitados.   As tabelas de área dinâmica para dados são definidas na cláusula WITH de uma CREATE TABLE.  Se utilizar uma tabela temporária, não se esqueça de também criar estatísticas nessa tabela temporária.

Veja também [Temporary tables][Temporary tables] (Tabelas temporárias), [CREATE TABLE][CREATE TABLE] e [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster
Os índices columnstore em cluster são uma das formas mais eficientes de armazenar os seus dados no SQL Data Warehouse.  Por predefinição, as tabelas no SQL Data Warehouse são criadas como ColumnStore em Cluster.  Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  Veja [Causes of poor columnstore index quality][Causes of poor columnstore index quality] (Causas da má qualidade do índice columnstore) no artigo [Table indexes][Table indexes] (Índices de tabela) para obter instruções passo a passo sobre como detetar e melhorar a qualidade do segmento para tabelas columnstore em cluster.  Como os segmentos de columnstore de alta qualidade são importantes, é uma boa idéia usar IDs que estão na classe de recursos média ou grande para carregar dados de utilizadores. Usando inferiores [unidades de armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

Uma vez que as tabelas columnstore geralmente não enviam dados para um segmento de columnstore comprimido até que existam mais de 1 milhão de linhas por tabela e cada tabela do SQL Data Warehouse esteja dividida em 60 tabelas, como regra, as tabelas columnstore não beneficiam uma consulta se a tabela não tiver mais de 60 milhões de linhas.  Para tabelas com menos de 60 milhões de linhas, pode não fazer qualquer sentido ter um índice columnstore.  Também poderá não prejudicar.  Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma tabela tiver 100 partições, terá de ter, pelo menos, 6 milhões de linhas para beneficiar de um arquivo de colunas em cluster (60 distribuições * 100 partições * 1 milhão de linhas).  Se a sua tabela não tiver 6 mil milhões de linhas neste exemplo, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados.  Também poderá ser útil experimentar, para ver se consegue obter um melhor desempenho com uma tabela de área dinâmica para dados com índices secundários, em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Veja também [Table indexes][Table indexes], [Columnstore indexes guide][Columnstore indexes guide] (Manual de indíces Columnstore) e [Rebuilding columnstore indexes][Rebuilding columnstore indexes] (Recriar índices columnstore)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilize a classe de recursos maior para melhorar o desempenho da consulta
O SQL Data Warehouse utiliza grupos de recursos como uma forma de alocar memória para consultas.  À partida, todos os utilizadores são atribuídos à classe de recursos pequena, que concede 100 MB de memória por distribuição.  Uma vez que existem sempre 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total é de 6000 MB ou apenas abaixo de 6 GB em todo o sistema.  Algumas consultas, como associações grandes ou carregamentos para tabelas columnstore em cluster, irão beneficiar de alocações de memória superiores.  Algumas consultas, como análises puras, não serão beneficiadas.  Por outro lado, utilizar classes de recursos maiores provoca um impacto na simultaneidade, por isso deve ter isto em consideração antes de mover todos os seus utilizadores para uma classe de recursos grande.

Veja também [Classes de recursos para gestão de cargas de trabalho](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Utilize a Classe de Recursos Mais Pequena para Aumentar a Simultaneidade
Se notar que as consultas do utilizador estão com grande atraso, é possível que os utilizadores estejam em execução em classes de recursos maiores e a consumir muitos blocos de simultaneidade que fazem com que as outras consultas entrem em fila.  Para ver se as consultas dos utilizadores estão colocadas em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se são devolvidas linhas.

Veja também [Classes de recursos para gestão de cargas de trabalho](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilize DMVs para monitorizar e otimizar as suas consultas
O SQL Data Warehouse tem várias DMVs que podem ser utilizadas para monitorizar a execução da consulta.  O artigo abaixo sobre a monitorização apresenta instruções passo a passo sobre como ver os detalhes de uma consulta em execução.  Para encontrar rapidamente as consultas nestas DMVs, pode ajudar se utilizar a opção LABEL com as suas consultas.

Veja também [Monitor your workload using DMVs][Monitor your workload using DMVs] (Utilizar DMVs para monitorizar a sua carga de trabalho), [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] e [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Outros recursos
Veja também o nosso artigo de [Troubleshooting][Troubleshooting] (Resolução de Problemas) para consultar problemas comuns e soluções.

Se não encontrar o que procura neste artigo, experimente utilizar a "Pesquisa de documentos" no lado esquerdo desta página para pesquisar todos os documentos do Azure SQL Data Warehouse.  O [fórum do Azure SQL Data Warehouse] [ Azure SQL Data Warehouse MSDN Forum] é um local para fazer perguntas a outros utilizadores e para o grupo de produto do SQL Data Warehouse.  Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se preferir fazer as suas perguntas no Stack Overflow, também temos um [Fórum do Stack Overflow do Azure SQL Data Warehouse][Azure SQL Data Warehouse Stack Overflow Forum].

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
