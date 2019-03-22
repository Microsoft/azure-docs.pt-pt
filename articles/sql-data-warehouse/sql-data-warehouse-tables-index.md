---
title: Indexação de tabelas no armazém de dados SQL do Azure | Microsoft Azure
description: Recomendações e exemplos para indexação tabelas no armazém de dados SQL do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: fe19510d9b4c6311923b4b2ea15f133249e6cbd5
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190044"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexação de tabelas no armazém de dados SQL
Recomendações e exemplos para indexação tabelas no armazém de dados SQL do Azure.

## <a name="what-are-index-choices"></a>Quais são as opções de índice?

SQL Data Warehouse oferece várias opções de indexação, incluindo [em cluster os índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview), [colocar em cluster índices e índices não agrupados](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), e um índice não-opção, também conhecido como [heap ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Para criar uma tabela com um índice, consulte a [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) documentação.

## <a name="clustered-columnstore-indexes"></a>Índices columnstore em cluster
Por predefinição, o SQL Data Warehouse cria um índice columnstore em cluster quando não existem opções de índice são especificadas numa tabela. Tabelas columnstore em cluster oferecem, tanto o maior nível de compressão de dados, bem como o melhor desempenho de consulta global.  Tabelas columnstore em cluster em geral serão superar o desempenho de tabelas de índice ou a área dinâmica para dados em cluster e costumam ser a melhor opção para tabelas grandes.  Por esses motivos, o columnstore em cluster é o melhor local para iniciar quando tiver a certeza de como sua tabela de índice.  

Para criar uma tabela columnstore em cluster, basta especificar o índice COLUMNSTORE em cluster na cláusula WITH ou deixe a cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existem alguns cenários em que o columnstore em cluster pode não ser uma boa opção:

- Tabelas Columnstore não suportam varchar (Max), nvarchar (Max) e varbinary (Max). Considere o heap ou o índice em cluster em vez disso.
- Tabelas Columnstore poderão ser menos eficientes para dados transitórios. Considere a área dinâmica para dados e tabelas temporárias talvez até mesmo.
- Obter tabelas pequenas com menos de 60 milhões de linhas. Considere as tabelas de heap.

## <a name="heap-tables"></a>Tabelas de Heap
Quando está temporariamente a colocar dados no SQL Data Warehouse, talvez ache que usar uma tabela de área dinâmica para dados torna o processo geral mais rápido. Isso é porque carregamentos para pilhas são mais rápidos do que para tabelas de índice e, em alguns casos que a leitura subsequente pode ser feita a partir da cache.  Se estiver a carregar dados apenas para testá-lo antes de executar mais transformações, carregar a tabela à tabela de área dinâmica para dados é muito mais rápido do que carregar os dados a uma tabela columnstore em cluster. Além disso, carregar dados para um [tabela temporária](sql-data-warehouse-tables-temporary.md) carrega mais rapidamente do que carregar uma tabela para armazenamento permanente.  

Para tabelas de pesquisa pequeno, menos de 60 milhões de linhas, muitas vezes, as tabelas de heap fazem sentido.  Tabelas de columnstore cluster começam a alcançar a compressão ideal quando houver mais de 60 milhões de linhas.

Para criar uma tabela de área dinâmica para dados, basta Especifica HEAP na cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices em cluster e não em cluster
Índices em cluster tabelas columnstore em cluster podem superar o desempenho quando precisa de uma única linha a obter rapidamente. Para consultas em que um único ou de pesquisa de linha muito poucos é necessária para o desempenho com velocidade de extrema, considere um índice cluster ou um índice secundário não em cluster. A desvantagem usando um índice clusterizado é que apenas as consultas que tiram partido são aqueles que utilizam um filtro altamente seletivo na coluna de índice agrupado. Para melhorar o filtro nas outras colunas, um índice não em cluster pode ser adicionado às outras colunas. No entanto, cada índice que é adicionado a uma tabela adiciona espaço e tempo de processamento de cargas.

Para criar uma tabela de índice agrupado, basta Especifica índice AGRUPADO na cláusula WITH:

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

## <a name="optimizing-clustered-columnstore-indexes"></a>Otimização de índices columnstore em cluster
Tabelas columnstore em cluster são organizadas em dados em segmentos.  É fundamental para alcançar o desempenho ideal de consultas em tabelas columnstore ter qualidade de segmento de alta.  Qualidade de segmento pode ser medida pelo número de linhas num grupo de linhas comprimido.  Qualidade de segmento é ideal em que existem, pelo menos, 100 mil linhas por linhas comprimidas de grupo e obter no desempenho, como o número de linhas por uma abordagem de grupo da linha 1 048 576 linhas, que é que a maioria das linhas que pode conter um grupo de linhas.

O abaixo vista pode ser criada e utilizada no seu sistema para calcular as média linhas por linha de grupo e identificar qualquer índices columnstore em cluster abaixo do ideal.  A última coluna sobre esta vista gera uma instrução SQL que pode ser utilizada para reconstruir os índices.

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

Agora que criou a exibição, executar esta consulta para identificar as tabelas com grupos de linhas com menos do que 100 mil linhas. É claro, pode querer aumentar o limite de 100K, se estiver à procura de mais qualidade de segmento ideal. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Depois de executar a consulta que pode começar a examinar os dados e analisar os resultados. Esta tabela explica o que procurar em sua análise do grupo de linha.

| Coluna | Como utilizar estes dados |
| --- | --- |
| [table_partition_count] |Se a tabela está particionada, em seguida, pode esperar ver a conta de grupo de linhas abertos superior. Em teoria, cada partição na distribuição poderia ter um grupo de linhas abertos associado a ele. Incluir isso no sua análise. Uma pequena tabela que tem sido particionada poderia ser otimizada ao remover completamente o particionamento como isso aumentaria a compressão. |
| [row_count_total] |Contagem total de linhas da tabela. Por exemplo, pode utilizar este valor para calcular a porcentagem das linhas no estado comprimido. |
| [row_count_per_distribution_MAX] |Se todas as linhas são distribuídas uniformemente esse valor poderia ser o número de linhas por distribuição de destino. Compare este valor com o compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Número total de linhas no formato columnstore para a tabela. |
| [COMPRESSED_rowgroup_rows_AVG] |Se o número médio de linhas é significativamente menor do que o n. º máximo de linhas para um grupo de linhas, em seguida, considere utilizar CTAS ou ALTER INDEX REBUILD para recompress os dados |
| [COMPRESSED_rowgroup_count] |Número de grupos de linhas no formato columnstore. Se este número é muito alto em relação a tabela é um indicador de que a densidade de columnstore é baixa. |
| [COMPRESSED_rowgroup_rows_DELETED] |Linhas logicamente são eliminadas no formato columnstore. Se o número for elevado em relação ao tamanho da tabela, considere recriar a partição ou reconstrua o índice como esta ação remove-los fisicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Usado em conjunto com as colunas de média e máxima para compreender o intervalo de valores para os grupos de linhas na sua columnstore. Um número baixo superior ao limiar de carga (102,400 por partição alinhada distribuição) sugere que as otimizações estão disponíveis no carregamento de dados |
| [COMPRESSED_rowgroup_rows_MAX] |Assim como acima |
| [OPEN_rowgroup_count] |Grupos de linhas abertos são normais. Razoavelmente era de se esperar um grupo de linhas abertos por distribuição da tabela (60). Números excessivos sugerem o carregamento em partições de dados. Verifique novamente a estratégia de particionamento para garantir que ela é som |
| [OPEN_rowgroup_rows] |Cada grupo de linhas pode ter 1 048 576 linhas no mesmo como um máximo. Utilize este valor para ver como completa os grupos de linhas abertos são atualmente |
| [OPEN_rowgroup_rows_MIN] |Abrir grupos indicam que os dados estão a ser trickle carregado para a tabela ou que a carga anterior derramou restantes linhas neste grupo de linhas. Utilizar o mín, Máx, grupos de linha de colunas de média para ver a quantidade de dados é Sentei no licenciamento OPEN. Para tabelas pequenas pode ser 100% de todos os dados! Nesse caso ALTER INDEX REBUILD para exigir que os dados para o columnstore. |
| [OPEN_rowgroup_rows_MAX] |Assim como acima |
| [OPEN_rowgroup_rows_AVG] |Assim como acima |
| [CLOSED_rowgroup_rows] |Ver as linhas de grupo de linha fechados como uma verificação da funcionalidade. |
| [CLOSED_rowgroup_count] |O número de grupos de linhas fechados deve ser baixo se qualquer são vistos de todo. Grupos de linhas fechado podem ser convertidos em grupos de linhas comprimido utilizando ALTER INDEX... REORGANIZE o comando. No entanto, isto não é normalmente necessário. Grupos fechados automaticamente são convertidos em grupos de linhas de columnstore pelo processo de "Movimentador de cadeia de identificação" em segundo plano. |
| [CLOSED_rowgroup_rows_MIN] |Grupos de linhas fechados devem ter uma taxa muito elevada de preenchimento. Se a taxa de preenchimento para um grupo de linhas fechado é baixa, são necessárias mais análises do columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Assim como acima |
| [CLOSED_rowgroup_rows_AVG] |Assim como acima |
| [Rebuild_Index_SQL] |SQL para reconstruir o índice columnstore para uma tabela |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de qualidade do índice columnstore fraca
Se identificou a tabelas com qualidade de segmento fraco, pretende identificar a causa de raiz.  Seguem-se algumas outras causas comuns de qualidade de segmento fraco:

1. Pressão de memória quando o índice foi criado
2. Alto volume de operações DML
3. Pequenas ou trickle operações de carga
4. Demasiadas partições

Esses fatores podem fazer com que um índice columnstore, ter significativamente menor do que o ideal 1 milhão de linhas por grupo de linhas. Eles também podem causar linhas Ir para o grupo de linhas de delta em vez de um grupo de linhas comprimido. 

### <a name="memory-pressure-when-index-was-built"></a>Pressão de memória quando o índice foi criado
O número de linhas por grupo de linhas comprimido está diretamente relacionados com a largura da linha e a quantidade de memória disponível para processar o grupo de linhas.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  Portanto, a prática recomendada é dar a sessão que está a escrever para o acesso de tabelas de índice columnstore para o máximo possível de memória.  Como há uma compensação entre a memória e simultaneidade, a documentação de orientação sobre a alocação de memória certo depende dos dados em cada linha da sua tabela, as unidades de armazém de dados alocadas para seu sistema e o número de ranhuras de simultaneidade pode dar à sessão que está a escrever dados à sua tabela.

### <a name="high-volume-of-dml-operations"></a>Alto volume de operações DML
Um grande volume de operações DML que atualizar e eliminar linhas pode introduzir ineficiência para o columnstore. Isso é especialmente verdadeiro quando a maioria das linhas num grupo de linhas são modificadas.

- A eliminar uma linha de um grupo de linhas comprimido apenas logicamente marca a linha como excluído. A linha permanece no grupo de linhas comprimido até que a partição ou tabela for reconstruída.
- Inserir uma linha adiciona a linha a uma tabela de rowstore interno chamada de um grupo de linhas de delta. A linha inserida não é convertida para o columnstore até que o grupo de linhas de delta está cheio e é marcado como fechada. Grupos de linhas são fechados assim que a capacidade máxima de 1 048 576 linhas. 
- A atualizar uma linha no formato columnstore é processada como uma exclusão de lógica e, em seguida, uma inserção. A linha inserida pode ser armazenada no arquivo de delta.

Atualização em lote e operações de inserção que excedem o limiar em massa de 102,400 linhas por partição alinhada distribuição vão diretamente para o formato columnstore. No entanto, supondo que uma distribuição uniforme, seria necessário modificar mais de 6.144 milhões de linhas numa única operação para que isso ocorra. Se o número de linhas para uma determinado distribuição alinhadas com a partição é menos de 102,400 linhas vão para o andstay da loja de delta até suficiente foram inseridas ou modificadas para fechar o grupo de linhas ou o índice foi reconstruído.

### <a name="small-or-trickle-load-operations"></a>Pequenas ou trickle operações de carga
Pequenas cargas que o fluxo para o SQL Data Warehouse também, por vezes, são conhecidas como trickle cargas. Normalmente, eles representam um quase transmissão em fluxo constante de dados a ser ingeridos pelo sistema. No entanto, como esse fluxo é quase contínua o volume de linhas não é particularmente grande. Mais freqüência, os dados são significativamente inferior ao limiar necessário para um carregamento direto para o formato columnstore.

Nestas situações, muitas vezes é melhor apresentar o primeiro os dados de armazenamento de Blobs do Azure e deixá-lo a se acumular antes de carregar. Essa técnica, muitas vezes, é conhecida como *criação de batches de micro*.

### <a name="too-many-partitions"></a>Demasiadas partições
Outro ponto a considerar é o impacto da criação de partições em tabelas columnstore em cluster.  Antes de criação de partições, o SQL Data Warehouse divide já seus dados em 60 bases de dados.  Ainda mais a criação de partições divide os seus dados.  Se dividir os dados, em seguida, considere que **cada** partição tem de, pelo menos, 1 milhão de linhas para beneficiar de um índice columnstore em cluster.  Se criar partições da tabela em 100 partições, então, sua tabela tem de, pelo menos, 6 milhões de linhas para beneficiar de um índice columnstore em cluster (60 distribuições * 100 partições * 1 milhão de linhas). Se a sua tabela de partições de 100 não tiver 6 milhões de linhas, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados em vez disso.

Depois das suas tabelas tenham sido carregadas com alguns dados, siga os passos abaixo para identificar e reconstruir as tabelas com abaixo do ideal em cluster os índices columnstore.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Reconstruir índices para melhorar a qualidade de segmento
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Passo 1: Identificar ou criar o utilizador que usa a classe de recurso correta
É uma forma rápida para melhorar a qualidade do segmento imediatamente reconstruir o índice.  O SQL devolvido pela exibição acima devolve uma instrução ALTER INDEX REBUILD, que pode ser utilizada para reconstruir os índices. Ao reconstruir os índices, certifique-se de que aloca memória suficiente para a sessão que recria o seu índice.  Para tal, aumente a classe de recursos de um utilizador que tem permissões para criar o índice nesta tabela para o mínimo recomendado. 

Segue-se um exemplo de como alocar mais memória a um utilizador ao aumentar a sua classe de recursos. Para trabalhar com classes de recursos, consulte [classes de recursos para a gestão da carga de trabalho](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Passo 2: Reconstruir índices columnstore em cluster com utilizadores de classe de recursos superior
Inicie sessão como utilizador do passo 1 (por exemplo, LoadUser), que é agora, usando uma classe de recursos maior, e executar as instruções ALTER INDEX. Certifique-se de que este utilizador tem a permissão ALTER para as tabelas onde o índice está sendo reconstruído. Estes exemplos mostram como reconstruir o índice columnstore todo ou como reconstruir uma única partição. Em tabelas grandes, é mais prático para reconstruir índices uma única partição, ao mesmo tempo.

Em alternativa, em vez de reconstrua o índice, copiar a tabela para uma nova tabela [utilizar CTAS](sql-data-warehouse-develop-ctas.md). Qual o melhor caminho é melhor? Para grandes volumes de dados, é geralmente mais rápido do que CTAS [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Para mais pequenos volumes de dados, ALTER INDEX é mais fácil de usar e não requer trocar a tabela. 

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

Reconstruir um índice no SQL Data Warehouse é uma operação offline.  Para obter mais informações sobre como reconstruir índices, consulte a secção ALTER INDEX REBUILD [desfragmentação de índices Columnstore](/sql/relational-databases/indexes/columnstore-indexes-defragmentation), e [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Passo 3: Certifique-se de que melhorou a qualidade de segmento de columnstore em cluster
Volte a executar a consulta a tabela identificada com fraca qualidade do segmento e certifique-se de qualidade de segmento melhorou.  Se não a melhorar a qualidade do segmento, é possível que as linhas na tabela são extra grande.  Considere a utilização de uma classe de recursos superior ou DWU ao reconstruir os índices.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Reconstruir índices com CTAS e a alternância de partição
Este exemplo utiliza a [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrução e a mudança para recriar uma partição de tabela de partições. 

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

Para obter mais detalhes sobre as partições com CTAS voltar a criar, ver [usando partições no SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o desenvolvimento de tabelas, veja [desenvolver tabelas](sql-data-warehouse-tables-overview.md).

