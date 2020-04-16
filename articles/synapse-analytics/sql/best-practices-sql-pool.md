---
title: Boas práticas para piscinas SQL
description: Recomendações e boas práticas que deve conhecer enquanto trabalha com piscinas SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427799"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Boas práticas para piscinas SQL em Azure Synapse Analytics

Este artigo fornece uma coleção de boas práticas para ajudá-lo a alcançar um desempenho ideal para piscinas SQL em Azure Synapse Analytics. Abaixo encontrará orientação básica e áreas importantes para se concentrar à medida que constrói a sua solução. Cada secção apresenta-o a um conceito e depois aponta-o para artigos mais detalhados que cobrem o conceito em maior profundidade.

## <a name="sql-pools-loading"></a>Carregamento de piscinas SQL

Para piscinas SQL, consulte [Orientação para carregar dados](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução dos custos através da pausa e escalagem, consulte Gerir a [computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Manter as estatísticas

Enquanto o SQL Server deteta e cria ou atualiza automaticamente as estatísticas sobre colunas, as piscinas SQL requerem manutenção manual de estatísticas. Você vai querer manter as suas estatísticas para garantir que os planos de piscina SQL estão otimizados.  Os planos criados pelo otimizador só serão bons consoante as estatísticas disponíveis.

> [!TIP]
> Criar estatísticas de amostragem em cada coluna é uma forma fácil de começar com as estatísticas.  

É igualmente importante atualizar as estatísticas à medida que ocorrem alterações significativas nos seus dados.  Poderá realizar uma abordagem conservadora para atualizar as estatísticas diariamente ou após cada carregamento.  Há sempre uma relação entre o desempenho e o custo para criar e atualizar as estatísticas.

Para encurtar o tempo de manutenção das estatísticas, seja seletivo sobre quais as colunas que têm estatísticas, ou que necessitem da atualização mais frequente. Por exemplo, é possível atualizar as colunas de datas onde novos valores possam ser adicionados diariamente. Concentre-se em ter estatísticas para colunas envolvidas em juntas, colunas utilizadas na cláusula WHERE e colunas encontradas no GROUP BY.

Informações adicionais sobre estatísticas podem ser encontradas nas estatísticas do [quadro de Gestão](develop-tables-statistics.md), [CRIAR ESTATÍSTICAS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [ATUALIZAR ARTIGOS ESTATÍSTICAS.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma carga única para uma tabela pequena com `INSERT INTO MyLookup VALUES (1, 'Type 1')`uma declaração INSERT, como pode ser a melhor abordagem dependendo das suas necessidades. No entanto, se precisar de carregar milhares ou milhões de filas ao longo do dia, é provável que os inserções singleton não sejam os ideais.

Uma forma de resolver este problema é desenvolver um processo que escreva para um ficheiro, e depois outro processo para carregar periodicamente este ficheiro. Consulte o artigo [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para mais informações.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilize o PolyBase para carregar e exportar dados rapidamente

O pool SQL suporta a carga e exportação de dados através de várias ferramentas, incluindo a Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta pode ser suficiente para as suas necessidades.  

> [!NOTE]
> A Polybase é a melhor escolha quando está a carregar ou a exportar grandes volumes de dados, ou precisa de um desempenho mais rápido.

Os carregamentos do PolyBase podem ser executados com CTAS ou INSERT INTO. O CTAS minimizará o registo de transações e é a forma mais rápida de carregar os seus dados. A Azure Data Factory também suporta cargas PolyBase e pode obter um desempenho semelhante ao CTAS. A PolyBase suporta vários formatos de ficheiros, incluindo ficheiros Gzip.

Para maximizar a entrada ao utilizar ficheiros de texto Gzip, separe ficheiros em 60 ou mais ficheiros para maximizar o paralelismo da sua carga. Para um débito total mais rápido, considere carregar dados em simultâneo. Informações adicionais para os tópicos relevantes para esta secção estão incluídas nos seguintes artigos:

- [Carregar dados](data-loading-overview.md)
- [Guia para utilizar o PolyBase](data-loading-best-practices.md)
- [Padrões e estratégias de carregamento de piscina SQL Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Dados de carga com fábrica de dados azure](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Mover dados com o Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Criar tabela ao selecionar (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

A polibase não é ideal para consultas. Tabelas de polibase para piscinas SQL atualmente apenas suportam ficheiros de blob Azure e armazenamento do Lago De Dados Azure. Estes ficheiros não têm recursos de computação que os apoiam. Como resultado, as piscinas SQL não podem descarregar este trabalho e devem ler todo o ficheiro carregando-o para tempdb para que possa ler os dados.

Se tiver várias consultas para consultar estes dados, é melhor carregar estes dados uma vez e ter consultas que utilizem a tabela local. Outraorientação da Polybase está incluída no Guia para a utilização do artigo [da PolyBase.](data-loading-best-practices.md)

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash

Por predefinição, as tabelas são distribuídas por Round Robin.   Este padrão facilita que os utilizadores comecem a criar tabelas sem terem de decidir como as suas tabelas devem ser distribuídas. As mesas redondas robin podem funcionar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, uma coluna de distribuição proporciona um melhor desempenho.  

O exemplo mais comum de uma mesa distribuída por uma coluna que supera uma mesa Round Robin é quando duas grandes tabelas de factos são unidas.  

Por exemplo, se tiver uma tabela de encomendas distribuída por order_id, e uma tabela de transações também distribuída por order_id, quando se junta à tabela de encomendas na tabela de transações sobre order_id, esta consulta torna-se uma consulta pass-through. As operações de movimento de dados são então eliminadas. Menos passos significam uma consulta mais rápida. Menos movimento de dados também torna as consultas mais rápidas.

> [!NOTE]
> Ao carregar uma tabela distribuída, os seus dados de entrada não devem ser classificados na chave de distribuição. Fazê-lo vai abrandar as suas cargas.

As ligações de artigo fornecidas abaixo lhe darão detalhes adicionais sobre a melhoria do desempenho através da seleção de uma coluna de distribuição. Além disso, encontrará informações sobre como definir uma tabela distribuída na cláusula COM da sua declaração CREATE TABLE:

- [Visão geral da tabela](develop-tables-overview.md)
- [Distribuição da tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Selecionar distribuição da tabela](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Não crie partições em demasia

Embora a partilha de dados possa ser eficaz para manter os seus dados através da troca de divisórias ou otimização de digitalizações com a eliminação da divisória, ter demasiadas divisórias pode abrandar as suas consultas.  Muitas vezes, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem na piscina SQL.  

Ter demasiadas divisórias pode reduzir a eficácia dos índices de lojas de colunas agrupadas se cada partição tiver menos de 1 milhão de linhas. A SQL partilha automaticamente os seus dados em 60 bases de dados. Então, se criar uma mesa com 100 divisórias, o resultado serão 6000 divisórias. Cada carga de trabalho é diferente, por isso o melhor conselho é experimentar a partilha para ver o que funciona melhor para a sua carga de trabalho.  

Uma opção a considerar é usar uma granularidade inferior à que implementou usando o SQL Server. Por exemplo, considere usar divisórias semanais ou mensais em vez de divisórias diárias.

Mais informações sobre a partilha são detalhadas no artigo de partilha da [tabela.](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação

Insira, atualize e elimine as declarações numa transação. Quando falham, devem ser revirados. Para reduzir o potencial de uma reversão longa, minimize os tamanhos de transação sempre que possível.  Minimizar os tamanhos das transações pode ser feito dividindo as declarações insert, UPDATE e DELETE em peças. Por exemplo, se tiver um INSERTo que espere demorar 1 hora, pode dividir o INSEREM em quatro partes. Cada corrida será então encurtada para 15 minutos.  

> [!TIP]
> Aproveite as caixas especiais de registo mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT a mesas vazias para reduzir o risco de retrocesso.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma declaração DELETE para apagar todas as linhas numa tabela onde o order_date estava em outubro de 2001, você poderia dividir os seus dados mensalmente. Em seguida, pode trocar a partição com dados para uma divisória vazia de outra tabela (ver exemplos ALTER TABLE).  

Para tabelas não partitivas, considere utilizar um CTAS para escrever os dados que pretende manter numa tabela em vez de utilizar o DELETE.  Se um CTAS demorar o mesmo tempo, é muito mais seguro executar, uma vez que tem o mínimo de registo de transações e pode ser cancelado rapidamente, se necessário.

Mais informações sobre o conteúdo relacionado com esta secção estão incluídas nos artigos abaixo:

- [Criar tabela ao selecionar (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Compreender as transações](develop-transactions.md)
- [Otimizar as transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partição de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tabela TRUNCATE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Reduzir o tamanho dos resultados da consulta

Reduzir o tamanho dos resultados da consulta ajuda-o a evitar problemas do lado do cliente causados por grandes resultados de consulta.  Pode editar a sua consulta para reduzir o número de linhas devolvidas. Algumas ferramentas de geração de consultas permitem-lhe adicionar sintaxe "Top N" a cada consulta.  Também pode CETAS a consulta resultar numa tabela temporária e, em seguida, utilizar a exportação da PolyBase para o processamento de nível inferior.

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna

Ao definir o seu DDL, utilize o menor tipo de dados que irá suportar os seus dados, pois isso melhorará o desempenho da consulta.  Esta recomendação é particularmente importante para as colunas CHAR e VARCHAR.  Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário em vez de usar o NVARCHAR.

Consulte a [visão geral](develop-tables-overview.md)da tabela , os tipos de dados de [tabela,](develop-tables-data-types.md)e criar artigos [TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para uma revisão mais detalhada dos conceitos essenciais relevantes para a informação acima.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilize tabelas temporárias de área dinâmica para dados em dados transitórios

Quando você está temporariamente a aterrar dados em piscinas SQL, as tabelas de heap geralmente tornarão o processo geral mais rápido.  Se estiver a carregar dados apenas para os encenar antes de realizar mais transformações, carregar a tabela para uma tabela de heap será mais rápido do que carregar os dados para uma tabela de colunas agrupada.  

Carregar dados para uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela para armazenamento permanente.  As tabelas temporárias começam com um "#" e só são acessíveis pela sessão que o criou. Consequentemente, só podem funcionar em cenários limitados. As tabelas de área dinâmica para dados são definidas na cláusula WITH de uma CREATE TABLE.  Se utilizar uma tabela temporária, não se esqueça de também criar estatísticas nessa tabela temporária.

Para obter orientações adicionais, consulte as [tabelas Temporárias](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) artigos.

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster

Os índices de lojas de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados em piscina SQL.  Por padrão, as tabelas na piscina SQL são criadas como ColumnStore Clustered.  Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  

A qualidade do segmento pode ser medida pelo número de linhas num Grupo de Linhas Comprimidos. Consulte as [causas da má qualidade do índice de colunas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) no artigo [dos índices](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) tabela para instruções passo a passo sobre a deteção e melhoria da qualidade do segmento para tabelas de colunas agrupadas.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs dos utilizadores que estão na classe média ou grande de recursos para carregar dados. A utilização de [unidades](resource-consumption-models.md) de armazém de dados mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

As tabelas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimido até que haja mais de 1 milhão de linhas por tabela. Cada mesa de bilhar SQL é dividida em 60 mesas. Como tal, as tabelas de lojas de colunas não beneficiarão uma consulta a menos que a mesa tenha mais de 60 milhões de filas.  

> [!TIP]
> Para tabelas com menos de 60 milhões de linhas, ter um índice de colunas pode não ser a solução ideal.  

Se dividir os seus dados, cada partição terá de ter 1 milhão de linhas para beneficiar de um índice de lojas de colunas agrupadas.  Para uma tabela com 100 divisórias, precisa de ter pelo menos 6 mil milhões de filas para beneficiar de uma loja de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas).  

Se a tua mesa não tiver 6 biliões de filas, tens duas opções principais. Ou reduza o número de divisórias ou considere usar uma mesa de heap.  Também pode valer a pena experimentar para ver se um melhor desempenho pode ser obtido usando uma tabela heap com índices secundários em vez de uma tabela de colunas.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  Mais informações sobre índices de tabela e loja de colunas e podem ser encontradas nos índices de [tabela,](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [guia de índices de colunas](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e artigos de índices de [colunas de reconstrução.](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilize a classe de recursos maior para melhorar o desempenho da consulta

As piscinas SQL usam grupos de recursos como forma de alocar a memória a consultas. Inicialmente, todos os utilizadores são atribuídos à classe de pequenos recursos, que concede 100 MB de memória por distribuição.  Há sempre 60 distribuições. Cada distribuição tem um mínimo de 100 MB. A atribuição total de memória em todo o sistema é de 6.000 MB, ou pouco menos de 6 GB.  

Algumas consultas, como associações grandes ou carregamentos para tabelas columnstore em cluster, irão beneficiar de alocações de memória superiores.  Algumas consultas, como exames puros, não verão nenhum benefício. A utilização de classes de recursos maiores tem impacto na conmoeda. Por isso, vai querer ter estes factos em mente antes de transferir todos os seus utilizadores para uma grande classe de recursos.

Para obter informações adicionais sobre as classes de recursos, consulte as classes de Recursos para o artigo de gestão da [carga de trabalho.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Use uma classe de recursos mais pequena para aumentar a conmoeda

Se notar um longo atraso nas consultas dos utilizadores, os seus utilizadores podem estar a funcionar em classes de recursos maiores. Este cenário promove o consumo de faixas de condivisões, o que pode fazer com que outras consultas façam fila.  Para determinar se as consultas dos `SELECT * FROM sys.dm_pdw_waits` utilizadores estão na fila, corra para ver se alguma linha é devolvida.

As [classes de Recursos para gestão](../sql-data-warehouse/resource-classes-for-workload-management.md) de carga de trabalho e [artigos sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) fornecer-lhe-ão mais informações.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilize DMVs para monitorizar e otimizar as suas consultas

As piscinas SQL têm vários DMVs que podem ser usados para monitorizar a execução de consultas.  O artigo de monitorização abaixo acompanha-o através de instruções passo a passo sobre como ver detalhes de uma consulta de execução.  Para encontrar rapidamente as consultas nestas DMVs, pode ajudar se utilizar a opção LABEL com as suas consultas. Para obter informações detalhadas adicionais, consulte os artigos incluídos na lista abaixo:

- [Monitorizar a carga de trabalho com DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [LABEL](develop-label.md)
- [OPÇÃO](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Passos seguintes

Consulte também o artigo de Resolução de [Problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para questões e soluções comuns.

Se precisar de informações não fornecidas neste artigo, utilize a "Search for docs" no lado esquerdo desta página para pesquisar todos os documentos da piscina SQL.  O Fórum de [Piscina SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um local para colocar questões a outros utilizadores e ao Grupo de Produtos de Piscina SQL.  

Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se você prefere fazer suas perguntas sobre Stack Overflow, também temos uma [piscina Azure SQL Stack Overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Para pedidos de funcionalidades, utilize a página de Feedback da [piscina Azure SQL.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  Adicionar os seus pedidos ou votar em votação antecipada outros pedidos ajuda-nos a focar nos recursos mais procurados.
