---
title: Boas práticas para piscina SQL dedicada (anteriormente SQL DW)
description: Recomendações e boas práticas para o desenvolvimento de soluções para piscinas SQL dedicadas (anteriormente SQL DW) em Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d62ea594916dbf5656569b7e1a3afe9ee81d8f89
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685298"
---
# <a name="best-practices-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Boas práticas para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Este artigo é uma coleção de boas práticas para ajudá-lo a obter o melhor desempenho a partir da sua [implantação dedicada sql pool (anteriormente SQL DW).](sql-data-warehouse-overview-what-is.md)  O objetivo deste artigo é dar-lhe alguma orientação básica e destacar áreas importantes de foco.  

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução de custos através da colocação em pausa e do dimensionamento, veja [Gerir a computação](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Manter as estatísticas

A piscina SQL dedicada (anteriormente SQL DW) pode ser configurada para detetar e criar automaticamente estatísticas sobre colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  

Recomendamos que ative AUTO_CREATE_STATISTICS para as suas bases de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas sobre colunas utilizadas nas suas consultas estão sempre atualizadas.

Se descobrir que está a demorar muito tempo a atualizar todas as suas estatísticas, talvez queira tentar ser mais seletivo sobre quais colunas precisam de atualizações estatísticas frequentes. Por exemplo, deve atualizar as colunas de data, onde podem ser adicionados diariamente novos valores.

> [!TIP]
> Obterá o maior benefício ao ter estatísticas atualizadas sobre colunas envolvidas em junções, colunas utilizadas na cláusula WHERE e colunas encontradas no GROUP BY.

Consulte também [estatísticas de tabelas,](sql-data-warehouse-tables-statistics.md) [CRIAR ESTATÍSTICAS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e ESTATÍSTICAS [DE ATUALIZAÇÃO](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilize DMVs para monitorizar e otimizar as suas consultas

Piscina de SQL dedicada (anteriormente SQL DW) tem vários DMVs que podem ser usados para monitorizar a execução de consultas.  O [Monitor da sua carga de trabalho utilizando dMVs](sql-data-warehouse-manage-monitor.md) artigo detalhes instruções passo a passo sobre como olhar os detalhes de uma consulta de execução.  

Para encontrar rapidamente as consultas nestas DMVs, pode ajudar se utilizar a opção LABEL com as suas consultas.

Consulte também [a sua carga de trabalho utilizando DMVs,](sql-data-warehouse-manage-monitor.md) [LABEL,](sql-data-warehouse-develop-label.md) [OPTION,](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [sys.dm_exec_sessions,](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [sys.dm_pdw_request_steps,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [sys.dm_pdw_sql_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [sys.dm_pdw_dms_workers,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), e [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajuste o desempenho da consulta com novos melhoramentos de produtos

- [Otimização do desempenho com vistas materializadas](performance-tuning-materialized-views.md)
- [Otimização do desempenho com índice columnstore em cluster ordenado](performance-tuning-ordered-cci.md)
- [Otimização do desempenho com a colocação em cache dos resultados](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma carga única para uma pequena mesa com uma declaração INSERT ou mesmo uma recarga periódica de um look-up pode funcionar bem para as suas necessidades com uma afirmação como `INSERT INTO MyLookup VALUES (1, 'Type 1')` .  

No entanto, se precisar de carregar milhares ou milhões de linhas ao longo do dia, poderá achar que o singleton INSERTS não é suficiente para acompanhar o ritmo.  Ao invés, desenvolva os seus processos de modo a que escrevam num ficheiro e outro processo carregue periodicamente este ficheiro.

Ver também [INSERIR](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilize o PolyBase para carregar e exportar dados rapidamente

O pool de SQL dedicado (anteriormente SQL DW) suporta dados de carregamento e exportação através de várias ferramentas, incluindo Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta pode ser suficiente para as suas necessidades.  No entanto, quando estiver a carregar ou a exportar grandes volumes de dados ou se for preciso um desempenho rápido, o PolyBase é a melhor escolha.  

A PolyBase foi concebida para alavancar a natureza distribuída do sistema e carregará e exportará magnitudes de dados mais rapidamente do que qualquer outra ferramenta.  Os carregamentos do PolyBase podem ser executados com CTAS ou INSERT INTO.   

> [!TIP]
> Utilizar CTAS irá minimizar o registo de transações e será a forma mais rápida de carregar os dados.

A Azure Data Factory também suporta cargas PolyBase e pode obter um desempenho semelhante ao CTAS.  O PolyBase suporta uma variedade de formatos de ficheiro, incluindo ficheiros Gzip.  
  
> [!NOTE]
> Para maximizar a produção ao utilizar ficheiros de texto gzip, descole os ficheiros em 60 ou mais ficheiros para maximizar o paralelismo da sua carga.  Para um débito total mais rápido, considere carregar dados em simultâneo.

Consulte também [os dados de carga,](design-elt-data-loading.md) [Guia para a utilização de PolyBase](guidance-for-loading-data.md), [Padrões e estratégias de carregamento de piscinas SQL dedicados, Dados](/archive/blogs/sqlcat/) [de Carga com Azure Data Factory,]( ../../data-factory/load-azure-sql-data-warehouse.md) [Mover dados com Azure Data Factory,](../../data-factory/transform-data-using-machine-learning.md) [CRIAR Formato de FICHEIRO EXTERNO,](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [Criar tabela como selecionado (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

Apesar do Polybase, também conhecido como tabelas externas, poder ser a forma mais rápida de carregar dados, não é o ideal para consultas. Atualmente, as tabelas Polybase suportam apenas ficheiros blob Azure e armazenamento do Lago de Dados Azure. Estes ficheiros não têm quaisquer recursos de computação a apoiá-los.  

Como resultado, o pool DE SQL dedicado não pode descarregar este trabalho e, portanto, deve ler todo o ficheiro carregando-o para a temperatura para ler os dados.  Portanto, se tiver várias consultas a estes dados, é melhor carregar estes dados uma vez e que as consultas utilizem a tabela local.

Consulte também [o Guia para a utilização do PolyBase.](guidance-for-loading-data.md)

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash

Por predefinição, as tabelas são distribuídas por Round Robin.  Isto torna mais fácil para os utilizadores começarem a criar tabelas, sem terem de decidir sobre como as respetivas tabelas devem ser distribuídas.  As tabelas Round Robin podem ter um desempenho suficiente para algumas cargas de trabalho, mas, na maioria dos casos, selecionar uma coluna de distribuição irá proporcionar um desempenho muito melhor.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  

Por exemplo, se tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando associa a tabela de pedidos à tabela de transações em order_id, esta consulta torna-se uma consulta pass-through, o que significa que podemos eliminar operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.  

> [!TIP]
> Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  

Consulte os seguintes links para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como como definir uma tabela distribuída na cláusula WITH da sua declaração CREATE TABLE.

Consulte também [a visão geral da tabela,](sql-data-warehouse-tables-overview.md) [distribuição de tabelas,](sql-data-warehouse-tables-distribute.md) [Selecionando a distribuição da tabela,](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service) [CRIE TABELA,](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [CRIE TABELA COMO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="do-not-over-partition"></a>Não crie partições em demasia

Embora os dados de partição possam ser eficazes para manter os seus dados através da comutação de partição ou otimização de digitalizações com eliminação de divisórias, ter demasiadas divisórias pode abrandar as suas consultas.  Muitas vezes, uma estratégia de partição de alta granularidade, que pode funcionar bem no SQL Server pode não funcionar bem em piscina SQL dedicada (anteriormente SQL DW).  

Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas.  Tenha em mente que nos bastidores, divisórias de piscinas SQL dedicadas os seus dados para si em 60 bases de dados, por isso, se criar uma tabela com 100 divisórias, isso resulta em 6000 divisórias.  

Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  Considere menos granularidade do que a que poderá ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Consulte também [a divisão da tabela.](sql-data-warehouse-tables-partition.md)

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação

As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se tiver um INSERT que espera levar 1 hora, se possível, desa parte o INSERT em quatro partes, que cada uma funciona em 15 minutos.  Aproveite os casos especiais de registo mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para mesas vazias, para reduzir o risco de reversão.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma declaração DELETE para apagar todas as linhas de uma tabela onde o order_date estava em outubro de 2001, poderia dividir os seus dados mensalmente e, em seguida, trocar a partição com dados para uma partição vazia de outra tabela (ver exemplos [ALTER TABLE).](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

Para tabelas não participantes, considere utilizar um CTAS para escrever os dados que pretende manter numa tabela em vez de utilizar DELETE.  Se um CTAS demorar o mesmo tempo, é uma operação muito mais segura, uma vez que tem registo de transações mínimos e pode ser cancelada rapidamente, se necessário.

Consulte também [as transações de compreensão](sql-data-warehouse-develop-transactions.md), [Otimização de transações,](sql-data-warehouse-develop-best-practices-transactions.md) [divisão de tabelas,](sql-data-warehouse-tables-partition.md) [TABELA TRUNCATE,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [TABELA ALTER,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [Criar tabela como selecionado (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Reduzir tamanhos de resultados de consulta

Este passo ajuda-o a evitar problemas do lado do cliente causados por um grande resultado de consulta.  Pode editar a sua consulta para reduzir o número de linhas devolvidas. Algumas ferramentas de geração de consultas permitem-lhe adicionar sintaxe "top N" a cada consulta.  Também pode CETAS o resultado da consulta para uma tabela temporária e, em seguida, utilizar a exportação polyBase para o processamento de nível baixo.

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna

Ao definir o seu DDL, a utilização do menor tipo de dados que irá suportar os seus dados melhorará o desempenho da consulta.  Esta abordagem é particularmente importante para as colunas CHAR e VARCHAR.  

Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Consulte também [a visão geral da tabela,](sql-data-warehouse-tables-overview.md) [tipos de dados de tabela,](sql-data-warehouse-tables-data-types.md) [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilize tabelas temporárias de área dinâmica para dados em dados transitórios

Quando estiver temporariamente a aterrar dados, poderá descobrir que usar uma tabela de amontoados tornará o processo global mais rápido.  Se estiver a carregar dados apenas para testá-los antes de executar mais transformações, será muito mais rápido carregar a tabela para a tabela de área dinâmica para dados, do que carregar os dados para uma tabela columnstore em cluster.  

Além disso, carregar dados para uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela para o armazenamento permanente.  As tabelas temporárias começam com um "#" e só são acessíveis pela sessão que a criou, pelo que só podem funcionar em cenários limitados.

As tabelas de área dinâmica para dados são definidas na cláusula WITH de uma CREATE TABLE.  Se utilizar uma tabela temporária, não se esqueça de também criar estatísticas nessa tabela temporária.

Consulte também [tabelas temporárias,](sql-data-warehouse-tables-temporary.md) [CRIE TABELA,](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [CRIE TABELA COMO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster

Os índices de lojas de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados numa piscina DE SQL dedicada.  Por predefinição, as tabelas na piscina SQL dedicada são criadas como Clustered ColumnStore.  Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  

Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  Consulte as [causas da má qualidade do índice de colunas](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) no artigo [índices](sql-data-warehouse-tables-index.md) de tabela para instruções passo a passo sobre a deteção e melhoria da qualidade do segmento para tabelas de colunas agrupadas.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs de utilizadores que estão na classe de recursos médios ou grandes para carregar dados. A utilização de unidades de [armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

Uma vez que as tabelas de lojas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimidos até que haja mais de 1 milhão de linhas por tabela e cada mesa de bilhar SQL dedicada é dividida em 60 mesas, como regra do polegar, as mesas de loja de colunas não beneficiarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  Para tabelas com menos de 60 milhões de linhas, pode não fazer qualquer sentido ter um índice columnstore.  Também poderá não prejudicar.  

Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma mesa tiver 100 divisórias, terá de ter pelo menos 6 mil milhões de linhas para beneficiar de uma loja de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas).  

Se a sua tabela não tiver 6 mil milhões de linhas neste exemplo, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados.  Também poderá ser útil experimentar, para ver se consegue obter um melhor desempenho com uma tabela de área dinâmica para dados com índices secundários, em vez de uma tabela columnstore.

> [!TIP]
> Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Consulte também [Índices de tabela](sql-data-warehouse-tables-index.md), [Guia de índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Reconstruir índices columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilize a classe de recursos maior para melhorar o desempenho da consulta

O pool de SQL dedicado usa grupos de recursos como forma de alocar a memória a consultas.  Fora da caixa, todos os utilizadores são atribuídos à classe de pequenos recursos, que concede 100 MB de memória por distribuição.  Uma vez que existem sempre 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total é de 6000 MB ou apenas abaixo de 6 GB em todo o sistema.  

Algumas consultas, como associações grandes ou carregamentos para tabelas columnstore em cluster, irão beneficiar de alocações de memória superiores.  Algumas consultas, como as sondagens puras, não darão nenhum benefício.  No entanto, a utilização de classes de recursos maiores reduz a convívio, pelo que deverá ter em consideração este impacto antes de transferir todos os seus utilizadores para uma grande classe de recursos.

Consulte também [as classes de recursos para a gestão da carga de trabalho.](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Utilize a Classe de Recursos Mais Pequena para Aumentar a Simultaneidade

Se notar que as consultas de utilizador parecem ter um longo atraso, pode ser que os seus utilizadores estejam a executar em classes de recursos maiores e estejam a consumir muitas faixas de conuncy, fazendo com que outras consultas façam fila.  Para ver se as consultas dos utilizadores estão colocadas em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se são devolvidas linhas.

Consulte também [as classes de recursos para gestão da carga de trabalho,](resource-classes-for-workload-management.md) [sys.dm_pdw_waits.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="other-resources"></a>Outros recursos

Consulte também o nosso artigo [Resolução de problemas](sql-data-warehouse-troubleshoot.md), para ver problemas e soluções comuns.

Se não encontrou o que procura neste artigo, tente usar a "Procura de docs" no lado esquerdo desta página para pesquisar todos os documentos da Azure Synapse.  O [Microsoft Q&Uma página de perguntas para o Azure Synapse](/answers/topics/azure-synapse-analytics.html) é um local para publicar perguntas a outros utilizadores e ao Grupo de Produtos Azure Synapse. Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  

Se preferir fazer as suas perguntas sobre Stack Overflow, também temos um [Azure Synapse Stack Overflow Forum.](https://stackoverflow.com/questions/tagged/azure-sqldw)

Utilize a página de feedback da [Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) para fazer pedidos de funcionalidades.  Adicionar os seus pedidos ou votar noutros pedidos que realmente nos ajudam a priorizar funcionalidades.