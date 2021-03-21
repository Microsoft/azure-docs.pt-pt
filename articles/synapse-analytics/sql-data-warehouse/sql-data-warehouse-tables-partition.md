---
title: Mesas de partilha
description: Recomendações e exemplos para a utilização de divisórias de mesa em piscina SQL dedicada
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8a59c24100b433719ccfd3a9ea1b6a676695d381
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673439"
---
# <a name="partitioning-tables-in-dedicated-sql-pool"></a>Mesas de partição em piscina SQL dedicada

Recomendações e exemplos para a utilização de divisórias de mesa em piscinas SQL dedicadas.

## <a name="what-are-table-partitions"></a>O que são divisórias de mesa?

As divisórias de tabela permitem dividir os seus dados em grupos de dados mais pequenos. Na maioria dos casos, as divisórias de mesa são criadas numa coluna de data. A partição é suportada em todos os tipos de mesas de bilhar SQL dedicados; incluindo loja de colunas agrupadas, índice agrupado e pilha. A partição também é suportada em todos os tipos de distribuição, incluindo tanto o haxixe como o robin redondo distribuídos.  

A partilha pode beneficiar a manutenção de dados e o desempenho da consulta. Se beneficia ambos ou apenas um depende da forma como os dados são carregados e se a mesma coluna pode ser usada para ambos os fins, uma vez que a partição só pode ser feita numa coluna.

### <a name="benefits-to-loads"></a>Benefícios para cargas

O principal benefício da partição no pool de SQL dedicado é melhorar a eficiência e o desempenho dos dados de carregamento através da utilização da eliminação de divisórias, comutação e fusão. Na maioria dos casos, os dados são divididos numa coluna de datas que está intimamente ligada à ordem em que os dados são carregados no pool SQL. Um dos maiores benefícios da utilização de divisórias para manter os dados é evitar a exploração de transações. Ao mesmo tempo que simplesmente inserir, atualizar ou eliminar dados pode ser a abordagem mais simples, com um pouco de pensamento e esforço, usar a partição durante o seu processo de carga pode melhorar substancialmente o desempenho.

A comutação de partição pode ser utilizada para remover ou substituir rapidamente uma secção de uma tabela.  Por exemplo, uma tabela de factos de vendas pode conter apenas dados dos últimos 36 meses. No final de cada mês, os dados de vendas mais antigos são eliminados da tabela.  Estes dados podem ser eliminados utilizando uma declaração de exclusão para apagar os dados do mês mais antigo. 

No entanto, a eliminação de uma grande quantidade de dados linha a linha com uma declaração de eliminação pode demorar demasiado tempo, assim como criar o risco de grandes transações que demoram muito tempo a ser revotadas se algo correr mal. Uma abordagem mais ideal é deixar cair a divisão mais antiga de dados. Onde apagar as fileiras individuais pode levar horas, apagar uma divisória inteira pode levar segundos.

### <a name="benefits-to-queries"></a>Benefícios para consultas

A partição também pode ser usada para melhorar o desempenho da consulta. Uma consulta que aplique um filtro a dados divididos pode limitar a digitalização apenas às divisórias elegíveis. Este método de filtragem pode evitar uma varredura completa da tabela e apenas digitalizar um subconjunto menor de dados. Com a introdução de índices de lojas de colunas agrupados, os benefícios de desempenho de eliminação predicado são menos benéficos, mas em alguns casos pode haver um benefício para as consultas. 

Por exemplo, se a tabela de factos de venda for dividida em 36 meses usando o campo de data de venda, então as consultas que filtram na data de venda podem saltar a procura em divisórias que não correspondem ao filtro.

## <a name="sizing-partitions"></a>Divisórias de dimensionamento

Embora a partição possa ser usada para melhorar o desempenho de alguns cenários, criar uma tabela com **muitas** divisórias pode prejudicar o desempenho em algumas circunstâncias.  Estas preocupações são especialmente verdadeiras para as mesas de lojas de colunas agrupadas. 

Para que a partição seja útil, é importante entender quando usar a partição e o número de divisórias para criar. Não existe uma regra difícil de quantas divisórias são demasiadas, depende dos seus dados e de quantas divisórias carrega simultaneamente. Um esquema de partição bem sucedido geralmente tem dezenas a centenas de divisórias, não milhares.

Ao criar divisórias em **mesas de lojas de colunas agrupadas,** é importante considerar quantas linhas pertencem a cada divisória. Para uma compressão e desempenho ótimos das mesas de loja de colunas agrupadas, é necessário um mínimo de 1 milhão de linhas por distribuição e partição. Antes de serem criadas as divisórias, a piscina SQL dedicada já divide cada mesa em 60 bases de dados distribuídas. 

Qualquer divisória adicionada a uma mesa é além das distribuições criadas nos bastidores. Usando este exemplo, se a tabela de factos de vendas continha 36 divisórias mensais, e dado que um pool de SQL dedicado tem 60 distribuições, então a tabela de factos de vendas deve conter 60 milhões de linhas por mês, ou 2,1 mil milhões de linhas quando todos os meses são povoados. Se uma tabela contiver menos do que o número mínimo recomendado de linhas por partição, considere a utilização de menos divisórias para aumentar o número de linhas por partição. 

Para mais informações, consulte o artigo [indexante,](sql-data-warehouse-tables-index.md) que inclui consultas que podem avaliar a qualidade dos índices de loja de colunas de cluster.

## <a name="syntax-differences-from-sql-server"></a>Diferenças de sintaxe do SQL Server

Piscina de SQL dedicada introduz uma forma de definir divisórias que é mais simples do que o SQL Server. As funções e esquemas de partição não são utilizados em piscinas SQL dedicadas, uma vez que se encontram no SQL Server. Em vez disso, tudo o que precisa fazer é identificar a coluna dividida e os pontos de fronteira. 

Embora a sintaxe da partição possa ser ligeiramente diferente do SQL Server, os conceitos básicos são os mesmos. O SQL Server e o pool de SQL dedicado suportam uma coluna de partição por tabela, que pode ser dividida. Para saber mais sobre a partição, consulte [Tabelas e Índices Divididos.](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

O exemplo a seguir utiliza a declaração [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para dividir a tabela FactInternetSales na coluna OrderDateKey:

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

## <a name="migrating-partitioning-from-sql-server"></a>Partição migratória do SQL Server

Para migrar definições de partição do SQL Server para piscinas SQL dedicadas simplesmente:

- Elimine o esquema de [partição](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)do SQL Server .
- Adicione a definição de [função de partição](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) à sua TABELA CREATE.

Se estiver a migrar uma tabela dividida a partir de uma instância SQL Server, o SQL seguinte pode ajudá-lo a descobrir o número de linhas que em cada divisória. Tenha em mente que se a mesma granularidade de partição for utilizada na piscina de SQL dedicada, o número de linhas por partição diminui em um fator de 60.  

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

## <a name="partition-switching"></a>Comutação de partição

A piscina dedicada SQL suporta a divisão, a fusão e a comutação. Cada uma destas funções é executada utilizando a declaração [ALTER TABLE.](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Para alternar as divisórias entre duas tabelas, deve certificar-se de que as divisórias se alinham nos respetivos limites e que as definições de tabela coincidem. Dado que não existem restrições de verificação para impor o intervalo de valores num quadro, a tabela de origem deve conter os mesmos limites de partição que a tabela-alvo. Se os limites da partição não forem os mesmos, então o interruptor de partição falhará, uma vez que os metadados de partição não serão sincronizados.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma partição que contém dados

O método mais eficiente para dividir uma partição que já contém dados é usar uma `CTAS` declaração. Se a mesa divisória for uma loja de colunas agrupada, então a divisória de mesa deve estar vazia antes de poder ser dividida.

O exemplo a seguir cria uma tabela de colunas dividida. Insere uma linha em cada divisória:

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

A seguinte consulta encontra a contagem de linha utilizando a vista do `sys.partitions` catálogo:

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

O seguinte comando dividido recebe uma mensagem de erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Nível 15, Estado 1, Cláusula de DIVISÃO da linha 44 da declaração de PARTIÇÃO ALTER falhou porque a partição não está vazia. Apenas divisórias vazias podem ser divididas quando existe um índice de loja de colunas na tabela. Considere desativar o índice de loja de colunas antes de emitir a declaração DE DIVISÃO ALTER, em seguida, reconstruir o índice de loja de colunas após a conclusão da PARTIÇÃO DE ALTERA.

No entanto, pode utilizar `CTAS` para criar uma nova tabela para reter os dados.

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

À medida que os limites da partição estão alinhados, é permitido um interruptor. Isto deixará a mesa de origem com uma divisória vazia que pode posteriormente dividir.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que resta é alinhar os dados com os novos limites de partição utilizando `CTAS` , e depois voltar a mudar os dados para a tabela principal.

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

Uma vez concluído o movimento dos dados, é uma boa ideia atualizar as estatísticas na tabela-alvo. A atualização das estatísticas garante que as estatísticas refletem com precisão a nova distribuição dos dados nas respetivas divisórias.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Carregue novos dados em divisórias que contenham dados num só passo

Carregar dados em divisórias com comutação de partição é uma forma conveniente de colocar novos dados numa tabela que não é visível para os utilizadores.  Pode ser um desafio para os sistemas ocupados lidar com a contenção de bloqueio associada à comutação de divisórias.  

Para limpar os dados existentes numa partição, `ALTER TABLE` é necessário que se altere os dados.  Em seguida, outro `ALTER TABLE` foi obrigado a mudar os novos dados.  

Na piscina SQL dedicada, a `TRUNCATE_TARGET` opção é suportada no `ALTER TABLE` comando.  Com `TRUNCATE_TARGET` o comando substitui os `ALTER TABLE` dados existentes na partição com novos dados.  Abaixo está um exemplo que usa `CTAS` para criar uma nova tabela com os dados existentes, insere novos dados e, em seguida, muda todos os dados de volta para a tabela alvo, sobreescrita dos dados existentes.

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

### <a name="table-partitioning-source-control"></a>Controlo de fonte de partição de tabelas

Para evitar que a definição de tabela **enferruje** no seu sistema de controlo de origem, pode considerar a seguinte abordagem:

1. Crie a tabela como uma mesa dividida, mas sem valores de partição

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

Com esta abordagem, o código de controlo de origem permanece estático e os valores de fronteira de partição podem ser dinâmicos; evoluindo com a piscina SQL ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento de tabelas, consulte os artigos na [Visão Geral da Tabela](sql-data-warehouse-tables-overview.md).
