---
title: Tabelas de indexação
description: Recomendações e exemplos para tabelas de indexação em piscinas SQL dedicadas.
services: synapse-analytics
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/16/2021
author: XiaoyuMSFT
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 58f3eed8b16ff3ed02c6dfac6dc7d72ebb4ca374
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599983"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Indexação de mesas de piscinas SQL dedicadas no Azure Synapse Analytics

Recomendações e exemplos para tabelas de indexação em piscinas SQL dedicadas.

## <a name="index-types"></a>Tipos de índice

O pool de SQL dedicado oferece várias opções de indexação, incluindo [índices de lojas de colunas agrupados,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [índices agrupados e índices não classificados,](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e uma opção não indexada também conhecida como [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  

Para criar uma tabela com um índice, consulte a documentação [CREATE TABLE (pool DE SQL dedicado).](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="clustered-columnstore-indexes"></a>Índices de loja de colunas agrupadas

Por predefinição, o pool SQL dedicado cria um índice de loja de colunas agrupado quando não são especificadas opções de índice numa tabela. As tabelas de lojas de colunas agrupadas oferecem tanto o mais alto nível de compressão de dados como o melhor desempenho geral da consulta.  As tabelas de lojas de colunas agrupadas geralmente superam o índice agrupado ou as tabelas de pilhas e são geralmente a melhor escolha para grandes tabelas.  Por estas razões, a loja de colunas agrupada é o melhor lugar para começar quando não tem a certeza de como indexar a sua tabela.  

Para criar uma tabela de loja de colunas agrupada, basta especificar o ÍNDICE DE LOJA DE COLUNAS AGRUPADAS na cláusula COM ou deixar a cláusula COM desligada:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existem alguns cenários em que a loja de colunas agrupada pode não ser uma boa opção:

- As mesas de loja de colunas não suportam varchar(máx), nvarchar (máx) e varbinário(máx). Considere o heap ou índice agrupado em vez disso.
- As tabelas de lojas de colunas podem ser menos eficientes para dados transitórios. Considere montes e talvez até mesas temporárias.
- Mesas pequenas com menos de 60 milhões de filas. Considere mesas de amontoados.

## <a name="heap-tables"></a>Mesas de pilha

Quando você está temporariamente a aterrar dados em pool SQL dedicado, você pode descobrir que usar uma mesa de amontoado torna o processo geral mais rápido. Isto porque as cargas em pilhas são mais rápidas do que para indexar tabelas e em alguns casos a leitura subsequente pode ser feita a partir de cache.  Se estiver a carregar dados apenas para os encenar antes de executar mais transformações, carregar a tabela para a mesa de montes é muito mais rápido do que carregar os dados para uma tabela de colunas agrupadas. Além disso, os dados de carregamento para uma [tabela temporária](sql-data-warehouse-tables-temporary.md) carregam mais rapidamente do que carregar uma mesa para armazenamento permanente.  Após o carregamento de dados, pode criar índices na tabela para um desempenho de consulta mais rápido.  

As tabelas de lojas de colunas de cluster começam a obter uma compressão ótima uma vez que há mais de 60 milhões de linhas.  Para pequenas tabelas de procura, menos de 60 milhões de linhas, considere usar o índice HEAP ou clustered para um desempenho de consulta mais rápido. 

Para criar uma tabela de pilhas, basta especificar HEAP na cláusula WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices agrupados e não agrupados

Os índices agrupados podem superar as tabelas de lojas de colunas agrupadas quando uma única linha precisa de ser rapidamente recuperada. Para consultas em que uma única ou muito poucas filas de procura é necessária para executar com velocidade extrema, considere um índice agrupado ou um índice secundário não aglomerado. A desvantagem de usar um índice agrupado é que apenas as consultas que beneficiam são as que usam um filtro altamente seletivo na coluna de índice agrupado. Para melhorar o filtro noutras colunas, um índice não aglomerado pode ser adicionado a outras colunas. No entanto, cada índice que é adicionado a uma tabela adiciona tanto espaço como tempo de processamento a cargas.

Para criar uma tabela de índices agrupados, basta especificar o ÍNDICE CLUSTERD na cláusula COM:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice não agrupado numa tabela, utilize a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Otimização de índices de lojas de colunas agrupadas

As tabelas de lojas de colunas agrupadas são organizadas em dados em segmentos.  Ter alta qualidade de segmento é fundamental para alcançar o melhor desempenho de consulta numa tabela de colunas.  A qualidade do segmento pode ser medida pelo número de linhas num grupo de linha comprimido.  A qualidade do segmento é a mais ideal onde há pelo menos 100 mil linhas por grupo de linha comprimido e ganha em desempenho à medida que o número de linhas por linha de grupo se aproxima 1.048.576 linhas, que é o grupo mais linhas que um grupo de linha pode conter.

A vista abaixo pode ser criada e usada no seu sistema para calcular as linhas médias por grupo de linha e identificar quaisquer índices de loja de colunas de cluster sub-ideais.  A última coluna desta vista gera uma declaração SQL que pode ser usada para reconstruir os seus índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Agora que criou a vista, faça esta consulta para identificar tabelas com grupos de linha com menos de 100 mil linhas. Claro que pode querer aumentar o limiar de 100 mil se estiver à procura de uma qualidade de segmento mais ótima.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Depois de ter feito a consulta, pode começar a olhar para os dados e analisar os seus resultados. Esta tabela explica o que procurar na sua análise de grupo de linha.

| Coluna | Como utilizar estes dados |
| --- | --- |
| [table_partition_count] |Se a tabela estiver dividida, então pode esperar ver uma contagem mais alta do grupo Open row. Cada divisória na distribuição poderia, em teoria, ter um grupo de linha aberta associado a ela. Considere isto na sua análise. Uma pequena tabela que foi dividida poderia ser otimizada removendo completamente a partição, uma vez que isso melhoraria a compressão. |
| [row_count_total] |Contagem total de filas para a mesa. Por exemplo, pode utilizar este valor para calcular a percentagem de linhas no estado comprimido. |
| [row_count_per_distribution_MAX] |Se todas as linhas forem distribuídas uniformemente, este valor será o número-alvo de linhas por distribuição. Compare este valor com o compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Número total de linhas em formato de loja de colunas para a tabela. |
| [COMPRESSED_rowgroup_rows_AVG] |Se o número médio de linhas for significativamente inferior ao máximo de linhas para um grupo de linhas, então considere usar CTAS ou ALTER INDEX REBUILD para recomprimir os dados |
| [COMPRESSED_rowgroup_count] |Número de grupos de linha no formato de loja de colunas. Se este número for muito elevado em relação à tabela, é um indicador de que a densidade da loja de colunas é baixa. |
| [COMPRESSED_rowgroup_rows_DELETED] |As linhas são logicamente eliminadas no formato de loja de colunas. Se o número for elevado em relação ao tamanho da mesa, considere recriar a partição ou reconstruir o índice à medida que este os remove fisicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Utilize-o em conjunto com as colunas AVG e MAX para compreender o alcance dos valores dos grupos de linha na sua loja de colunas. Um número baixo acima do limiar de carga (102.400 por distribuição alinhada de partição) sugere que as otimizações estão disponíveis na carga de dados |
| [COMPRESSED_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_count] |Os grupos de linha aberta estão normais. Seria de esperar razoavelmente um grupo de linha ABERTA por distribuição de mesa (60). Números excessivos sugerem que os dados carregam em divisórias. Verifique duas vezes a estratégia de partição para se certificar de que está bem |
| [OPEN_rowgroup_rows] |Cada grupo de linha pode ter 1.048.576 linhas nele como um máximo. Use este valor para ver como estão atualmente os grupos de linha aberta |
| [OPEN_rowgroup_rows_MIN] |Grupos abertos indicam que os dados estão a ser carregados na tabela ou que a carga anterior derramou sobre as restantes linhas neste grupo de linha. Utilize as colunas MIN, MAX, AVG para ver a quantidade de dados que existem em grupos de linhas ABERTAs. Para mesas pequenas pode ser 100% de todos os dados! Nesse caso, ALTER INDEX REBUILD para forçar os dados à loja de colunas. |
| [OPEN_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_rows_AVG] |Como acima |
| [CLOSED_rowgroup_rows] |Olhe para as filas de grupo fechadas como uma verificação de sanidade. |
| [CLOSED_rowgroup_count] |O número de grupos de linhas fechadas deve ser baixo se algum for visto. Os grupos de linha fechados podem ser convertidos em grupos de linha comprimidos utilizando o ÍNDICE ALTER ... Reorganizar o comando. No entanto, isto não é normalmente necessário. Os grupos fechados são automaticamente convertidos em grupos de fila de colunas pelo processo de "tuple mover" de fundo. |
| [CLOSED_rowgroup_rows_MIN] |Os grupos de linhas fechadas devem ter uma taxa de enchimento muito elevada. Se a taxa de preenchimento de um grupo de linha fechada for baixa, então é necessária uma análise mais aprofundada da loja de colunas. |
| [CLOSED_rowgroup_rows_MAX] |Como acima |
| [CLOSED_rowgroup_rows_AVG] |Como acima |
| [Rebuild_Index_SQL] |SQL para reconstruir índice de loja de colunas para uma tabela |

## <a name="impact-of-index-maintenance"></a>Impacto da manutenção do índice

A coluna `Rebuild_Index_SQL` na vista contém uma declaração que pode ser usada para reconstruir os seus `vColumnstoreDensity` `ALTER INDEX REBUILD` índices. Ao reconstruir os seus índices, certifique-se de que aloca memória suficiente à sessão que reconstrói o seu índice. Para isso, aumente a classe de [recursos](resource-classes-for-workload-management.md) de um utilizador que tenha permissões para reconstruir o índice nesta tabela ao mínimo recomendado. Por exemplo, consulte [índices de reconstrução para melhorar a qualidade](#rebuilding-indexes-to-improve-segment-quality) do segmento mais tarde neste artigo.

Para uma tabela com um índice de loja de colunas agrupado encomendado, `ALTER INDEX REBUILD` irá reencaminhar os dados usando o temporário. Monitora temperatura durante as operações de reconstrução. Se precisar de mais espaço temporário, escale a base de dados. Reduza para baixo assim que o índice estiver concluído.

Para uma tabela com um índice de loja de colunas agrupado encomendado, `ALTER INDEX REORGANIZE` não reordena os dados. Para reenerdenar os dados, utilize `ALTER INDEX REBUILD` .

Para obter mais informações sobre índices de lojas de colunas agrupados ordenados, consulte [a sintonização de desempenho com índice de colunas agrupados ordenado](performance-tuning-ordered-cci.md).

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de fraca qualidade do índice columnstore

Se identificou tabelas com má qualidade de segmento, quer identificar a causa principal.  Abaixo estão algumas outras causas comuns de má qualidade do segmento:

1. Pressão da memória quando o índice foi construído
2. Elevado volume de operações de DML
3. Operações de carga pequenas ou gotas
4. Demasiadas divisórias

Estes fatores podem fazer com que um índice de loja de colunas tenha significativamente menos do que o grupo ideal de 1 milhão de linhas por linha. Também podem fazer com que as filas vão para o grupo delta row em vez de um grupo de linha comprimido.

### <a name="memory-pressure-when-index-was-built"></a>Pressão da memória quando o índice foi construído

O número de linhas por grupo de linha comprimido está diretamente relacionado com a largura da linha e a quantidade de memória disponível para processar o grupo de linha.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  Portanto, a melhor prática é dar à sessão que está escrevendo para as suas tabelas de índices de loja de colunas acesso ao máximo de memória possível.  Uma vez que existe uma compensação entre a memória e a concordância, a orientação sobre a atribuição de memória certa depende dos dados de cada linha da sua tabela, das unidades de armazém de dados atribuídas ao seu sistema e do número de faixas horárias de concordância que pode dar à sessão que está a escrever dados para a sua mesa.

### <a name="high-volume-of-dml-operations"></a>Elevado volume de operações de DML

Um grande volume de operações DML que atualizam e apagam linhas podem introduzir ineficiência na loja de colunas. Isto é especialmente verdade quando a maioria das linhas seguidas são modificadas.

- Apagar uma linha de um grupo de linha comprimido apenas marca logicamente a linha como eliminada. A linha permanece no grupo de linha comprimido até que a divisória ou a mesa sejam reconstruídas.
- A inserção de uma linha adiciona a linha a uma tabela interna de lojas chamada grupo delta row. A linha inserida não é convertida em loja de colunas até que o grupo da linha delta esteja cheio e esteja marcado como fechado. Os grupos de linha são fechados assim que atingem a capacidade máxima de 1.048.576 linhas.
- A atualização de uma linha no formato de loja de colunas é processada como uma eliminação lógica e, em seguida, uma inserção. A linha inserida pode ser armazenada na loja delta.

As operações de atualização e inserção em lote que excedam o limiar de 102.400 linhas por distribuição alinhada com divisórias vão diretamente para o formato da loja de colunas. No entanto, assumindo uma distribuição uniforme, seria necessário modificar mais de 6,144 milhões de linhas numa única operação para que isso acontecesse. Se o número de linhas para uma determinada distribuição alinhada com divisórias for inferior a 102.400, então as linhas vão para a loja delta e permanecem lá até que tenham sido inseridas ou modificadas filas suficientes para fechar o grupo de linha ou o índice tenha sido reconstruído.

### <a name="small-or-trickle-load-operations"></a>Operações de carga pequenas ou gotas

Pequenas cargas que fluem para piscinas SQL dedicadas também são por vezes conhecidas como cargas gota-a-gota. Normalmente representam um fluxo quase constante de dados que estão a ser ingeridos pelo sistema. No entanto, como este fluxo está perto de ser contínuo, o volume de linhas não é particularmente grande. Mais frequentemente do que não, os dados estão significativamente abaixo do limiar necessário para uma carga direta para o formato de loja de colunas.

Nestas situações, é muitas vezes melhor aterrar os dados primeiro no armazenamento de bolhas Azure e deixá-los acumular antes de carregar. Esta técnica é frequentemente conhecida como *micro-lote.*

### <a name="too-many-partitions"></a>Demasiadas divisórias

Outra coisa a considerar é o impacto da partição nas suas mesas de colunas agrupadas.  Antes da partilha, o pool de SQL dedicado já divide os seus dados em 60 bases de dados.  A partilha divide ainda mais os seus dados.  Se dividir os seus dados, considere que **cada** partição precisa de pelo menos 1 milhão de linhas para beneficiar de um índice de colunas agrupado.  Se dividir a sua mesa em 100 divisórias, então a sua mesa precisa de pelo menos 6 mil milhões de linhas para beneficiar de um índice de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas). Se a sua mesa de 100 divisórias não tiver 6 mil milhões de linhas, reduza o número de divisórias ou considere usar uma mesa de amontoados.

Uma vez que as suas tabelas tenham sido carregadas com alguns dados, siga os passos abaixo para identificar e reconstruir tabelas com índices de colunas sub-ideais agrupados.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Reconstroem índices para melhorar a qualidade do segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Passo 1: Identificar ou criar um utilizador que utilize a classe de recursos certa

Uma forma rápida de melhorar imediatamente a qualidade do segmento é reconstruir o índice.  O SQL devolvido pela vista acima devolve uma declaração de RECONSTRUÇÃO DO ÍNDICE ALTER que pode ser usada para reconstruir os seus índices. Ao reconstruir os seus índices, certifique-se de que aloca memória suficiente à sessão que reconstrói o seu índice. Para isso, aumente a classe de recursos de um utilizador que tenha permissões para reconstruir o índice nesta tabela ao mínimo recomendado.

Abaixo está um exemplo de como alocar mais memória a um utilizador aumentando a sua classe de recursos. Para trabalhar com aulas de recursos, consulte [as aulas de Recursos para gestão da carga de trabalho.](resource-classes-for-workload-management.md)

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser';
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Passo 2: Reconstruir índices de lojas de colunas agrupadas com utilizador de classe de recursos mais elevados

Inscreva-se como utilizador do passo 1 (LoadUser), que está agora a utilizar uma classe de recursos mais elevada, e execute as declarações DO ALTER INDEX. Certifique-se de que este utilizador tem permissão ALTER para as tabelas onde o índice está a ser reconstruído. Estes exemplos mostram como reconstruir todo o índice de loja de colunas ou como reconstruir uma única partição. Em mesas grandes, é mais prático reconstruir índices de uma única divisória de cada vez.

Em alternativa, em vez de reconstruir o índice, pode copiar a tabela para uma nova tabela [utilizando CTAS](sql-data-warehouse-develop-ctas.md). Qual é o melhor caminho? Para grandes volumes de dados, o CTAS é geralmente mais rápido que [o ÍNDICE ALTER](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Para volumes menores de dados, o ALTER INDEX é mais fácil de utilizar e não exigirá que troque a tabela.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5;
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE);
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE);
```

Reconstruir um índice em pool SQL dedicado é uma operação offline.  Para obter mais informações sobre os índices de reconstrução, consulte a secção DE RECONSTRUÇÃO DO ÍNDICE DE ALTERAÇÃO na [Desfragmentação de Índices de Colunas](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Passo 3: Verificar se a qualidade do segmento de colunas agrupadas melhorou

Repercuta a consulta que identificou a tabela com má qualidade do segmento e verifique se a qualidade do segmento melhorou.  Se a qualidade do segmento não melhorou, pode ser que as linhas na sua mesa sejam extra largas.  Considere usar uma classe de recursos mais elevado ou DWU ao reconstruir os seus índices.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Índices de reconstrução com CTAS e comutação de divisórias

Este exemplo utiliza a declaração [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e a comutação de divisórias para reconstruir uma divisória de mesa.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Para obter mais informações sobre a recriação de divisórias utilizando CTAS, consulte [utilizar divisórias em piscina SQL dedicada.](sql-data-warehouse-tables-partition.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento de tabelas, consulte [tabelas de desenvolvimento.](sql-data-warehouse-tables-overview.md)
