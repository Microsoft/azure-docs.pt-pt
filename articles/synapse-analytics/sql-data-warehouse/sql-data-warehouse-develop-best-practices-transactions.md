---
title: Otimizar as transações
description: Aprenda a otimizar o desempenho do seu código transacional no Synapse SQL, minimizando o risco de retrocessos longos.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0139c581e6660622f1ab6db9f407725816377a6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633569"
---
# <a name="optimizing-transactions-in-synapse-sql"></a>Otimização de transações no Synapse SQL

Aprenda a otimizar o desempenho do seu código transacional no Synapse SQL, minimizando o risco de retrocessos longos.

## <a name="transactions-and-logging"></a>Transações e exploração madeireira

As transações são um componente importante de um motor de base de dados relacional. As transações são utilizadas durante a modificação de dados. Estas transações podem ser explícitas ou implícitas. As declarações de INSIRA, ATUALIZAÇÃO e DELETE são todos exemplos de transações implícitas. As transações explícitas utilizam o BEGIN TRAN, o COMMIT TRAN ou o ROLLBACK TRAN. As transações explícitas são normalmente utilizadas quando várias declarações de modificação precisam de ser ligadas numa única unidade atómica.

As alterações na base de dados são rastreadas utilizando registos de transações. Cada distribuição tem o seu próprio registo de transações. Os registos de transações são automáticos. Não é necessária nenhuma configuração. No entanto, embora este processo garanta a escrita, introduz uma sobrecarga no sistema. Pode minimizar este impacto escrevendo código sinuoso e eficiente. O código transaccionalmente eficiente enquadra-se, em geral, em duas categorias.

* Utilize construções mínimas de exploração madeireira sempre que possível
* Processar dados utilizando lotes com âmbito de aplicação para evitar transações singulares de longo prazo
* Adote um padrão de comutação de divisórias para grandes modificações numa determinada partição

## <a name="minimal-vs-full-logging"></a>Mínimo vs. exploração madeireira completa

Ao contrário das operações totalmente registadas, que utilizam o registo de transações para acompanhar todas as alterações de linha, as operações minimamente registadas registam apenas a atribuição de extensão e alterações de meta-dados. Portanto, o registo mínimo envolve o registo apenas das informações necessárias para reverter a transação após uma falha, ou para um pedido explícito (ROLLBACK TRAN). Uma vez que muito menos informação é rastreada no registo de transações, uma operação minimamente registada executa melhor do que uma operação de tamanho semelhante totalmente registada. Além disso, como menos escritos vão o registo de transações, uma quantidade muito menor de dados de registo é gerada e assim é mais eficiente em E/S.

Os limites de segurança da transação aplicam-se apenas às operações totalmente registadas.

> [!NOTE]
> As operações minimamente registadas podem participar em transações explícitas. À medida que todas as alterações nas estruturas de atribuição são rastreadas, é possível reverter as operações minimamente registadas.

## <a name="minimally-logged-operations"></a>Operações minimamente registadas

As seguintes operações são capazes de ser minimamente registadas:

* CRIAR TABELA COMO SELECIONADO[(CTAS)](sql-data-warehouse-develop-ctas.md)
* INSERIR. SELECIONE
* CREATE INDEX
* ALTERAR RECONSTRUÇÃO DO ÍNDICE
* DROP INDEX
* Tabela TRUNCATE
* MESA DE LANÇAMENTO
* ALTER DIVISÓRIA DE INTERRUPTOR DE MESA

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> As operações internas de circulação de dados (como broadcast e SHUFFLE) não são afetadas pelo limite de segurança da transação.

## <a name="minimal-logging-with-bulk-load"></a>Extração mínima com carga a granel

CTAS e INSERT... SELECT são ambas as operações de carga a granel. No entanto, ambos são influenciados pela definição da tabela-alvo e dependem do cenário de carga. O quadro que se segue explica quando as operações a granel são totalmente ou minimamente registadas:  

| Índice Primário | Cenário de carga | Modo de exploração madeireira |
| --- | --- | --- |
| Área dinâmica para dados |Qualquer |**Mínimo** |
| Índice Agrupado |Mesa-alvo vazia |**Mínimo** |
| Índice Agrupado |Linhas carregadas não se sobrepõem com páginas existentes no alvo |**Mínimo** |
| Índice Agrupado |Linhas carregadas sobrepõem-se com páginas existentes no alvo |Completa |
| Índice de Colunas Agrupadas |Tamanho do lote >= 102.400 por distribuição alinhada por divisória |**Mínimo** |
| Índice de Colunas Agrupadas |Tamanho do lote < 102.400 por distribuição alinhada por divisória |Completa |

Vale a pena notar que quaisquer escritos para atualizar índices secundários ou não agrupados serão sempre operações totalmente registadas.

> [!IMPORTANT]
> Uma base de dados de piscina Synapse SQL tem 60 distribuições. Portanto, assumindo que todas as linhas estão distribuídas uniformemente e aterrando numa única divisória, o seu lote terá de conter 6.144.000 linhas ou maiores para ser minimamente registado ao escrever para um Índice de Colunas Agrupadas. Se a tabela for dividida e as linhas inseridas forem inseridas, então precisará de 6.144.000 linhas por limite de divisória assumindo a distribuição uniforme de dados. Cada partição em cada distribuição deve exceder independentemente o limiar de 102.400 linhas para que a inserção seja minimamente registada na distribuição.

Carregar dados numa tabela não vazia com um índice agrupado pode muitas vezes conter uma mistura de linhas totalmente registadas e minimamente registadas. Um índice agrupado é uma árvore equilibrada (b-árvore) de páginas. Se a página que está a ser escrita já contiver linhas de outra transação, então estas escritas serão totalmente registadas. No entanto, se a página estiver vazia, então a escrita para essa página será minimamente registada.

## <a name="optimizing-deletes"></a>Otimizar elimina

Delete é uma operação totalmente registada.  Se precisar de eliminar uma grande quantidade de dados numa tabela ou `SELECT` numa divisória, muitas vezes faz mais sentido para os dados que pretende guardar, que podem ser executados como uma operação minimamente registada.  Para selecionar os dados, crie uma nova tabela com [CTAS](sql-data-warehouse-develop-ctas.md).  Uma vez criado, use [RENAME](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para trocar a sua mesa antiga com a mesa recém-criada.

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

Update é uma operação totalmente registada.  Se precisar de atualizar um grande número de linhas numa tabela ou numa partição, muitas vezes pode ser muito mais eficiente utilizar uma operação minimamente registada, como [as CTAS,](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para o fazer.

No exemplo abaixo, foi convertida uma atualização completa do quadro para um CTAS para que seja possível o abate mínimo.

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
> Recriar grandes tabelas pode beneficiar da utilização de funcionalidades de gestão de carga de trabalho de piscina Synapse SQL. Para mais informações, consulte [as classes de recursos para gestão](resource-classes-for-workload-management.md)da carga de trabalho.

## <a name="optimizing-with-partition-switching"></a>Otimização com comutação de divisórias

Se confrontado com modificações em larga escala dentro de uma [divisória](sql-data-warehouse-tables-partition.md)de mesa, então um padrão de comutação de divisória faz sentido. Se a modificação de dados for significativa e abranger várias divisórias, então a epísero sobre as divisórias obtém o mesmo resultado.

Os passos para executar um interruptor de partição são os seguintes:

1. Criar uma divisória vazia
2. Execute a 'actualização' como CTAS
3. Mude os dados existentes para a tabela de saída
4. Alternar os novos dados
5. Limpar os dados

No entanto, para ajudar a identificar as divisórias para mudar, crie o seguinte procedimento de ajudante.  

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

Este procedimento maximiza a reutilização do código e mantém o exemplo de comutação de divisórias mais compacto.

O código que se segue demonstra os passos mencionados anteriormente para alcançar uma rotina completa de comutação de divisórias.

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

Para grandes operações de modificação de dados, pode fazer sentido dividir a operação em pedaços ou lotes para examinar a unidade de trabalho.

Um seguinte código é um exemplo de funcionamento. O tamanho do lote foi definido para um número trivial para destacar a técnica. Na realidade, o tamanho do lote seria significativamente maior.

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

## <a name="pause-and-scaling-guidance"></a>Pausa e orientação de escala

Synapse SQL permite-lhe [parar, retomar e escalar](sql-data-warehouse-manage-compute-overview.md) a sua piscina SQL a pedido. Quando você pausa ou escala o seu pool SQL, é importante entender que quaisquer transações a bordo são terminadas imediatamente; fazendo com que quaisquer transações abertas sejam revertidas. Se a sua carga de trabalho tiver emitido uma modificação de dados de longo curso e incompleta antes da pausa ou operação de escala, então este trabalho terá de ser desfeito. Esta destruição pode ter impacto no tempo que leva para parar ou escalar a sua piscina SQL.

> [!IMPORTANT]
> Ambos `UPDATE` `DELETE` e stão a ser operações totalmente registadas, pelo que estas operações de desfazer/redo podem demorar significativamente mais tempo do que as operações mínimas equivalentes.

O melhor cenário é permitir que as transações de modificação de dados de voo sejam concluídas antes de fazer uma pausa ou escalonar o pool SQL. No entanto, este cenário pode nem sempre ser prático. Para mitigar o risco de uma longa reversão, considere uma das seguintes opções:

* Reescrever operações de longo prazo utilizando [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* Quebrar a operação em pedaços; operando em um subconjunto das linhas

## <a name="next-steps"></a>Passos seguintes

Consulte [as transações em Synapse SQL](sql-data-warehouse-develop-transactions.md) para saber mais sobre os níveis de isolamento e os limites transacionais.  Para uma visão geral de outras Boas Práticas, consulte as [Melhores Práticas do Armazém de Dados SQL](sql-data-warehouse-best-practices.md).
