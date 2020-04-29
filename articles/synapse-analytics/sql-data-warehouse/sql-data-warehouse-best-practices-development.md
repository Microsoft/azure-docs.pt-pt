---
title: Boas práticas de desenvolvimento
description: Recomendações e boas práticas que deve conhecer ao desenvolver soluções para piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9c4f08b143ab4a0d3e780f68f8d5ab823d4eae12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745361"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>As melhores práticas de desenvolvimento para piscina Synapse SQL

Este artigo descreve orientações e boas práticas à medida que desenvolve a sua solução de piscina SQL.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Desempenho de consulta de sintonização com novas melhorias do produto

- [Otimização do desempenho com vistas materializadas](performance-tuning-materialized-views.md)
- [Otimização do desempenho com índice columnstore em cluster ordenado](performance-tuning-ordered-cci.md)
- [Otimização do desempenho com a colocação em cache dos resultados](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução dos custos através da pausa e escalagem, consulte o artigo [Manage compute.](sql-data-warehouse-manage-compute-overview.md)

## <a name="maintain-statistics"></a>Manter as estatísticas

O pool SQL pode ser configurado para detetar e criar automaticamente estatísticas sobre colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  

Recomendamos que AUTO_CREATE_STATISTICS para as suas bases de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas sobre as colunas utilizadas nas suas consultas estão sempre atualizadas.

Se descobrir que está a demorar muito tempo a atualizar todas as suas estatísticas, talvez queira tentar ser mais seletiva sobre quais as colunas que precisam de atualizações estatísticas frequentes. Por exemplo, deve atualizar as colunas de data, onde podem ser adicionados diariamente novos valores.

> [!TIP]
> Você ganhará o maior benefício com estatísticas atualizadas sobre colunas envolvidas em juntas, colunas utilizadas na cláusula WHERE, e colunas encontradas no GRUPO BY.

Ver também Gerir estatísticas de [tabelas,](sql-data-warehouse-tables-statistics.md) [CRIAR ESTATÍSTICAS,](sql-data-warehouse-tables-statistics.md)e [ATUALIZAR ESTATÍSTICAS.](sql-data-warehouse-tables-statistics.md#update-statistics)

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash

Por predefinição, as tabelas são distribuídas por Round Robin.  Este design facilita que os utilizadores possam começar a criar tabelas sem terem de decidir como as suas tabelas devem ser distribuídas.  

As tabelas Round Robin podem ter um desempenho suficiente para algumas cargas de trabalho, mas, na maioria dos casos, selecionar uma coluna de distribuição irá proporcionar um desempenho muito melhor.  O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  

Por exemplo, se tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando associa a tabela de pedidos à tabela de transações em order_id, esta consulta torna-se uma consulta pass-through, o que significa que podemos eliminar operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.  

Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  Os artigos que se seguem dão mais detalhes sobre a melhoria do desempenho selecionando uma coluna de distribuição e como definir uma tabela distribuída na cláusula COM da sua declaração CREATE TABLES.

Ver também [visão geral da tabela,](sql-data-warehouse-tables-overview.md)distribuição de [tabelas,](sql-data-warehouse-tables-distribute.md) [seleção de tabelas,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)MESA DE [CRIAÇÃO,](sql-data-warehouse-tables-overview.md)e CRIAR TABELA COMO [SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Não crie partições em demasia

Embora a partilha de dados possa ser eficaz para manter os seus dados através da troca de divisórias ou otimização de digitalizações com a eliminação da divisória, ter demasiadas divisórias pode abrandar as suas consultas.  

Frequentemente, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem na piscina SQL.  Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas.  

Tenha em mente que nos bastidores, a SQL partilha os seus dados em 60 bases de dados, por isso, se criar uma tabela com 100 divisórias, isto resulta em 6000 divisórias.  Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  

> [!TIP]
> Considere usar uma granularidade inferior à que possa ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Ver também [partição de mesa](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação

As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se tiver um INSERT, que espera demorar 1 hora, se possível, separe o INSirem em quatro partes, que funcionarão cada uma em 15 minutos.  Aproveite as caixas especiais de registo mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT a mesas vazias, para reduzir o risco de retrocesso.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  

Para tabelas não partitivas, considere utilizar um CTAS para escrever os dados que pretende manter numa tabela em vez de utilizar o DELETE.  Se um CTAS demorar o mesmo tempo, é uma operação muito mais segura para ser executada, uma vez que tem o mínimo de registo de transações e pode ser cancelado rapidamente, se necessário.

Ver também [Compreender as transações,](sql-data-warehouse-develop-transactions.md) [otimizar transações,](sql-data-warehouse-develop-best-practices-transactions.md) [partição em tabela,](sql-data-warehouse-tables-partition.md) [Tabela TRUNCATE,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [ALTER TABLE,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [Criar tabela como selecionado (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna

Ao definir o seu DDL, utilizando o menor tipo de dados que irá suportar os seus dados irá melhorar o desempenho da consulta.  Esta abordagem é especialmente importante para as colunas CHAR e VARCHAR.  

Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Consulte também [a visão geral da tabela,](sql-data-warehouse-tables-overview.md)os tipos de dados da [tabela](sql-data-warehouse-tables-data-types.md)e a [TABELA CREATE](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster

Os índices de lojas de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados em piscina SQL.  Por padrão, as tabelas na piscina SQL são criadas como ColumnStore Clustered.  

> [!NOTE]
> Para obter um desempenho ideal para consultas em tabelas de lojas de colunas, ter boa qualidade de segmento é importante.  

Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  

Consulte as [causas da má qualidade do índice de colunas](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) no artigo [dos índices](sql-data-warehouse-tables-index.md) tabela para instruções passo a passo sobre a deteção e melhoria da qualidade do segmento para tabelas de colunas agrupadas.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs dos utilizadores que estão na classe média ou grande de recursos para carregar dados. A utilização de [unidades](what-is-a-data-warehouse-unit-dwu-cdwu.md) de armazém de dados mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

Uma vez que as tabelas de lojas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimido até que existam mais de 1 milhão de linhas por mesa e cada mesa de bilhar SQL é dividida em 60 tabelas, geralmente, as tabelas de lojas de colunas não beneficiarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  

Para uma tabela com menos de 60 milhões de linhas, pode não fazer sentido ter um índice de colunas.  Também poderá não prejudicar.  

Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma tabela tiver 100 divisórias, então terá de ter pelo menos 6 mil milhões de filas para beneficiar de uma loja de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas).  

Se a sua tabela não tiver 6 mil milhões de linhas neste exemplo, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados.  Também poderá ser útil experimentar, para ver se consegue obter um melhor desempenho com uma tabela de área dinâmica para dados com índices secundários, em vez de uma tabela columnstore.

> [!TIP]
> Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Consulte também índices de [tabela,](sql-data-warehouse-tables-index.md) [guia de índices de colunas](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e índices de [reconstrução de colunas.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="next-steps"></a>Passos seguintes

Se não encontrar o que procura neste artigo, tente usar a "Search for docs" no lado esquerdo desta página para pesquisar todos os documentos azure synapse.  

O [Azure Synapse Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um local para você publicar perguntas a outros utilizadores e ao Grupo de Produtos Azure Synapse.  Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  

Se preferir colocar as suas questões no Stack Overflow, também temos um [Fórum do Stack Overflow do Azure SQL Data Warehouse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Utilize a página [de Feedback Do Synapse Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) para fazer pedidos de funcionalidades.  Adicionar os seus pedidos ou votar noutros pedidos que realmente nos ajudam a priorizar funcionalidades.
