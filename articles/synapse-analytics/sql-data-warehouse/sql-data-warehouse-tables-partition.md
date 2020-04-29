---
title: Mesas de partição
description: Recomendações e exemplos para a utilização de divisórias de mesa na piscina SYnapse SQL
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
ms.openlocfilehash: 368276f75128c80b8df326a26acf26c841e9f68a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80742683"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>Mesas de partição na piscina Synapse SQL

Recomendações e exemplos para a utilização de divisórias de mesa na piscina Synapse SQL.

## <a name="what-are-table-partitions"></a>O que são divisórias de mesa?

As divisórias de mesa permitem-lhe dividir os seus dados em grupos de dados mais pequenos. Na maioria dos casos, as divisórias de mesa são criadas numa coluna de data. A partilha é suportada em todos os tipos de mesa de bilhar Synapse SQL; incluindo loja de colunas agrupada, índice agrupado e pilha. A partilha também é suportada em todos os tipos de distribuição, incluindo tanto o hash como o robin redondo distribuídos.  

A partilha pode beneficiar a manutenção de dados e o desempenho da consulta. Quer beneficie tanto ou apenas um depende da forma como os dados são carregados e se a mesma coluna pode ser usada para ambos os fins, uma vez que a partilha só pode ser feita numa coluna.

### <a name="benefits-to-loads"></a>Benefícios para cargas

O principal benefício da partilha no pool SQL synapse é melhorar a eficiência e o desempenho dos dados de carregamento através da eliminação da partilha, comutação e fusão. Na maioria dos casos, os dados são divididos numa coluna de data selada intimamente ligada à ordem em que os dados são carregados na base de dados. Um dos maiores benefícios da utilização de divisórias para manter os dados que evita a exploração madeireira de transações. Embora simplesmente inserir, atualizar ou apagar dados pode ser a abordagem mais simples, com um pouco de pensamento e esforço, usar divisórias durante o seu processo de carga pode melhorar substancialmente o desempenho.

A troca de divisórias pode ser utilizada para remover ou substituir rapidamente uma secção de uma mesa.  Por exemplo, uma tabela de factos de venda pode conter apenas dados nos últimos 36 meses. No final de cada mês, o mês mais antigo dos dados de vendas é apagado da tabela.  Estes dados podem ser eliminados através de uma declaração de eliminação para eliminar os dados relativos ao mês mais antigo. No entanto, eliminar uma grande quantidade de dados consecutivos com uma declaração de exclusão pode demorar demasiado tempo, bem como criar o risco de grandes transações que demoram muito tempo a reverter se algo correr mal. Uma abordagem mais ótima é deixar cair a mais antiga partilha de dados. Onde apagar as linhas individuais pode levar horas, apagar uma partição inteira pode levar segundos.

### <a name="benefits-to-queries"></a>Benefícios para consultas

A partilha também pode ser usada para melhorar o desempenho da consulta. Uma consulta que aplica um filtro a dados divididos pode limitar a varredura apenas às divisórias elegíveis. Este método de filtragem pode evitar uma varredura completa da tabela e apenas digitalizar um subconjunto de dados menor. Com a introdução de índices de lojas de colunas agrupadas, os benefícios de desempenho da eliminação predicada são menos benéficos, mas em alguns casos pode haver um benefício para consultas. Por exemplo, se a tabela de factos de venda for dividida em 36 meses usando o campo de data de venda, então as consultas que filtram na data de venda podem saltar a procura em divisórias que não correspondem ao filtro.

## <a name="sizing-partitions"></a>Partições de tamanho

Embora a partilha possa ser usada para melhorar o desempenho de alguns cenários, criar uma tabela com **demasiadas** divisórias pode prejudicar o desempenho em algumas circunstâncias.  Estas preocupações são especialmente verdadeiras para as tabelas de lojas de colunas agrupadas. Para que a partilha seja útil, é importante entender quando usar a partilha e o número de divisórias para criar. Não existe uma regra rígida e rápida sobre quantas divisórias são demasiadas, depende dos seus dados e de quantas divisórias você carrega simultaneamente. Um esquema de partição bem sucedido geralmente tem dezenas a centenas de divisórias, não milhares.

Ao criar divisórias em mesas de lojas de **colunas agrupadas,** é importante considerar quantas linhas pertencem a cada divisória. Para uma ótima compressão e desempenho das tabelas de colunas agrupadas, é necessário um mínimo de 1 milhão de linhas por distribuição e partição. Antes de as divisórias serem criadas, a piscina SQL synapse já divide cada tabela em 60 bases de dados distribuídas. Qualquer divisória adicionada a uma mesa é além das distribuições criadas nos bastidores. Utilizando este exemplo, se a tabela de factos de venda contivesse 36 divisórias mensais, e dado que um synapse SQL tem 60 distribuições, então a tabela de factos de venda deve conter 60 milhões de filas por mês, ou 2,1 mil milhões de filas quando todos os meses são povoados. Se uma tabela contiver menos do que o número mínimo recomendado de linhas por partição, considere utilizar menos divisórias para aumentar o número de linhas por partição. Para obter mais informações, consulte o artigo [indexante,](sql-data-warehouse-tables-index.md) que inclui consultas que podem avaliar a qualidade dos índices de colunas de cluster.

## <a name="syntax-differences-from-sql-server"></a>Diferenças de sintaxe do SQL Server

O pool SQL synapse introduz uma forma de definir divisórias que são mais simples que o SQL Server. Funções e esquemas de partição não são utilizados na piscina SQL synapse como estão no Servidor SQL. Em vez disso, tudo o que precisa fazer é identificar a coluna dividida e os pontos de fronteira. Embora a sintaxe da partilha possa ser ligeiramente diferente do SQL Server, os conceitos básicos são os mesmos. SQL Server e Synapse SQL suporteuma coluna de partição por tabela, que pode ser dividida com variação. Para saber mais sobre a partilha, consulte [Tabelas e Índices Divididos.](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

O exemplo seguinte utiliza a declaração [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para dividir a tabela FactInternetSales na coluna OrderDateKey:

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

## <a name="migrating-partitioning-from-sql-server"></a>Partição migratória do Servidor SQL

Para migrar as definições de partição do SQL Server para a piscina Synapse SQL simplesmente:

- Elimine o esquema de [partição](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)do Servidor SQL .
- Adicione a definição de [função de partição](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) à sua TABELA CREATE.

Se estiver a migrar uma tabela dividida a partir de uma instância do SQL Server, o Seguinte SQL pode ajudá-lo a descobrir o número de linhas que em cada partição. Tenha em mente que se a mesma granularidade de partição for utilizada na piscina SYnapse SQL, o número de linhas por divisória diminui em um fator de 60.  

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

## <a name="partition-switching"></a>Troca de divisórias

A piscina Synapse SQL suporta a divisão, a fusão e a troca. Cada uma destas funções é executada utilizando a declaração [ALTER TABLE.](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Para trocar divisórias entre duas tabelas, deve certificar-se de que as divisórias se alinham nos respetivos limites e que as definições de tabela correspondem. Dado que não existem restrições de verificação para impor o leque de valores numa tabela, a tabela de origem deve conter os mesmos limites de divisória que a tabela-alvo. Se os limites da divisória não forem iguais, então o interruptor de partição falhará, uma vez que os metadados da divisória não serão sincronizados.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma divisória que contém dados

O método mais eficiente para dividir uma divisória que já contém dados é usar uma `CTAS` declaração. Se a mesa dividida for uma loja de colunas agrupada, a divisória da mesa deve estar vazia antes de poder ser dividida.

O exemplo seguinte cria uma tabela de colunas dividida. Insere uma linha em cada divisória:

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

A seguinte consulta encontra a contagem `sys.partitions` da linha utilizando a vista do catálogo:

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

O comando dividido seguinte recebe uma mensagem de erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Nível 15, Estado 1, Cláusula de DIVISÃO da Linha 44 da declaração de DIVISÓRIA ALTER falhou porque a partição não está vazia. Só divisórias vazias podem ser divididas quando existe um índice de loja de colunas na mesa. Considere desativar o índice de loja de colunas antes de emitir a declaração alter partition e, em seguida, reconstruir o índice de colunas após a divisão alter está completa.

No entanto, `CTAS` pode usar para criar uma nova tabela para reter os dados.

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

À medida que os limites da divisória estão alinhados, é permitido um interruptor. Isto deixará a mesa de origem com uma divisória vazia que poderá posteriormente dividir.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que resta é alinhar os dados `CTAS`com os novos limites de partição usando , e depois mudar os dados de volta para a tabela principal.

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

Uma vez concluído o movimento dos dados, é uma boa ideia refrescar as estatísticas na tabela-alvo. A atualização das estatísticas garante que as estatísticas refletem com precisão a nova distribuição dos dados nas respetivas divisórias.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Carregue novos dados em divisórias que contenham dados num só passo

Carregar dados em divisórias com troca de divisórias é uma forma conveniente de encenar novos dados numa tabela que não é visível para os utilizadores o interruptor nos novos dados.  Pode ser um desafio em sistemas ocupados para lidar com a contenção de bloqueio associada à troca de divisórias.  Para limpar os dados existentes numa `ALTER TABLE` partição, um usado para ser necessário para desativar os dados.  Em `ALTER TABLE` seguida, outro foi obrigado a mudar os novos dados.  Na piscina Synapse SQL, a opção `TRUNCATE_TARGET` é suportada no `ALTER TABLE` comando.  Com `TRUNCATE_TARGET` `ALTER TABLE` o comando substitui os dados existentes na partilha com novos dados.  Abaixo está um `CTAS` exemplo que usa para criar uma nova tabela com os dados existentes, insere novos dados, em seguida, muda todos os dados de volta para a tabela alvo, sobrepondo os dados existentes.

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

### <a name="table-partitioning-source-control"></a>Controlo de fonte de divisão de tabelas

Para evitar que a definição da tabela seja **enferrujada** no seu sistema de controlo de fontes, é melhor considerar a seguinte abordagem:

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

Com esta abordagem, o código de controlo de origem permanece estático e os valores de fronteira de divisão são autorizados a ser dinâmicos; evoluindo com a base de dados ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento de tabelas, consulte os artigos sobre [a visão geral](sql-data-warehouse-tables-overview.md)da tabela .
