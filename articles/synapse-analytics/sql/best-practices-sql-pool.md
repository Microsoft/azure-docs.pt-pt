---
title: Melhores práticas para piscinas SQL dedicadas
description: Recomendações e boas práticas que deve conhecer enquanto trabalha com piscinas SQL dedicadas.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 03a536e16a6ba12611ed704b404c1bd411f0c4c8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322700"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Melhores práticas para piscinas SQL dedicadas em Azure Synapse Analytics

Este artigo fornece uma coleção de boas práticas para ajudá-lo a alcançar o melhor desempenho para piscinas SQL dedicadas em Azure Synapse Analytics. Abaixo encontrará orientações básicas e áreas importantes para se concentrar à medida que constrói a sua solução. Cada secção apresenta-lhe um conceito e, em seguida, aponta-o para artigos mais detalhados que cobrem o conceito em maior profundidade.

## <a name="dedicated-sql-pools-loading"></a>Carregamento dedicado de piscinas SQL

Para obter orientação dedicada de carregamento de piscinas SQL, consulte [orientação para os dados de carregamento.](data-loading-best-practices.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução de custos através de pausas e escalas, consulte [Manage compute](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Manter as estatísticas

Enquanto o SQL Server deteta e cria ou atualiza automaticamente estatísticas sobre colunas, as piscinas SQL dedicadas requerem a manutenção manual das estatísticas. Você vai querer manter as suas estatísticas para garantir que os planos de piscina SQL estão otimizados.  Os planos criados pelo otimizador só serão bons consoante as estatísticas disponíveis.

> [!TIP]
> Criar estatísticas de amostragem em cada coluna é uma forma fácil de começar com as estatísticas.  

É igualmente importante atualizar as estatísticas à medida que ocorrem alterações significativas nos seus dados.  Poderá realizar uma abordagem conservadora para atualizar as estatísticas diariamente ou após cada carregamento.  Há sempre uma relação entre o desempenho e o custo para criar e atualizar as estatísticas.

Para encurtar o tempo de manutenção das estatísticas, seja seletivo sobre quais colunas têm estatísticas ou precise da atualização mais frequente. Por exemplo, é possível que pretenda atualizar colunas de datas onde novos valores possam ser adicionados diariamente. Concentre-se em ter estatísticas para colunas envolvidas em junções, colunas utilizadas na cláusula WHERE e colunas encontradas no GROUP BY.

Informações adicionais sobre estatísticas podem ser encontradas nas estatísticas do [quadro gerir,](develop-tables-statistics.md) [criar estatísticas](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e atualizar artigos [estatísticos.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma carga única para uma mesa pequena com uma declaração INSERIDA, como `INSERT INTO MyLookup VALUES (1, 'Type 1')` pode ser a melhor abordagem dependendo das suas necessidades. No entanto, se precisar de carregar milhares ou milhões de linhas ao longo do dia, é provável que os Ins inserts singleton não sejam os ideais.

Uma forma de resolver este problema é desenvolver um processo que escreva para um ficheiro e, em seguida, outro processo para carregar periodicamente este ficheiro. Consulte o artigo [INSERIR](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obter mais informações.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilize o PolyBase para carregar e exportar dados rapidamente

A piscina SQL suporta dados de carregamento e exportação através de várias ferramentas, incluindo Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta pode ser suficiente para as suas necessidades.  

> [!NOTE]
> A polibase é a melhor escolha quando está a carregar ou a exportar grandes volumes de dados, ou precisa de um desempenho mais rápido.

Os carregamentos do PolyBase podem ser executados com CTAS ou INSERT INTO. O CTAS minimizará o registo de transações e é a forma mais rápida de carregar os seus dados. A Azure Data Factory também suporta cargas PolyBase e pode alcançar um desempenho semelhante ao CTAS. O PolyBase suporta vários formatos de ficheiros, incluindo ficheiros Gzip.

Para maximizar a produção ao utilizar ficheiros de texto Gzip, descole os ficheiros em 60 ou mais ficheiros para maximizar o paralelismo da sua carga. Para um débito total mais rápido, considere carregar dados em simultâneo. Informações adicionais para os tópicos relevantes para esta secção estão incluídas nos seguintes artigos:

- [Carregar dados](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Guia para utilizar o PolyBase](data-loading-best-practices.md)
- [Padrões e estratégias de carregamento de piscinaS Azure SQL](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Dados de carga com Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Mover dados com o Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Criar tabela ao selecionar (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

A base polibase não é ideal para consultas. As tabelas Polybase para piscinas SQL dedicadas suportam apenas ficheiros blob Azure e armazenamento do Lago de Dados Azure. Estes ficheiros não têm recursos de computação a apoiá-los. Como resultado, piscinas SQL dedicadas não podem descarregar este trabalho e devem ler todo o arquivo carregando-o para temperatura para que possa ler os dados.

Se tiver várias consultas para consultar estes dados, é melhor carregar estes dados uma vez e ter consultas que utilizem a tabela local. Outras orientações da Polybase estão incluídas no Guia para a utilização do artigo [PolyBase.](data-loading-best-practices.md)

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash

Por predefinição, as tabelas são distribuídas por Round Robin.   Este padrão facilita a criação de tabelas por parte dos utilizadores sem ter de decidir como devem ser distribuídas as suas tabelas. As mesas robin redondas podem funcionar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, uma coluna de distribuição proporciona um melhor desempenho.  

O exemplo mais comum de uma tabela distribuída por uma coluna que executa uma mesa Round Robin é quando duas grandes tabelas de factos são unidas.  

Por exemplo, se tiver uma tabela de encomendas distribuída por order_id, e uma tabela de transações também distribuída por order_id, quando se junta à sua tabela de encomendas à sua tabela de transações no order_id, esta consulta torna-se uma consulta de passagem. As operações de movimento de dados são então eliminadas. Menos passos significam uma consulta mais rápida. Menos movimento de dados também torna as consultas mais rápidas.

> [!NOTE]
> Ao carregar uma tabela distribuída, os seus dados de entrada não devem ser classificados na chave de distribuição. Fazê-lo vai abrandar as suas cargas.

Os links de artigos fornecidos abaixo dar-lhe-ão detalhes adicionais sobre a melhoria do desempenho através da seleção de uma coluna de distribuição. Além disso, encontrará informações sobre como definir uma tabela distribuída na cláusula COM da sua declaração DE TABELA CREATE:

- [Visão geral da tabela](develop-tables-overview.md)
- [Distribuição da tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Selecionar distribuição da tabela](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CRIAR TABELA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Não crie partições em demasia

Embora os dados de partição possam ser eficazes para manter os seus dados através da comutação de partição ou otimização de digitalizações com eliminação de divisórias, ter demasiadas divisórias pode abrandar as suas consultas.  Muitas vezes, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem em piscina SQL dedicada.  

Ter demasiadas divisórias pode reduzir a eficácia dos índices de lojas de colunas agrupadas se cada partição tiver menos de 1 milhão de linhas. pools SQL dedicados dividiam automaticamente os seus dados em 60 bases de dados. Então, se criar uma mesa com 100 divisórias, o resultado será 6000 divisórias. Cada carga de trabalho é diferente, por isso o melhor conselho é experimentar a partição para ver o que funciona melhor para a sua carga de trabalho.  

Uma opção a considerar é usar uma granularidade inferior à que implementou usando o SQL Server. Por exemplo, considere usar divisórias semanais ou mensais em vez de divisórias diárias.

Mais informações sobre a partição são detalhadas no artigo [de divisão da tabela.](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação

Inserir, atualizar e APAGAR as declarações executadas numa transação. Quando falharem, devem ser revoados. Para reduzir o potencial de um retrocesso longo, minimize os tamanhos de transação sempre que possível.  Minimizar os tamanhos de transação pode ser feito dividindo as declarações DE INSERT, UPDATE e DELETE em peças. Por exemplo, se tiver um INSERT que espera levar 1 hora, pode partir o INSERT em quatro partes. Cada corrida será então encurtada para 15 minutos.  

> [!TIP]
> Aproveite os casos especiais de registo mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para mesas vazias para reduzir o risco de reversão.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma declaração DELETE para apagar todas as linhas numa tabela onde o order_date estava em outubro de 2001, poderia dividir os seus dados mensalmente. Em seguida, pode trocar a partição com dados para uma partição vazia de outra tabela (ver exemplos ALTER TABLE).  

Para tabelas não participantes, considere utilizar um CTAS para escrever os dados que pretende manter numa tabela em vez de utilizar DELETE.  Se um CTAS demorar o mesmo tempo, é muito mais seguro de executar, uma vez que tem registo de transações mínimos e pode ser cancelado rapidamente se necessário.

Mais informações sobre o conteúdo relacionado com esta secção estão incluídas nos artigos abaixo:

- [Criar tabela ao selecionar (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Compreender as transações](develop-transactions.md)
- [Otimizar as transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Divisórias de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TABELA TRUNCADA](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [TABELA ALTER](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Reduzir tamanhos de resultados de consulta

A redução dos tamanhos dos resultados da consulta ajuda-o a evitar problemas do lado do cliente causados por grandes resultados de consultas.  Pode editar a sua consulta para reduzir o número de linhas devolvidas. Algumas ferramentas de geração de consultas permitem-lhe adicionar sintaxe "top N" a cada consulta.  Também pode CETAS o resultado da consulta para uma tabela temporária e, em seguida, utilizar a exportação polyBase para o processamento de nível baixo.

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna

Ao definir o seu DDL, utilize o menor tipo de dados que irá suportar os seus dados ao fazê-lo irá melhorar o desempenho da consulta.  Esta recomendação é particularmente importante para as colunas CHAR e VARCHAR.  Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário em vez de usar NVARCHAR.

Consulte a [visão geral](develop-tables-overview.md)do quadro, tipos de [dados de tabela,](develop-tables-data-types.md)e CRIE artigos [TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para uma revisão mais detalhada de conceitos essenciais relevantes para as informações acima referidas.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilize tabelas temporárias de área dinâmica para dados em dados transitórios

Quando você está temporariamente a aterrar dados em piscinas SQL dedicadas, as mesas de heap geralmente tornarão o processo geralmente mais rápido.  Se estiver a carregar dados apenas para os encenar antes de executar mais transformações, carregar a tabela para uma mesa de amontoados será mais rápido do que carregar os dados numa tabela de colunas agrupadas.  

Os dados de carregamento para uma tabela temporária também carregarão muito mais rapidamente do que carregar uma mesa para armazenamento permanente.  As tabelas temporárias começam com um "#" e só são acessíveis pela sessão que a criou. Consequentemente, só podem funcionar em cenários limitados. As tabelas de área dinâmica para dados são definidas na cláusula WITH de uma CREATE TABLE.  Se utilizar uma tabela temporária, não se esqueça de também criar estatísticas nessa tabela temporária.

Para obter orientações adicionais, consulte as [tabelas Temporárias,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [CRIE TABELA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e CRIE QUADRO COMO ARTIGOS [SELECIONADOS.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster

Os índices de lojas de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados numa piscina DE SQL dedicada.  Por predefinição, as tabelas na piscina SQL dedicada são criadas como Clustered ColumnStore.  Para obter o melhor desempenho das consultas em tabelas columnstore, ter uma boa qualidade de segmento é importante.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  

A qualidade do segmento pode ser medida pelo número de linhas num Grupo Row comprimido. Consulte as [causas da má qualidade do índice de colunas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) no artigo [índices](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) de tabela para instruções passo a passo sobre a deteção e melhoria da qualidade do segmento para tabelas de colunas agrupadas.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs de utilizadores que estão na classe de recursos médios ou grandes para carregar dados. A utilização de unidades de [armazém de dados](resource-consumption-models.md) mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

As tabelas de lojas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimidos até que haja mais de 1 milhão de linhas por tabela. Cada mesa de bilhar SQL dedicada é dividida em 60 mesas. Como tal, as mesas de loja de colunas não beneficiarão uma consulta a menos que a mesa tenha mais de 60 milhões de linhas.  

> [!TIP]
> Para tabelas com menos de 60 milhões de linhas, ter um índice de loja de colunas pode não ser a solução ideal.  

Se dividir os seus dados, cada partição terá de ter 1 milhão de linhas para beneficiar de um índice de colunas agrupado.  Para uma tabela com 100 divisórias, precisa de ter pelo menos 6 mil milhões de linhas para beneficiar de uma loja de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas).  

Se a tua mesa não tiver 6 biliões de filas, tens duas opções principais. Ou reduz o número de divisórias ou considera usar uma mesa de amontoados.  Também pode valer a pena experimentar para ver se um melhor desempenho pode ser obtido usando uma tabela de amontoados com índices secundários em vez de uma tabela de lojas de colunas.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  Mais informações sobre índices de tabelas e lojas de colunas e podem ser encontrados dentro dos índices de [tabela,](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [guia de índices de colunas](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [artigos de índices de loja de colunas de reconstrução.](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilize a classe de recursos maior para melhorar o desempenho da consulta

As piscinas SQL utilizam grupos de recursos como forma de alocar a memória a consultas. Inicialmente, todos os utilizadores são designados para a classe de pequenos recursos, que concede 100 MB de memória por distribuição.  Há sempre 60 distribuições. Cada distribuição é dada um mínimo de 100 MB. A alocação total de memória em todo o sistema é de 6.000 MB, ou pouco menos de 6 GB.  

Algumas consultas, como associações grandes ou carregamentos para tabelas columnstore em cluster, irão beneficiar de alocações de memória superiores.  Algumas consultas, como as sondagens puras, não verão nenhum benefício. A utilização de classes de recursos maiores tem impacto na concordância. Por isso, vai querer ter estes factos em mente antes de transferir todos os seus utilizadores para uma grande classe de recursos.

Para obter informações adicionais sobre as classes de recursos, consulte as classes de Recursos para o artigo [de gestão da carga de trabalho.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Use classe de recursos menores para aumentar a concordância

Se notar um longo atraso nas consultas dos utilizadores, os seus utilizadores podem estar a executar em classes de recursos maiores. Este cenário promove o consumo de slots de convíria, o que pode fazer com que outras consultas façam fila.  Para determinar se as consultas dos utilizadores estão em fila, corra `SELECT * FROM sys.dm_pdw_waits` para ver se alguma linha é devolvida.

As [classes de Recursos para gestão de carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md) e artigos [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) irão fornecer-lhe mais informações.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilize DMVs para monitorizar e otimizar as suas consultas

As piscinas SQL têm vários DMVs que podem ser usados para monitorizar a execução de consultas.  O artigo de monitorização abaixo acompanha-o através de instruções passo a passo sobre como ver detalhes de uma consulta de execução.  Para encontrar rapidamente as consultas nestas DMVs, pode ajudar se utilizar a opção LABEL com as suas consultas. Para obter informações detalhadas adicionais, consulte os artigos incluídos na lista abaixo:

- [Monitorizar a carga de trabalho com DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [RÓTULO](develop-label.md)
- [OPÇÃO](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Passos seguintes

Consulte também o artigo [de resolução de problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para questões e soluções comuns.

Se precisar de informações não fornecidas neste artigo, pesse na [página de perguntas do Microsoft Q&Uma página de perguntas para o Azure Synapse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) é um local para colocar questões a outros utilizadores e ao Grupo de Produtos de Piscina SQL.  

Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  Se preferir fazer as suas perguntas sobre Stack Overflow, também temos um [Azure SQL pool Stack Overflow Forum.](https://stackoverflow.com/questions/tagged/azure-sqldw)

Para pedidos de funcionalidades, utilize a página de feedback da [piscina Azure SQL.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  Adicionar os seus pedidos ou votar outros pedidos ajuda-nos a focar-nos nas funcionalidades mais procuradas.
