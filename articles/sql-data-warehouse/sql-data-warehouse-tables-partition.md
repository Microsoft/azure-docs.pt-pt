---
title: A criação de partições de tabelas no armazém de dados SQL do Azure | Documentos da Microsoft
description: Recomendações e exemplos de utilização de partições de tabela no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d3557be2fd8fdb459571d2c792302963e17e4471
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189398"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>A criação de partições de tabelas no armazém de dados SQL
Recomendações e exemplos de utilização de partições de tabela no Azure SQL Data Warehouse.

## <a name="what-are-table-partitions"></a>Quais são as partições de tabela?
Partições de tabela permitem dividir os dados em grupos mais pequenos de dados. Na maioria dos casos, as partições de tabela são criadas numa coluna de data. Criação de partições é suportada em todos os tipos de tabela do SQL Data Warehouse; incluindo columnstore em cluster, o índice agrupado e o heap. Criação de partições também é suportada em todos os tipos de distribuição, incluindo hash ou distribuídas por round robin.  

Criação de partições pode trazer benefícios para desempenho de consulta e de manutenção de dados. Se se beneficia ambos ou apenas um é dependente de como os dados são carregados e se a mesma coluna pode ser utilizada para ambos os efeitos, uma vez que a criação de partições apenas pode ser feita numa coluna.

### <a name="benefits-to-loads"></a>Benefícios para cargas
É o principal benefício da criação de partições no SQL Data Warehouse melhorar a eficiência e o desempenho de carregamento de dados através de eliminação de partição, troca e mesclagem. Na maioria dos casos os dados são particionados numa coluna de data que está estreitamente vinculada à ordem na qual os dados são carregados no banco de dados. Um dos maiores benefícios do uso de partições para manter os dados-lo via do registo de transações. Embora simplesmente a inserir, atualizar ou eliminar dados pode ser a abordagem mais direta, com um pouco de reflexão e esforço, usando a criação de partições durante o processo de carregamento pode aprimorar substancialmente o desempenho.

Alternância de partição pode ser utilizado para remover ou substituir uma seção de uma tabela rapidamente.  Por exemplo, uma tabela de fatos de vendas pode conter dados apenas para os últimos meses de 36. No final de cada mês, o mês de dados de vendas mais antigo é eliminado da tabela.  Foi possível eliminar estes dados utilizando uma instrução de eliminação para eliminar os dados para o mês mais antigo. No entanto, a eliminação de uma grande quantidade de dados linha por linha com uma instrução de eliminação pode demorar muito tempo, bem como criar o risco de transações grandes que demoram muito tempo para reversão, caso algo corra mal. Uma abordagem mais ideal é descartar a partição mais antiga dos dados. Onde a eliminar as linhas individuais pode demorar horas, a eliminação de uma partição inteira pode demorar segundos.

### <a name="benefits-to-queries"></a>Benefícios para consultas
Criação de partições também pode ser utilizada para melhorar o desempenho de consulta. Uma consulta que aplica-se um filtro para dados particionados pode limitar a análise para apenas as partições elegíveis. Este método de filtragem pode evitar a análise da tabela completa e analisar apenas um subconjunto mais pequeno de dados. Com a introdução de índices columnstore em cluster, os benefícios de desempenho de eliminação de predicado são menos úteis, mas em alguns casos podem existir um benefício para consultas. Por exemplo, se a tabela de fatos de vendas é particionada em 36 meses usando o campo de data, a consulta esse filtro na data de venda pode ignorar a pesquisa nas partições que não correspondem ao filtro.

## <a name="sizing-partitions"></a>Partições de dimensionamento
Embora a criação de partições pode ser utilizada para melhorar o desempenho de alguns cenários, a criação de uma tabela com **demasiados** partições podem prejudicar o desempenho em algumas circunstâncias.  Estas questões são especialmente verdadeiras para tabelas columnstore em cluster. Para a criação de partições para ser útil, é importante compreender quando deve utilizar a criação de partições e o número de partições para criar. Há nenhuma regra de rápida de disco rígida em relação a quantas partições for demasiado elevado, depende de seus dados e o número de partições que carregar em simultâneo. Um esquema de particionamento bem-sucedida normalmente tem dezenas, centenas de partições, não milhares.

Ao criar partições num **columnstore em cluster** tabelas, é importante considerar o número de linhas pertence a cada partição. Para compressão ideal e o desempenho de tabelas columnstore em cluster, é necessário um mínimo de 1 milhão de linhas por partição e de distribuição. Antes das partições são criadas, o SQL Data Warehouse divide já cada tabela em 60 bases de dados distribuídas. Qualquer criação de partições adicionada a uma tabela é, além das distribuições criadas em segundo plano. Com este exemplo, se a tabela de fatos de vendas incluídas 36 partições mensais e uma vez que o SQL Data Warehouse tem 60 distribuições, em seguida, a tabela de fatos de vendas deve conter linhas de 60 milhões por mês, ou 2.1 milhões de linhas quando todos os meses são preenchidos. Se uma tabela contém menos do que o número recomendado de linhas por partição, considere utilizar menos partições para aumentar o número de linhas por partição. Para obter mais informações, consulte a [indexação](sql-data-warehouse-tables-index.md) artigo, que inclui consultas que podem avaliar a qualidade dos índices columnstore em cluster.

## <a name="syntax-differences-from-sql-server"></a>Diferenças de sintaxe do SQL Server
O SQL Data Warehouse introduz uma maneira de definir as partições que é mais simples do que o SQL Server. As funções de criação de partições e esquemas não são utilizados no SQL Data Warehouse como estão no SQL Server. Em vez disso, tudo o que precisa fazer é identificar a coluna particionada e os pontos de limites. Embora a sintaxe de criação de partições pode ser ligeiramente diferente do SQL Server, os conceitos básicos são os mesmos. SQL Server e SQL Data Warehouse suportam uma coluna de partição por tabela, que pode ser ranged partição. Para saber mais sobre a criação de partições, veja [tabelas Particionadas e índices](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

O exemplo seguinte utiliza a [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) instrução para particionar a tabela FactInternetSales na coluna OrderDateKey:

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

## <a name="migrating-partitioning-from-sql-server"></a>Migrar a criação de partições do SQL Server
Para migrar definições de partição do SQL Server para o SQL Data Warehouse simplesmente:

- Eliminar o SQL Server [esquema de partição](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Adicionar a [função de partição](/sql/t-sql/statements/create-partition-function-transact-sql) definição à sua tabela de criar.

Se estiver a migrar uma tabela particionada de uma instância do SQL Server, o SQL seguinte pode ajudá-lo a descobrir o número de linhas que em cada partição. Tenha em atenção que, se a mesma granularidade de criação de partições é utilizada no SQL Data Warehouse, o número de linhas por partição diminui por um fator de 60.  

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
O SQL Data Warehouse oferece suporte a divisão, mesclagem e alternância de partição. Cada uma dessas funções é executada com o [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) instrução.

Para mudar as partições entre duas tabelas, tem de garantir que as partições alinham em seus respectivos limites e que correspondem às definições de tabela. Como verificar as restrições não estão disponíveis para impor o intervalo de valores numa tabela, a tabela de origem tem de conter os mesmos limites de partição da tabela de destino. Se os limites de partição, em seguida, não são iguais, a alternância de partição irá falhar, não serão sincronizados os metadados da partição.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma partição que contém dados
O método mais eficiente para dividir uma partição que contém os dados já está a utilizar um `CTAS` instrução. Se a tabela particionada é um columnstore em cluster, em seguida, a partição de tabela pode estar vazia antes de este pode ser dividido.

O exemplo seguinte cria uma tabela particionada columnstore. Ele insere uma linha para cada partição:

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

A seguinte consulta encontra a contagem de linhas utilizando o `sys.partitions` vista de catálogo:

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

O seguinte comando de dividir recebe uma mensagem de erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, 15 de nível, 1 de estado, a divisão de 44 linha cláusula da instrução ALTER PARTITION falhou porque a partição não está vazia. Apenas as partições vazias podem ser divididas quando existe um índice columnstore na tabela. Considere desativar o índice columnstore antes de emitir a instrução ALTER PARTITION, em seguida, reconstrua o índice columnstore depois de ALTER PARTITION ter sido concluída.

No entanto, pode usar `CTAS` para criar uma nova tabela para armazenar os dados.

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

Como os limites de partição estão alinhados, é permitido um comutador. Isto deixará a tabela de origem com uma partição vazia que, em seguida, pode dividir.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que resta é alinhar os dados para os novos limites de partição com `CTAS`e, em seguida, mude os dados de volta à tabela principal.

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

Depois de concluir o movimento dos dados, é uma boa idéia para atualizar as estatísticas na tabela de destino. Atualizar as estatísticas garante que as estatísticas refletem com precisão a distribuição nova dos dados em seus respectivos partições.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Carregar novos dados em partições que contêm dados num único passo
Carregar dados para partições com a mudança de partição é uma forma conveniente testar novos dados numa tabela que não é visível para os utilizadores do comutador os novos dados.  Pode ser um desafio em sistemas ocupados para lidar com a contenção de bloqueio associada a alternância de partição.  Para limpar os dados existentes numa partição, uma `ALTER TABLE` costumava ser necessária para alternar os dados.  Em seguida, outra `ALTER TABLE` era necessária para alternar os novos dados.  No SQL Data Warehouse, o `TRUNCATE_TARGET` opção é suportada a `ALTER TABLE` comando.  Com o `TRUNCATE_TARGET` o `ALTER TABLE` comando substitui dados existentes na partição com novos dados.  Segue-se um exemplo que usa `CTAS` criar uma nova tabela com os dados existentes, insere novos dados, e comutadores de todos os dados novamente para a tabela de destino, substituindo os dados existentes.

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

### <a name="table-partitioning-source-control"></a>Controlo de origem de criação de partições de tabela
Para evitar a definição da tabela partir **rusting** no seu sistema de controle de origem, deve considerar a seguinte abordagem:

1. Criar a tabela como uma tabela particionada, mas com nenhum valor de partição

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

1. `SPLIT` a tabela como parte do processo de implantação:

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

Com essa abordagem, o código no controle de origem permanece estático e os valores de limite de criação de partições têm permissão para ser dinâmico; com o armazém a evoluir ao longo do tempo.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o desenvolvimento de tabelas, veja os artigos sobre [descrição geral da tabela](sql-data-warehouse-tables-overview.md).

