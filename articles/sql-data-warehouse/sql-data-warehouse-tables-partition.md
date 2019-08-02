---
title: Particionando tabelas no Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações e exemplos para usar partições de tabela no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6791ff2f2a9719a19d2c9abc4ff480435de7bb00
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477092"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Particionando tabelas no SQL Data Warehouse
Recomendações e exemplos para usar partições de tabela no SQL Data Warehouse do Azure.

## <a name="what-are-table-partitions"></a>O que são partições de tabela?
As partições de tabela permitem dividir os dados em grupos menores de dados. Na maioria dos casos, as partições de tabela são criadas em uma coluna de data. O particionamento tem suporte em todos os tipos de tabela de SQL Data Warehouse; incluindo columnstore clusterizado, índice clusterizado e heap. O particionamento também tem suporte em todos os tipos de distribuição, incluindo hash ou round robin distribuídos.  

O particionamento pode beneficiar a manutenção de dados e o desempenho da consulta. Se ele beneficia ambos ou apenas um depende de como os dados são carregados e se a mesma coluna pode ser usada para ambas as finalidades, já que o particionamento só pode ser feito em uma coluna.

### <a name="benefits-to-loads"></a>Benefícios para cargas
O principal benefício do particionamento no SQL Data Warehouse é melhorar a eficiência e o desempenho do carregamento de dados por meio do uso de exclusão de partição, troca e mesclagem. Na maioria dos casos, os dados são particionados em uma coluna de data que está fortemente ligada à ordem na qual os dados são carregados no banco de dado. Um dos maiores benefícios do uso de partições para manter os dados a eliminação do log de transações. Embora simplesmente inserir, atualizar ou excluir dados possa ser a abordagem mais direta, com um pouco de pensamento e esforço, o uso do particionamento durante o processo de carregamento pode melhorar substancialmente o desempenho.

A alternância de partição pode ser usada para remover ou substituir rapidamente uma seção de uma tabela.  Por exemplo, uma tabela de fatos de vendas pode conter apenas dados para os últimos 36 meses. No final de cada mês, o mês mais antigo de dados de vendas é excluído da tabela.  Esses dados podem ser excluídos usando uma instrução DELETE para excluir os dados do mês mais antigo. No entanto, a exclusão de uma grande quantidade de dados linha por linha com uma instrução DELETE pode levar muito tempo, bem como criar o risco de transações grandes que levam muito tempo para reverter se algo der errado. Uma abordagem mais ideal é descartar a partição de dados mais antiga. Quando a exclusão de linhas individuais pode levar horas, a exclusão de uma partição inteira pode levar segundos.

### <a name="benefits-to-queries"></a>Benefícios para consultas
O particionamento também pode ser usado para melhorar o desempenho da consulta. Uma consulta que aplica um filtro a dados particionados pode limitar a verificação somente a partições de qualificação. Esse método de filtragem pode evitar uma verificação de tabela completa e verificar apenas um subconjunto de dados menor. Com a introdução de índices columnstore clusterizados, os benefícios de desempenho de eliminação de predicado são menos benéficos, mas, em alguns casos, pode haver um benefício para as consultas. Por exemplo, se a tabela de fatos de vendas é particionada em 36 meses usando o campo data de vendas, as consultas que filtram a data de venda podem ignorar a pesquisa em partições que não correspondem ao filtro.

## <a name="sizing-partitions"></a>Dimensionando partições
Embora o particionamento possa ser usado para melhorar o desempenho de alguns cenários, criar uma tabela com **muitas** partições pode prejudicar o desempenho em algumas circunstâncias.  Essas preocupações são especialmente verdadeiras para tabelas columnstore clusterizadas. Para que o particionamento seja útil, é importante entender quando usar o particionamento e o número de partições a serem criadas. Não há nenhuma regra difícil quanto a quantas partições são muitas, depende de seus dados e de quantas partições você carrega simultaneamente. Um esquema de particionamento bem-sucedido geralmente tem dezenas a centenas de partições, e não milhares.

Ao criar partições em tabelas **columnstore clusterizadas** , é importante considerar quantas linhas pertencem a cada partição. Para obter a compactação e o desempenho ideais das tabelas columnstore clusterizadas, é necessário um mínimo de 1 milhão linhas por distribuição e partição. Antes que as partições sejam criadas, SQL Data Warehouse já divide cada tabela em bancos de dados distribuídos 60. Qualquer particionamento adicionado a uma tabela é além das distribuições criadas nos bastidores. Usando este exemplo, se a tabela de fatos de vendas contiver 36 partições mensais e, Considerando que SQL Data Warehouse tem 60 distribuições, a tabela de fatos de vendas deverá conter 60 milhões linhas por mês ou 2.100.000.000 linhas quando todos os meses forem preenchidos. Se uma tabela contiver menos que o número mínimo recomendado de linhas por partição, considere usar menos partições para aumentar o número de linhas por partição. Para obter mais informações, consulte [](sql-data-warehouse-tables-index.md) o artigo indexação, que inclui consultas que podem avaliar a qualidade dos índices columnstore do cluster.

## <a name="syntax-differences-from-sql-server"></a>Diferenças de sintaxe de SQL Server
SQL Data Warehouse introduz uma maneira de definir partições mais simples do que SQL Server. As funções e os esquemas de particionamento não são usados no SQL Data Warehouse como estão em SQL Server. Em vez disso, tudo o que você precisa fazer é identificar a coluna particionada e os pontos de limite. Embora a sintaxe do particionamento possa ser um pouco diferente da SQL Server, os conceitos básicos são os mesmos. SQL Server e SQL Data Warehouse dão suporte a uma coluna de partição por tabela, que pode ser uma partição de intervalo. Para saber mais sobre particionamento, consulte [tabelas e índices particionados](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

O exemplo a seguir usa a instrução [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) para particionar a tabela FactInternetSales na coluna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrando o particionamento de SQL Server
Para migrar SQL Server definições de partição para SQL Data Warehouse simplesmente:

- Elimine o [esquema de partição](/sql/t-sql/statements/create-partition-scheme-transact-sql)SQL Server.
- Adicione a definição de [função de partição](/sql/t-sql/statements/create-partition-function-transact-sql) à sua CREATE TABLE.

Se você estiver migrando uma tabela particionada de uma instância SQL Server, o SQL a seguir poderá ajudá-lo a descobrir o número de linhas em cada partição. Tenha em mente que, se a mesma granularidade de particionamento for usada na SQL Data Warehouse, o número de linhas por partição diminuirá em um fator de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Alternância de partição
O SQL Data Warehouse dá suporte à divisão, mesclagem e alternância de partições. Cada uma dessas funções é executada usando a instrução [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) .

Para alternar as partições entre duas tabelas, você deve garantir que as partições se alinhem em seus respectivos limites e que as definições de tabela correspondam. Como as restrições de verificação não estão disponíveis para impor o intervalo de valores em uma tabela, a tabela de origem deve conter os mesmos limites de partição que a tabela de destino. Se os limites de partição não forem os mesmos, a opção de partição falhará, pois os metadados de partição não serão sincronizados.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma partição que contém dados
O método mais eficiente para dividir uma partição que já contém dados é usar uma `CTAS` instrução. Se a tabela particionada for um columnstore clusterizado, a partição da tabela deverá estar vazia antes de poder ser dividida.

O exemplo a seguir cria uma tabela columnstore particionada. Ele insere uma linha em cada partição:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

A consulta a seguir localiza a contagem de linhas usando `sys.partitions` a exibição de catálogo:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

O comando de divisão a seguir recebe uma mensagem de erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

MSG 35346, nível 15, estado 1, linha 44 divisão cláusula da instrução ALTER PARTITION falhou porque a partição não está vazia. Somente partições vazias podem ser divididas quando há um índice columnstore na tabela. Considere desabilitar o índice columnstore antes de emitir a instrução ALTER PARTITION e, em seguida, recompilar o índice columnstore após a conclusão de ALTER PARTITION.

No entanto, você `CTAS` pode usar o para criar uma nova tabela para manter os dados.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

À medida que os limites de partição são alinhados, um comutador é permitido. Isso deixará a tabela de origem com uma partição vazia que você pode dividir posteriormente.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que resta é alinhar os dados aos novos limites de partição usando `CTAS`e, em seguida, alternar os dados de volta para a tabela principal.

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Depois de concluir a movimentação dos dados, é uma boa ideia atualizar as estatísticas na tabela de destino. Atualizar estatísticas garante que as estatísticas reflitam com precisão a nova distribuição dos dados em suas respectivas partições.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Carregar novos dados em partições que contêm dados em uma única etapa
Carregar dados em partições com a alternância de partição é uma maneira conveniente de novos dados em uma tabela que não está visível para os usuários na mudança dos novos dados.  Pode ser desafiador em sistemas ocupados para lidar com a contenção de bloqueio associada à alternância de partição.  Para limpar os dados existentes em uma partição, é necessário `ALTER TABLE` ter um usado para mudar os dados.  Em seguida `ALTER TABLE` , outro era necessário para alternar os novos dados.  No SQL data warehouse, a `TRUNCATE_TARGET` opção tem suporte `ALTER TABLE` no comando.  Com `TRUNCATE_TARGET` o`ALTER TABLE` comando, substitui os dados existentes na partição por novos dados.  Abaixo está um exemplo que usa `CTAS` o para criar uma nova tabela com os dados existentes, insere novos dados e, em seguida, alterna todos os dados de volta para a tabela de destino, substituindo os dados existentes.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
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

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Controle de origem de particionamento de tabela
Para evitar a definição de tabela de **Rusting** no sistema de controle do código-fonte, considere a seguinte abordagem:

1. Criar a tabela como uma tabela particionada, mas sem valores de partição

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT`a tabela como parte do processo de implantação:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Com essa abordagem, o código no controle do código-fonte permanece estático e os valores de limite de particionamento podem ser dinâmicos; evoluindo com o warehouse ao longo do tempo.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como desenvolver tabelas, consulte os artigos em [visão geral da tabela](sql-data-warehouse-tables-overview.md).

