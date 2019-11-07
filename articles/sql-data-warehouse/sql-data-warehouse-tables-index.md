---
title: Indexando tabelas
description: Recomendações e exemplos para indexação de tabelas no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 079891824bf71caf1ebfa575833de650a55ed5be
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685445"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexando tabelas no SQL Data Warehouse

Recomendações e exemplos para indexação de tabelas no Azure SQL Data Warehouse.

## <a name="index-types"></a>Tipos de índice

O SQL Data Warehouse oferece várias opções de indexação, incluindo [índices columnstore clusterizados](/sql/relational-databases/indexes/columnstore-indexes-overview), [índices clusterizados e índices não clusterizados](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), além de uma opção que não é de índice, também conhecida como [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Para criar uma tabela com um índice, consulte a documentação [CREATE TABLE (Azure SQL data warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) .

## <a name="clustered-columnstore-indexes"></a>Índices columnstore clusterizados

Por padrão, SQL Data Warehouse cria um índice columnstore clusterizado quando nenhuma opção de índice é especificada em uma tabela. As tabelas columnstore clusterizadas oferecem o nível mais alto de compactação de dados, bem como o melhor desempenho geral de consulta.  As tabelas columnstore clusterizadas geralmente superarão o índice clusterizado ou as tabelas de heap e geralmente são a melhor opção para tabelas grandes.  Por esses motivos, o columnstore clusterizado é o melhor lugar para começar quando você não tiver certeza de como indexar sua tabela.  

Para criar uma tabela columnstore clusterizada, basta especificar o índice COLUMNSTORE CLUSTERIZAdo na cláusula WITH ou deixar a cláusula WITH desativada:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Há alguns cenários em que columnstore clusterizado pode não ser uma boa opção:

- As tabelas Columnstore não dão suporte a varchar (max), nvarchar (max) e varbinary (max). Considere o heap ou o índice clusterizado.
- As tabelas Columnstore podem ser menos eficientes para dados transitórios. Considere o heap e, talvez, até mesmo as tabelas temporárias.
- Tabelas pequenas com menos de 60 milhões linhas. Considere as tabelas de heap.

## <a name="heap-tables"></a>Tabelas de heap

Quando você estiver temporariamente destinando dados no SQL Data Warehouse, talvez você ache que usar uma tabela de heap torna o processo geral mais rápido. Isso ocorre porque as cargas em heaps são mais rápidas do que as tabelas de índice e, em alguns casos, a leitura subsequente pode ser feita do cache.  Se você estiver carregando dados somente para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap é muito mais rápido do que carregar os dados em uma tabela columnstore clusterizada. Além disso, o carregamento de dados em uma [tabela temporária](sql-data-warehouse-tables-temporary.md) é carregado mais rápido do que carregar uma tabela no armazenamento permanente.  

Para tabelas de pesquisa pequenas, menos de 60 milhões linhas, muitas vezes as tabelas de heap fazem sentido.  As tabelas columnstore do cluster começam a alcançar uma compactação ideal quando há mais de 60 milhões linhas.

Para criar uma tabela de heap, basta especificar HEAP na cláusula WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices clusterizados e não clusterizados

Os índices clusterizados podem superar as tabelas columnstore clusterizadas quando uma única linha precisa ser recuperada rapidamente. Para consultas em que uma pesquisa de linha única ou muito poucas é necessária para o desempenho com velocidade extrema, considere um índice de cluster ou um índice secundário não clusterizado. A desvantagem de usar um índice clusterizado é que apenas as consultas que se beneficiam são aquelas que usam um filtro altamente seletivo na coluna de índice clusterizado. Para melhorar o filtro em outras colunas, um índice não clusterizado pode ser adicionado a outras colunas. No entanto, cada índice que é adicionado a uma tabela adiciona espaço e tempo de processamento para cargas.

Para criar uma tabela de índice clusterizado, basta especificar o índice CLUSTERIZAdo na cláusula WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice não clusterizado em uma tabela, use a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Otimizando índices columnstore clusterizados

As tabelas columnstore clusterizadas são organizadas em dados em segmentos.  Ter alta qualidade de segmento é essencial para alcançar o desempenho de consulta ideal em uma tabela columnstore.  A qualidade do segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  A qualidade do segmento é ideal quando há pelo menos 100.000 linhas por grupo de linhas compactado e obter desempenho como o número de linhas por abordagem de grupo de linha 1.048.576 linhas, que é a maior quantidade de linhas que um grupo de linhas pode conter.

O modo de exibição abaixo pode ser criado e usado no seu sistema para computar as linhas médias por grupo de linhas e identificar os índices columnstore de cluster abaixo do ideal.  A última coluna nessa exibição gera uma instrução SQL que pode ser usada para recriar os índices.

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

Agora que você criou o modo de exibição, execute esta consulta para identificar tabelas com grupos de linhas com menos de 100.000 linhas. É claro que você pode desejar aumentar o limite de 100 mil se estiver procurando uma qualidade de segmento ideal.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Depois de executar a consulta, você pode começar a examinar os dados e analisar os resultados. Esta tabela explica o que procurar em sua análise de grupo de linhas.

| Column | Como usar esses dados |
| --- | --- |
| [table_partition_count] |Se a tabela for particionada, talvez você espere ver as contagens de grupos de linhas abertas mais altas. Cada partição na distribuição poderia, teoricamente, ter um grupo de linhas aberto associado a ela. Considerar isso em sua análise. Uma pequena tabela que foi particionada poderia ser otimizada removendo o particionamento completamente, pois isso melhoraria a compactação. |
| [row_count_total] |Contagem total de linhas da tabela. Por exemplo, você pode usar esse valor para calcular a porcentagem de linhas no estado compactado. |
| [row_count_per_distribution_MAX] |Se todas as linhas forem distribuídas uniformemente, esse valor será o número de destino de linhas por distribuição. Compare esse valor com o compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Número total de linhas no formato columnstore para a tabela. |
| [COMPRESSED_rowgroup_rows_AVG] |Se o número médio de linhas for significativamente menor que o n º máximo de linhas para um grupo de linhas, considere usar CTAS ou ALTER INDEX REBUILD para recompactar os dados |
| [COMPRESSED_rowgroup_count] |Número de grupos de linhas no formato columnstore. Se esse número for muito alto em relação à tabela, será um indicador de que a densidade columnstore é baixa. |
| [COMPRESSED_rowgroup_rows_DELETED] |As linhas são logicamente excluídas no formato columnstore. Se o número for alto em relação ao tamanho da tabela, considere recriar a partição ou recriar o índice, pois isso os remove fisicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Use-o em conjunto com as colunas AVG e MAX para entender o intervalo de valores para os grupos de linhas em seu columnstore. Um número baixo acima do limite de carga (102.400 por distribuição alinhada por partição) sugere que as otimizações estão disponíveis no carregamento de dados |
| [COMPRESSED_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_count] |Os grupos de linhas abertos são normais. Um seria razoavelmente esperado um grupo de linhas aberto por distribuição de tabela (60). Números excessivos sugerem carregamento de dados entre partições. Verifique novamente a estratégia de particionamento para verificar se ela é sólida |
| [OPEN_rowgroup_rows] |Cada grupo de linhas pode ter 1.048.576 linhas nele como um máximo. Use este valor para ver o quão completo os grupos de linhas abertos estão atualmente |
| [OPEN_rowgroup_rows_MIN] |Os grupos abertos indicam que os dados estão sendo trickle carregados na tabela ou que a carga anterior foi despejada sobre as linhas restantes nesse grupo de linhas. Use as colunas MIN, MAX e AVG para ver a quantidade de dados em grupos de linhas abertos. Para tabelas pequenas, poderia ser 100% de todos os dados! Nesse caso, ALTER INDEX REBUILD para forçar os dados para o columnstore. |
| [OPEN_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_rows_AVG] |Como acima |
| [CLOSED_rowgroup_rows] |Examine as linhas do grupo de linhas fechadas como uma verificação de sanidade. |
| [CLOSED_rowgroup_count] |O número de grupos de linhas fechados deve ser baixo se algum for visto. Os grupos de linhas fechados podem ser convertidos em grupos de linhas compactados usando a instrução ALTER INDEX... Comando reorganizar. No entanto, isso normalmente não é necessário. Os grupos fechados são convertidos automaticamente em grupos de linhas columnstore pelo processo de "movimentação de tupla" em segundo plano. |
| [CLOSED_rowgroup_rows_MIN] |Os grupos de linhas fechados devem ter uma taxa de preenchimento muito alta. Se a taxa de preenchimento de um grupo de linhas fechado for baixa, a análise adicional do columnstore será necessária. |
| [CLOSED_rowgroup_rows_MAX] |Como acima |
| [CLOSED_rowgroup_rows_AVG] |Como acima |
| [Rebuild_Index_SQL] |SQL para recriar o índice columnstore para uma tabela |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas da qualidade de índice columnstore ruim

Se você identificou tabelas com qualidade de segmento ruim, você deseja identificar a causa raiz.  Abaixo estão algumas causas comuns de má qualidade de segmento:

1. Pressão de memória quando o índice foi criado
2. Alto volume de operações DML
3. Operações de carregamento pequenas ou trickles
4. Muitas partições

Esses fatores podem fazer com que um índice columnstore tenha significativamente menos do que o ideal de 1 milhão linhas por grupo de linhas. Eles também podem fazer com que as linhas passem para o grupo de linhas Delta em vez de um grupo de linhas compactado.

### <a name="memory-pressure-when-index-was-built"></a>Pressão de memória quando o índice foi criado

O número de linhas por grupo de linhas compactado está diretamente relacionado à largura da linha e à quantidade de memória disponível para processar o grupo de linhas.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  Portanto, a prática recomendada é fornecer à sessão que está gravando o acesso de tabelas de índice columnstore ao máximo de memória possível.  Como há uma compensação entre a memória e a simultaneidade, a orientação sobre a alocação de memória correta depende dos dados em cada linha da tabela, das unidades de data warehouse alocadas para o seu sistema e do número de Slots de simultaneidade que você pode dar à sessão que está gravando dados em sua tabela.

### <a name="high-volume-of-dml-operations"></a>Alto volume de operações DML

Um alto volume de operações DML que atualizam e excluem linhas pode introduzir a ineficiência no columnstore. Isso é especialmente verdadeiro quando a maioria das linhas em um grupo de linhas é modificada.

- A exclusão de uma linha de um grupo de linhas compactada apenas marca logicamente a linha como excluída. A linha permanece no grupo de linhas compactada até que a partição ou tabela seja recriada.
- A inserção de uma linha adiciona a linha a uma tabela interna do rowgroup chamada um grupo de linhas Delta. A linha inserida não é convertida em columnstore até que o grupo de linhas Delta esteja cheio e seja marcado como fechado. Os grupos de linhas são fechados quando atingem a capacidade máxima de 1.048.576 linhas.
- A atualização de uma linha no formato columnstore é processada como uma exclusão lógica e, em seguida, uma inserção. A linha inserida pode ser armazenada no armazenamento Delta.

As operações de atualização e inserção em lote que excedem o limite em massa de 102.400 linhas por distribuição alinhada por partição vão diretamente para o formato columnstore. No entanto, supondo uma distribuição uniforme, você precisaria estar modificando mais de 6.144.000 linhas em uma única operação para que isso ocorra. Se o número de linhas de uma determinada distribuição alinhada por partição for menor que 102.400, as linhas vão para o repositório Delta e permanecem lá até que as linhas suficientes tenham sido inseridas ou modificadas para fechar o grupo de linhas ou o índice tenha sido recriado.

### <a name="small-or-trickle-load-operations"></a>Operações de carregamento pequenas ou trickles

Pequenas cargas que fluem em SQL Data Warehouse às vezes também são conhecidas como cargas de Trickle. Normalmente, eles representam um fluxo quase constante de dados que estão sendo ingeridos pelo sistema. No entanto, como esse fluxo é quase contínuo, o volume de linhas não é particularmente grande. Com mais frequência do que não os dados estão significativamente sob o limite necessário para uma carga direta para o formato columnstore.

Nessas situações, geralmente é melhor colocar os dados primeiro no armazenamento de BLOBs do Azure e deixá-los acumular antes do carregamento. Essa técnica geralmente é conhecida como *micro Batching*.

### <a name="too-many-partitions"></a>Muitas partições

Outra coisa a considerar é o impacto do particionamento em suas tabelas columnstore clusterizadas.  Antes de particionar, SQL Data Warehouse já divide seus dados em bancos de dado 60.  O particionamento divide ainda mais seus dados.  Se você particionar seus dados, considere que **cada** partição precisa de pelo menos 1 milhão linhas para se beneficiar de um índice columnstore clusterizado.  Se você particionar sua tabela em 100 partições, sua tabela precisará de pelo menos 6.000.000.000 linhas para se beneficiar de um índice columnstore clusterizado (60 distribuições *100 partições* 1 milhão linhas). Se a tabela 100-Partition não tiver 6.000.000.000 linhas, reduza o número de partições ou considere usar uma tabela de heap.

Depois que as tabelas tiverem sido carregadas com alguns dados, siga as etapas abaixo para identificar e recriar tabelas com índices columnstore clusterizados de nível inferior.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Recriando índices para melhorar a qualidade do segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Etapa 1: identificar ou criar um usuário que usa a classe de recurso correta

Uma maneira rápida de melhorar imediatamente a qualidade do segmento é recompilar o índice.  O SQL retornado pela exibição acima retorna uma instrução ALTER INDEX REBUILD, que pode ser usada para recriar os índices. Ao recriar os índices, certifique-se de alocar memória suficiente para a sessão que recria o índice.  Para fazer isso, aumente a classe de recurso de um usuário que tem permissões para recriar o índice nessa tabela para o mínimo recomendado.

Abaixo está um exemplo de como alocar mais memória para um usuário, aumentando sua classe de recurso. Para trabalhar com classes de recursos, consulte [classes de recursos para gerenciamento de carga de](resource-classes-for-workload-management.md)trabalho.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Etapa 2: recompilar índices columnstore clusterizados com maior usuário da classe de recursos

Entre como o usuário da etapa 1 (por exemplo, loaduser), que agora está usando uma classe de recurso mais alta e execute as instruções ALTER INDEX. Certifique-se de que esse usuário tenha a permissão ALTER para as tabelas em que o índice está sendo recriado. Esses exemplos mostram como recriar o índice columnstore inteiro ou como recriar uma única partição. Em tabelas grandes, é mais prático recriar índices uma única partição por vez.

Como alternativa, em vez de recompilar o índice, você pode copiar a tabela para uma nova tabela [usando CTAS](sql-data-warehouse-develop-ctas.md). Qual é a melhor maneira? Para grandes volumes de dados, o CTAS geralmente é mais rápido do que [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Para volumes menores de dados, ALTER INDEX é mais fácil de usar e não exigirá que você troque a tabela.

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

A recriação de um índice no SQL Data Warehouse é uma operação offline.  Para obter mais informações sobre a recompilação de índices, consulte a seção ALTER INDEX REBUILD em [desfragmentação de índices Columnstore](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)e [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Etapa 3: verificar se a qualidade do segmento columnstore clusterizado foi aprimorada

Execute novamente a consulta que identificou a tabela com qualidade de segmento ruim e verifique se a qualidade do segmento foi aprimorada.  Se a qualidade do segmento não melhorou, pode ser que as linhas em sua tabela tenham uma largura extra.  Considere usar uma classe de recurso mais alta ou DWU ao recriar os índices.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Recriando índices com CTAS e troca de partição

Este exemplo usa a instrução [CREATE TABLE como SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) e a alternância de partição para recriar uma partição de tabela.

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

Para obter mais detalhes sobre como recriar partições usando o CTAS, consulte [usando partições no SQL data warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como desenvolver tabelas, consulte [desenvolvendo tabelas](sql-data-warehouse-tables-overview.md).
