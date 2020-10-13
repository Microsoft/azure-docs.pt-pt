---
title: Melhores práticas de desenvolvimento
description: Recomendações e boas práticas que deve conhecer à medida que desenvolve soluções para a piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f8db19b209ddefebd4a297f18fb9b178fcf40f21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89457911"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Melhores práticas de desenvolvimento para piscina Sinapse SQL

Este artigo descreve orientação e boas práticas à medida que desenvolve a sua solução de piscina SQL.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajuste o desempenho da consulta com novos melhoramentos de produtos

- [Otimização do desempenho com vistas materializadas](performance-tuning-materialized-views.md)
- [Otimização do desempenho com índice columnstore em cluster ordenado](performance-tuning-ordered-cci.md)
- [Otimização do desempenho com a colocação em cache dos resultados](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir os custos com a colocação em pausa e o dimensionamento

Para obter mais informações sobre a redução de custos através de pausas e dimensionamento, consulte o artigo [de cálculo Gerir.](sql-data-warehouse-manage-compute-overview.md)

## <a name="maintain-statistics"></a>Manter as estatísticas

A piscina SQL pode ser configurada para detetar e criar automaticamente estatísticas sobre colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  

Recomendamos que ative AUTO_CREATE_STATISTICS para as suas bases de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas sobre colunas utilizadas nas suas consultas estão sempre atualizadas.

Se descobrir que está a demorar muito tempo a atualizar todas as suas estatísticas, talvez queira tentar ser mais seletivo sobre quais colunas precisam de atualizações estatísticas frequentes. Por exemplo, deve atualizar as colunas de data, onde podem ser adicionados diariamente novos valores.

> [!TIP]
> Obterá o maior benefício ao ter estatísticas atualizadas sobre colunas envolvidas em junções, colunas utilizadas na cláusula WHERE e colunas encontradas no GROUP BY.

Consulte também [estatísticas de tabelas,](sql-data-warehouse-tables-statistics.md) [CRIAR ESTATÍSTICAS](sql-data-warehouse-tables-statistics.md)e ESTATÍSTICAS [DE ATUALIZAÇÃO](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Distribuir tabelas grandes por hash

Por predefinição, as tabelas são distribuídas por Round Robin.  Este design facilita a criação de tabelas sem ter de decidir como as suas tabelas devem ser distribuídas.  

As tabelas Round Robin podem ter um desempenho suficiente para algumas cargas de trabalho, mas, na maioria dos casos, selecionar uma coluna de distribuição irá proporcionar um desempenho muito melhor.  O exemplo mais comum de quando uma tabela distribuída por uma coluna supera de longe uma tabela Round Robin é quando duas tabelas de factos grandes são associadas.  

Por exemplo, se tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando associa a tabela de pedidos à tabela de transações em order_id, esta consulta torna-se uma consulta pass-through, o que significa que podemos eliminar operações de movimento de dados.  Menos passos significam uma consulta mais rápida.  Menos movimento de dados também torna as consultas mais rápidas.  

Ao carregar uma tabela distribuída, certifique-se de que os dados recebidos não estão ordenados na chave de distribuição, uma vez que isto desacelera os carregamentos.  Os artigos que se seguem dão mais detalhes sobre a melhoria do desempenho selecionando uma coluna de distribuição e como definir uma tabela distribuída na cláusula WITH da sua declaração CREATE TABLES.

Ver também [Tabela Geral,](sql-data-warehouse-tables-overview.md) [Distribuição de Tabelas](sql-data-warehouse-tables-distribute.md), [Seleção de distribuição de tabelas,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)TABELA [CREATE](sql-data-warehouse-tables-overview.md)e CRIAR TABELA [COMO SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Não crie partições em demasia

Embora os dados de partição possam ser eficazes para manter os seus dados através da comutação de partição ou otimização de digitalizações com eliminação de divisórias, ter demasiadas divisórias pode abrandar as suas consultas.  

Frequentemente, uma estratégia de partição de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem na piscina SQL.  Ter demasiadas partições pode também reduzir a eficácia de índices columnstore em cluster se cada partição tiver menos de 1 milhão de linhas.  

Tenha em mente que nos bastidores, a SQL partilha os seus dados em 60 bases de dados, por isso, se criar uma tabela com 100 divisórias, isso resulta em 6000 divisórias.  Cada carga de trabalho é diferente, pelo que o melhor conselho é experimentar o particionamento para ver o que funciona melhor para a sua carga de trabalho.  

> [!TIP]
> Considere usar uma granularidade inferior à que pode ter funcionado para si no SQL Server.  Por exemplo, considere utilizar partições semanais ou mensais, em vez de partições diárias.

Consulte também [a divisão da tabela.](sql-data-warehouse-tables-partition.md)

## <a name="minimize-transaction-sizes"></a>Minimizar tamanhos de transação

As instruções INSERT, UPDATE e DELETE são executadas numa transação e quando falham têm de ser revertidas.  Para reduzir a probabilidade uma reversão longa, minimize os tamanhos de transação sempre que possível.  Isto pode ser realizado ao dividir as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se tiver um INSERT, que espera levar 1 hora, se possível, desa parte o INSERT em quatro partes, que cada uma funciona em 15 minutos.  Aproveite os casos especiais de registo mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT para mesas vazias, para reduzir o risco de reversão.  

Outra forma de eliminar reversões consiste em utilizar Operações Apenas de Metadados, como a mudança de partições para a gestão de dados.  Por exemplo, em vez de executar uma instrução DELETE para eliminar todas as linhas numa tabela onde a order_date estava em outubro de 2001, pode dividir os dados mensalmente e, em seguida, mudar a partição com dados para uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  

Para tabelas não participantes, considere utilizar um CTAS para escrever os dados que pretende manter numa tabela em vez de utilizar DELETE.  Se um CTAS demorar o mesmo tempo, é uma operação muito mais segura, uma vez que tem registo de transações mínimos e pode ser cancelada rapidamente, se necessário.

Consulte também [as transações de compreensão](sql-data-warehouse-develop-transactions.md), [Otimização de transações,](sql-data-warehouse-develop-best-practices-transactions.md) [divisão de tabelas,](sql-data-warehouse-tables-partition.md) [TABELA TRUNCATE,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [TABELA ALTER,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [Criar tabela como selecionado (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Utilizar o tamanho mais pequeno possível da coluna

Ao definir o seu DDL, a utilização do menor tipo de dados que irá suportar os seus dados melhorará o desempenho da consulta.  Esta abordagem é especialmente importante para as colunas CHAR e VARCHAR.  

Se o maior valor numa coluna for de 25 carateres, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de carateres com um comprimento predefinido grande.  Além disso, defina colunas como VARCHAR quando é tudo o que é necessário, em vez de utilizar NVARCHAR.

Consulte também [a visão geral da tabela,](sql-data-warehouse-tables-overview.md) [os tipos de dados de tabela](sql-data-warehouse-tables-data-types.md)e a [CRIAÇÃO DE TABELA](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore em cluster

Os índices de loja de colunas agrupados são uma das formas mais eficientes de armazenar os seus dados na piscina SQL.  Por predefinição, as tabelas na piscina SQL são criadas como Clustered ColumnStore.  

> [!NOTE]
> Para obter um desempenho ideal para consultas em tabelas de lojas de colunas, ter boa qualidade de segmento é importante.  

Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  A qualidade de segmento pode ser medida pelo número de linhas num Grupo de Linhas comprimido.  

Consulte as [causas da má qualidade do índice de colunas](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) no artigo [índices](sql-data-warehouse-tables-index.md) de tabela para instruções passo a passo sobre a deteção e melhoria da qualidade do segmento para tabelas de colunas agrupadas.  

Como os segmentos de lojas de colunas de alta qualidade são importantes, é uma boa ideia usar iDs de utilizadores que estão na classe de recursos médios ou grandes para carregar dados. A utilização de unidades de [armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) mais baixas significa que pretende atribuir uma classe de recursos maior ao utilizador de carregamento.

Uma vez que as tabelas de lojas de colunas geralmente não empurram os dados para um segmento de loja de colunas comprimidos até que haja mais de 1 milhão de linhas por tabela e cada mesa de bilhar SQL é dividida em 60 tabelas, geralmente, as mesas de loja de colunas não beneficiarão uma consulta a menos que a mesa tenha mais de 60 milhões de linhas.  

Para uma tabela com menos de 60 milhões de linhas, pode não fazer sentido ter um índice de loja de colunas.  Também poderá não prejudicar.  

Além disso, se dividir os dados, deverá ter em consideração que cada partição tem de ter 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se uma mesa tiver 100 divisórias, terá de ter pelo menos 6 mil milhões de linhas para beneficiar de uma loja de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas).  

Se a sua tabela não tiver 6 mil milhões de linhas neste exemplo, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados.  Também poderá ser útil experimentar, para ver se consegue obter um melhor desempenho com uma tabela de área dinâmica para dados com índices secundários, em vez de uma tabela columnstore.

> [!TIP]
> Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se selecionar apenas as colunas de que precisa.  

Consulte também [índices de tabela,](sql-data-warehouse-tables-index.md) [guia de índices de colunas](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [índices de loja de colunas de reconstrução](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Passos seguintes

Se não encontrar o que procura neste artigo, tente usar a "Procura de docs" no lado esquerdo desta página para pesquisar todos os documentos da Azure Synapse.  

O [Microsoft Q&Uma página de perguntas para o Azure Synapse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) é um local para publicar perguntas a outros utilizadores e ao Grupo de Produtos Azure Synapse.  Monitorizamos ativamente este fórum para nos certificarmos de que as suas perguntas são respondidas por outro utilizador ou um de nós.  

Se preferir fazer as suas perguntas sobre Stack Overflow, também temos um [Azure Synapse Analytics Stack Overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).

Utilize a página de feedback da [Sinapse Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) para fazer pedidos de funcionalidades.  Adicionar os seus pedidos ou votar noutros pedidos que realmente nos ajudam a priorizar funcionalidades.
