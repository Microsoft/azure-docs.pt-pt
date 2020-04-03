---
title: Tabelas de indexação
description: Recomendações e exemplos para tabelas de indexação na piscina SYnapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d5acc2b69ed521af4fd4777dc9f3496290078379
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583275"
---
# <a name="indexing-tables-in-synapse-sql-pool"></a>Tabelas de indexação na piscina Synapse SQL

Recomendações e exemplos para tabelas de indexação na piscina SYnapse SQL.

## <a name="index-types"></a>Tipos de índice

O pool SYnapse SQL oferece várias opções de indexação, incluindo índices de lojas de [colunas agrupadas,](/sql/relational-databases/indexes/columnstore-indexes-overview) [índices agrupados e índices não agrupados,](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)e uma opção não indexada também conhecida como [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Para criar uma tabela com um índice, consulte a documentação [CREATE TABLE (Piscina Synapse SQL).](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)

## <a name="clustered-columnstore-indexes"></a>Índices de lojas de colunas agrupadas

Por padrão, o pool SQL Synapse cria um índice de loja de colunas agrupado quando não são especificadas opções de índice numa tabela. As tabelas de lojas de colunas agrupadas oferecem tanto o nível mais alto de compressão de dados como o melhor desempenho global de consulta.  As tabelas de lojas de colunas agrupadas geralmente superam tabelas de índices ou heap agrupadas e são geralmente a melhor escolha para grandes tabelas.  Por estas razões, a loja de colunas agrupada é o melhor lugar para começar quando não tem a certeza de como indexar a sua tabela.  

Para criar uma tabela de colunas agrupada, basta especificar o ÍNDICE DE COLUMNSTORE Clustered na cláusula COM, ou deixar a cláusula COM desligada:

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

- As tabelas de colunas não suportam varchar(máx), nvarchar (máx) e varbinary (máx). Considere heap ou índice agrupado em vez disso.
- As tabelas de colunas podem ser menos eficientes para dados transitórios. Considere a pilha e talvez até mesas temporárias.
- Mesas pequenas com menos de 60 milhões de filas. Considere as mesas de heap.

## <a name="heap-tables"></a>Mesas de heap

Quando estiver temporariamente a aterrar dados na piscina SYnapse SQL, poderá descobrir que usar uma tabela de heap torna o processo global mais rápido. Isto porque as cargas em pilhas são mais rápidas do que para indexar tabelas e em alguns casos a leitura subsequente pode ser feita a partir de cache.  Se estiver a carregar dados apenas para os encenar antes de realizar mais transformações, carregar a tabela para a mesa de heap é muito mais rápido do que carregar os dados para uma tabela de colunas agrupada. Além disso, carregar dados para uma [mesa temporária](sql-data-warehouse-tables-temporary.md) carrega mais rapidamente do que carregar uma tabela para armazenamento permanente.  

Para pequenas mesas de lookup, menos de 60 milhões de filas, muitas vezes as mesas de amontoada fazem sentido.  As tabelas de colunas de cluster começam a obter uma compressão ideal uma vez que há mais de 60 milhões de linhas.

Para criar uma tabela de heap, basta especificar HEAP na cláusula COM:

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

Os índices agrupados podem superar as tabelas de lojas de colunas agrupadas quando uma única linha precisa de ser rapidamente recuperada. Para consultas em que uma única ou muito pouca sonoridade de linha é necessária para realizar com velocidade extrema, considere um índice de cluster ou um índice secundário não agrupado. A desvantagem para usar um índice agrupado é que apenas as consultas que beneficiam são as que usam um filtro altamente seletivo na coluna de índice agrupada. Para melhorar o filtro noutras colunas, um índice não agrupado pode ser adicionado a outras colunas. No entanto, cada índice que é adicionado a uma tabela adiciona espaço e tempo de processamento às cargas.

Para criar uma tabela de índices agrupadas, basta especificar o INDEXADO Clustered na cláusula COM:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice não agrupado sobre uma tabela, utilize a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Otimização dos índices de lojas de colunas agrupadas

As tabelas de lojas de colunas agrupadas são organizadas em dados em segmentos.  Ter alta qualidade de segmento é fundamental para alcançar um desempenho de consulta ideal numa tabela de colunas.  A qualidade do segmento pode ser medida pelo número de linhas num grupo de linhas comprimidos.  A qualidade do segmento é mais ideal onde existem pelo menos 100K linhas por grupo de linhas comprimidos e ganho de desempenho à medida que o número de linhas por grupo de linhas se aproxima de 1.048.576 linhas, que é a maior parte das linhas que um grupo de linhas pode conter.

A vista abaixo pode ser criada e usada no seu sistema para calcular as linhas médias por grupo de linhas e identificar quaisquer índices de colunas de cluster sub-ideais.  A última coluna desta vista gera uma declaração SQL que pode ser usada para reconstruir os seus índices.

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

Agora que criou a vista, faça esta consulta para identificar tabelas com grupos de remo com filas inferiores a 100K. Claro que pode querer aumentar o limiar de 100K se estiver à procura de uma qualidade de segmento mais ótima.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Uma vez executada a consulta, pode começar a olhar para os dados e analisar os seus resultados. Esta tabela explica o que procurar na sua análise de grupo de fileiras.

| Coluna | Como utilizar estes dados |
| --- | --- |
| [table_partition_count] |Se a mesa estiver dividida, então pode esperar ver as contagens de grupo open row mais altas. Cada partição na distribuição poderia, em teoria, ter um grupo de linhas abertas associado a ele. Considere isto na sua análise. Uma pequena tabela que foi dividida poderia ser otimizada removendo completamente a divisão, uma vez que isso melhoraria a compressão. |
| [row_count_total] |Contagem total de filas para a mesa. Por exemplo, pode utilizar este valor para calcular a percentagem de linhas no estado comprimido. |
| [row_count_per_distribution_MAX] |Se todas as linhas forem distribuídas uniformemente, este valor seria o número-alvo de linhas por distribuição. Compare este valor com o compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Número total de linhas em formato de loja de colunas para a tabela. |
| [COMPRESSED_rowgroup_rows_AVG] |Se o número médio de linhas for significativamente inferior ao #máximo de linhas para um grupo de linhas, então considere utilizar CTAS ou ALTERAR INDEX REBUILD para recomprimir os dados |
| [COMPRESSED_rowgroup_count] |Número de grupos de filas em formato de loja de colunas. Se este número for muito elevado em relação à tabela, é um indicador de que a densidade da loja de colunas é baixa. |
| [COMPRESSED_rowgroup_rows_DELETED] |As linhas são logicamente eliminadas no formato da columnstore. Se o número for elevado em relação ao tamanho da mesa, considere recriar a divisória ou reconstruir o índice, uma vez que este os remove fisicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Utilize isto em conjunto com as colunas AVG e MAX para compreender a gama de valores para os grupos de linhas na sua loja de colunas. Um número baixo sobre o limiar de carga (102.400 por distribuição alinhada por divisória) sugere que as otimizações estão disponíveis na carga de dados |
| [COMPRESSED_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_count] |Grupos de fila aberta são normais. Seria de esperar razoavelmente um grupo de linhas ABERTAs por distribuição de tabelas (60). Números excessivos sugerem o carregamento de dados através das divisórias. Verifique duas vezes a estratégia de partição para se certificar de que está sã |
| [OPEN_rowgroup_rows] |Cada grupo de filas pode ter 1.048.576 filas como máximo. Use este valor para ver como os grupos de linhas abertas estão cheios atualmente |
| [OPEN_rowgroup_rows_MIN] |Grupos abertos indicam que os dados estão a ser carregados na tabela ou que a carga anterior derramou sobre as linhas restantes neste grupo de linhas. Utilize as colunas MIN, MAX, AVG para ver quantos dados estão em grupos de linhas ABERTAs. Para pequenas tabelas pode ser 100% de todos os dados! Nesse caso, ALTER INDEX REBUILD para forçar os dados a armazenar colunas. |
| [OPEN_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_rows_AVG] |Como acima |
| [CLOSED_rowgroup_rows] |Olhe para as filas de grupo sanidade fechadas como uma verificação de sanidade. |
| [CLOSED_rowgroup_count] |O número de grupos de filas fechadas deve ser baixo se algum for visto. Os grupos de linhas fechadas podem ser convertidos em grupos de linhas comprimidos utilizando o ÍNDICE ALTER ... Reorganizar o comando. No entanto, isto não é normalmente necessário. Os grupos fechados são automaticamente convertidos em grupos de linhas de colunas pelo processo de fundo "tuple mover". |
| [CLOSED_rowgroup_rows_MIN] |Os grupos de fila fechada devem ter uma taxa de preenchimento muito elevada. Se a taxa de preenchimento de um grupo de linhas fechadas for baixa, então é necessária uma análise mais aprofundada da loja de colunas. |
| [CLOSED_rowgroup_rows_MAX] |Como acima |
| [CLOSED_rowgroup_rows_AVG] |Como acima |
| [Rebuild_Index_SQL] |SQL para reconstruir índice de loja de colunas para uma tabela |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de fraca qualidade do índice columnstore

Se identificou tabelas com má qualidade de segmento, pretende identificar a causa principal.  Abaixo estão algumas outras causas comuns de má qualidade do segmento:

1. Pressão de memória quando o índice foi construído
2. Grande volume de operações dML
3. Operações de carga pequenas ou gotas
4. Demasiadas divisórias

Estes fatores podem fazer com que um índice de colunas tenha significativamente menos do que o ideal de 1 milhão de linhas por grupo de linhas. Também podem fazer com que as filas vão para o grupo delta row em vez de um grupo de fileiras comprimidos.

### <a name="memory-pressure-when-index-was-built"></a>Pressão de memória quando o índice foi construído

O número de linhas por grupo de linhas comprimidos está diretamente relacionado com a largura da linha e com a quantidade de memória disponível para processar o grupo de linhas.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  Portanto, a melhor prática é dar a sessão que está a escrever para as tabelas de índices da sua loja de colunas acesso ao máximo de memória possível.  Uma vez que existe uma compensação entre a memória e a conmoeda, a orientação sobre a atribuição certa de memória depende dos dados de cada linha da sua tabela, das unidades de armazém de dados atribuídas ao seu sistema, e do número de ranhuras de condivisões que pode dar à sessão que está a escrever dados à sua tabela.

### <a name="high-volume-of-dml-operations"></a>Grande volume de operações dML

Um grande volume de operações de DML que atualizam e apagam linhas pode introduzir ineficiência na loja de colunas. Isto é especialmente verdade quando a maioria das linhas seguidas são modificadas.

- Apagar uma linha de um grupo de fileiras comprimidos apenas marca logicamente a linha como eliminada. A linha permanece no grupo de fileiras comprimido até que a divisória ou a mesa seja reconstruída.
- Inserir uma linha adiciona a linha a uma mesa interna de rowstore chamada grupo delta row. A linha inserida não é convertida em columnstore até que o grupo de linha delta esteja cheio e esteja marcado como fechado. Os grupos de filas são fechados assim que atingem a capacidade máxima de 1.048.576 linhas.
- Atualizar uma linha em formato de columnstore é processado como uma eliminação lógica e, em seguida, uma inserção. A linha inserida pode ser armazenada na loja delta.

As operações de atualização e inserção em lotação que excedam o limiar a granel de 102.400 linhas por distribuição alinhada com divisórias vão diretamente para o formato da columnstore. No entanto, assumindo uma distribuição uniforme, teria de modificar mais de 6,144 milhões de linhas numa única operação para que isso ocorresse. Se o número de linhas para uma determinada distribuição alinhada com divisórias for inferior a 102.400, as linhas vão para a loja delta e ficam lá até que tenham sido inseridas ou modificadas filas suficientes para fechar o grupo de linhas ou o índice tiver sido reconstruído.

### <a name="small-or-trickle-load-operations"></a>Operações de carga pequenas ou gotas

Pequenas cargas que fluem para a piscina Synapse SQL também são por vezes conhecidas como cargas de gotas. Normalmente representam um fluxo quase constante de dados que estão a ser ingeridos pelo sistema. No entanto, como este fluxo está próximo de continuar, o volume de linhas não é particularmente grande. Mais frequentemente do que não, os dados estão significativamente abaixo do limiar exigido para uma carga direta para o formato de colunastore.

Nestas situações, muitas vezes é melhor aterrar os dados primeiro no armazenamento de blob Azure e deixá-los acumular antes do carregamento. Esta técnica é frequentemente conhecida como *micro-lotação*.

### <a name="too-many-partitions"></a>Demasiadas divisórias

Outra coisa a ter em conta é o impacto da partilha nas tabelas de colunas agrupadas.  Antes de se partir, o pool SQL synapse já divide os seus dados em 60 bases de dados.  A partilha divide ainda mais os seus dados.  Se dividir os seus dados, considere que **cada** partição necessita de pelo menos 1 milhão de linhas para beneficiar de um índice de lojas de colunas agrupadas.  Se dividir a sua mesa em 100 divisórias, então a sua tabela precisa de pelo menos 6 mil milhões de linhas para beneficiar de um índice de lojas de colunas agrupadas (60 distribuições *100 divisórias* 1 milhão de linhas). Se a sua tabela de 100 divisórias não tiver 6 mil milhões de linhas, reduza o número de divisórias ou considere utilizar uma mesa de heap.

Uma vez que as suas tabelas tenham sido carregadas com alguns dados, siga os passos abaixo para identificar e reconstruir tabelas com índices de colunas agrupadas sub-ideais.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Índices de reconstrução para melhorar a qualidade do segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Passo 1: Identificar ou criar o utilizador que utiliza a classe de recursos certas

Uma forma rápida de melhorar imediatamente a qualidade do segmento é reconstruir o índice.  O SQL devolvido pela vista acima devolve uma declaração de RECONSTRUÇÃO DO ÍNDICE ALTER que pode ser usada para reconstruir os seus índices. Ao reconstruir os seus índices, certifique-se de que aloca memória suficiente para a sessão que reconstrói o seu índice.  Para tal, aumente a classe de recursos de um utilizador que tenha permissões para reconstruir o índice nesta tabela ao mínimo recomendado.

Abaixo está um exemplo de como alocar mais memória a um utilizador, aumentando a sua classe de recursos. Para trabalhar com aulas de recursos, consulte [aulas de recursos para gestão](resource-classes-for-workload-management.md)de carga de trabalho.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Passo 2: Reconstruir índices de colunas agrupadas com utilizadores de classe de recursos mais elevados

Inscreva-se como utilizador a partir do passo 1 (por exemplo, LoadUser), que agora utiliza uma classe de recursos mais elevada, e execute as declarações do ALTER INDEX. Certifique-se de que este utilizador tem permissão ALTER para as tabelas onde o índice está a ser reconstruído. Estes exemplos mostram como reconstruir todo o índice de colunas ou como reconstruir uma única divisória. Em mesas grandes, é mais prático reconstruir índices uma única partição de cada vez.

Em alternativa, em vez de reconstruir o índice, pode copiar a tabela para uma nova tabela [utilizando CTAS](sql-data-warehouse-develop-ctas.md). Qual é o melhor caminho? Para grandes volumes de dados, os CTAS são geralmente mais rápidos do que o [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Para volumes menores de dados, o ALTER INDEX é mais fácil de usar e não vai exigir que troque a tabela.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Reconstruir um índice na piscina SYnapse SQL é uma operação offline.  Para obter mais informações sobre os índices de reconstrução, consulte a secção DERECONSTRUÇÃO DO ÍNDICE ALTER em Índices de Colunas de [desfragmentação](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)e [ÍNDICE ALTER](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Passo 3: Verificar que a qualidade do segmento de colunas agrupadas melhorou

Reexecutar a consulta que identificou tabela com má qualidade de segmento e verificar a qualidade do segmento melhorou.  Se a qualidade do segmento não melhorou, pode ser que as linhas da sua mesa sejam extra largas.  Considere utilizar uma classe de recursos mais elevado ou DWU na reconstrução dos seus índices.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Índices de reconstrução com CTAS e comutação de divisórias

Este exemplo utiliza a declaração [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) e a troca de divisórias para reconstruir uma partilha de mesa.

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

Para mais detalhes sobre a recriação de divisórias utilizando CTAS, consulte [A utilização de divisórias na piscina SYnapse SQL](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento de tabelas, consulte [tabelas de desenvolvimento.](sql-data-warehouse-tables-overview.md)
