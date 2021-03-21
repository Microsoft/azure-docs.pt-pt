---
title: Otimizar as transações
description: Aprenda a otimizar o desempenho do seu código transacional em pool SQL dedicado, minimizando o risco para reversãos longas.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f7d6f8ca285fdc024db9ba952af9f7d169e7188
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678479"
---
# <a name="optimizing-transactions-in-dedicated-sql-pool-in-azure-synapse-analytics"></a>Otimização de transações em pool SQL dedicado em Azure Synapse Analytics

Aprenda a otimizar o desempenho do seu código transacional em pool SQL dedicado, minimizando o risco para reversãos longas.

## <a name="transactions-and-logging"></a>Transações e registos

As transações são um componente importante de um motor de piscina SQL relacional. As transações são utilizadas durante a modificação de dados. Estas transações podem ser explícitas ou implícitas. As declarações de INSERÇÃO, ATUALIZAÇÃO e DELETE são exemplos de transações implícitas. As transações explícitas utilizam O BEGIN TRAN, COMMIT TRAN ou O TRAN DE REVERSÃO. As transações explícitas são normalmente utilizadas quando várias declarações de modificação precisam de ser ligadas numa única unidade atómica.

As alterações na piscina SQL são rastreadas utilizando registos de transações. Cada distribuição tem o seu próprio registo de transações. As gravações de registos de transações são automáticas. Não é necessária configuração. No entanto, embora este processo garanta a escrita, introduz uma sobrecarga no sistema. Pode minimizar este impacto escrevendo código transacionalmente eficiente. O código transacionalmente eficiente enquadra-se, em termos gerais, em duas categorias.

* Utilize construções mínimas de registo sempre que possível
* Processar dados utilizando lotes de âmbito para evitar transações singulares de longo prazo
* Adotar um padrão de comutação de partição para grandes modificações a uma determinada partição

## <a name="minimal-vs-full-logging"></a>Mínimo vs. registo completo

Ao contrário das operações totalmente registadas, que utilizam o registo de transações para acompanhar todas as alterações de linha, as operações minimamente registadas mantêm apenas o registo de alocações de extensão e alterações de meta-dados. Portanto, o registo mínimo envolve apenas registar as informações necessárias para reverter a transação após uma falha, ou para um pedido explícito (ROLLBACK TRAN). Como muito menos informação é rastreada no registo de transações, uma operação minimamente registada executa melhor do que uma operação de tamanho semelhante totalmente registada. Além disso, como menos escritos vão para o registo de transações, uma quantidade muito menor de dados de registo é gerada e, por isso, é mais eficiente em E/O.

Os limites de segurança da transação aplicam-se apenas às operações totalmente registadas.

> [!NOTE]
> As operações com registo mínimo podem participar em transações explícitas. À medida que todas as alterações nas estruturas de atribuição são rastreadas, é possível reverter as operações minimamente registadas.

## <a name="minimally-logged-operations"></a>Operações minimamente registadas

As seguintes operações são capazes de ser minimamente registadas:

* CRIAR TABELA COMO SELECT[(CTAS)](sql-data-warehouse-develop-ctas.md)
* INSIRA.. SELECIONE
* CREATE INDEX
* ALTERAR O ÍNDICE RECONSTRUIR
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTERAR PARTILHA DO INTERRUPTOR DE MESA

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> As operações internas de movimento de dados (como a BROADCAST e a SHUFFLE) não são afetadas pelo limite de segurança da transação.

## <a name="minimal-logging-with-bulk-load"></a>Registo mínimo com carga a granel

CTAS e INSERT... SELECT são ambas operações de carga a granel. No entanto, ambos são influenciados pela definição da tabela-alvo e dependem do cenário de carga. O quadro seguinte explica quando as operações a granel são registadas total ou minimamente:  

| Índice Primário | Cenário de carga | Modo de Registo |
| --- | --- | --- |
| Área dinâmica para dados |Qualquer |**Mínimo** |
| Índice Agrupado |Mesa-alvo vazia |**Mínimo** |
| Índice Agrupado |Linhas carregadas não se sobrepõem com páginas existentes no alvo |**Mínimo** |
| Índice Agrupado |Linhas carregadas sobrepõem-se com páginas existentes no alvo |Completa |
| Índice de Loja de Colunas Agrupada |Tamanho do lote >= 102.400 por distribuição alinhada de partição |**Mínimo** |
| Índice de Loja de Colunas Agrupada |Tamanho do lote < 102.400 por distribuição alinhada de partição |Completa |

Vale a pena notar que quaisquer escritos para atualizar índices secundários ou não agrupados serão sempre operações totalmente registadas.

> [!IMPORTANT]
> Uma piscina SQL dedicada tem 60 distribuições. Portanto, assumindo que todas as linhas são distribuídas uniformemente e aterrando numa única divisória, o seu lote terá de conter 6.144.000 linhas ou maiores para ser minimamente registado ao escrever para um Índice de Colunas Agrupadas. Se a tabela for dividida e as linhas inseridas forem limites de partição de extensão, então você precisará de 6.144.000 linhas por limite de partição assumindo até mesmo a distribuição de dados. Cada divisória em cada distribuição deve exceder independentemente o limiar de 102.400 linhas para que o inserível seja minimamente registado na distribuição.

Carregar dados numa tabela não vazia com um índice agrupado pode muitas vezes conter uma mistura de linhas totalmente registadas e minimamente registadas. Um índice agrupado é uma árvore equilibrada (árvore b) de páginas. Se a página que está a ser escrita já contiver linhas de outra transação, então estas gravações serão totalmente registadas. No entanto, se a página estiver vazia, a escrita para essa página será minimamente registada.

## <a name="optimizing-deletes"></a>Otimização elimina

DELETE é uma operação totalmente iniciada.  Se precisar de eliminar uma grande quantidade de dados numa tabela ou numa divisória, muitas vezes faz mais sentido para `SELECT` os dados que pretende guardar, que podem ser executados como uma operação minimamente registada.  Para selecionar os dados, crie uma nova tabela com [CTAS](sql-data-warehouse-develop-ctas.md).  Uma vez criado, use [o RENAME](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para trocar a sua mesa antiga com a mesa recém-criada.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Otimização de atualizações

UPDATE é uma operação totalmente iniciada.  Se precisar de atualizar um grande número de linhas numa mesa ou numa divisória, muitas vezes pode ser muito mais eficiente utilizar uma operação minimamente registada, como [ctas](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para o fazer.

No exemplo abaixo, uma atualização completa da tabela foi convertida para um CTAS para que a exploração de registo mínimo seja possível.

Neste caso, estamos retrospetivamente adicionando um valor de desconto às vendas na tabela:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Recriar grandes mesas pode beneficiar da utilização de funcionalidades dedicadas de gestão da carga de trabalho da piscina SQL. Para obter mais informações, consulte [as classes de Recursos para a gestão da carga de trabalho.](resource-classes-for-workload-management.md)

## <a name="optimizing-with-partition-switching"></a>Otimização com comutação de partição

Se confrontado com modificações em larga escala dentro de uma divisória de [mesa,](sql-data-warehouse-tables-partition.md)então um padrão de comutação de divisórias faz sentido. Se a modificação de dados for significativa e abranger várias divisórias, então a imersão sobre as divisórias obtém o mesmo resultado.

Os passos para realizar um interruptor de partição são os seguintes:

1. Criar uma divisória vazia
2. Execute a 'actualização' como CTAS
3. Mude os dados existentes para a tabela de out
4. Mudar os novos dados
5. Limpe os dados

No entanto, para ajudar a identificar as divisórias para mudar, crie o seguinte procedimento de ajuda.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Este procedimento maximiza a reutilização de códigos e mantém o exemplo de comutação de partição mais compacto.

O código a seguir demonstra os passos mencionados anteriormente para obter uma rotina completa de comutação de partição.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimizar a exploração madeireira com pequenos lotes

Para grandes operações de modificação de dados, pode fazer sentido dividir a operação em pedaços ou lotes para estender a unidade de trabalho.

Um código seguinte é um exemplo de trabalho. O tamanho do lote foi definido para um número trivial para realçar a técnica. Na realidade, o tamanho do lote seria significativamente maior.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Orientação de pausa e escala

Piscina DEDICADA SQL permite-lhe [parar, retomar e escalar](sql-data-warehouse-manage-compute-overview.md) a sua piscina SQL dedicada a pedido. Quando você pausa ou escala o seu pool DE SQL dedicado, é importante entender que quaisquer transações a bordo são terminadas imediatamente; fazendo com que quaisquer transações abertas sejam revertidas. Se a sua carga de trabalho tivesse emitido uma longa e incompleta modificação de dados antes da pausa ou da operação de escala, então este trabalho terá de ser desfeito. Esta desfasamento pode ter impacto no tempo que leva para parar ou escalar a sua piscina DE SQL dedicada.

> [!IMPORTANT]
> Ambas `UPDATE` e `DELETE` são operações totalmente registadas e, por isso, estas operações de desfazer/refazer podem demorar significativamente mais tempo do que operações minimamente registadas equivalentes.

O melhor cenário é deixar as transações de modificação de dados de voo concluídas antes de fazer uma pausa ou escalar um pool de SQL dedicado. No entanto, este cenário pode nem sempre ser prático. Para mitigar o risco de uma longa reversão, considere uma das seguintes opções:

* Reescrever operações de longo prazo utilizando [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* Parta a operação em pedaços; operando num subconjunto das linhas

## <a name="next-steps"></a>Passos seguintes

Consulte [transações em pool SQL dedicado](sql-data-warehouse-develop-transactions.md) para saber mais sobre os níveis de isolamento e limites transacionais.  Para uma visão geral de outras Boas Práticas, consulte as [melhores práticas dedicadas ao pool SQL.](sql-data-warehouse-best-practices.md)
