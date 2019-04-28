---
title: Otimizar as transações do Azure SQL Data Warehouse | Documentos da Microsoft
description: Saiba como otimizar o desempenho do seu código transacional no Azure SQL Data Warehouse, minimizando o risco de reversões longo.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/19/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: f5e0b2b75ac111f3221108936f84e5883aebfc1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478832"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Otimizar as transações no Azure SQL Data Warehouse
Saiba como otimizar o desempenho do seu código transacional no Azure SQL Data Warehouse, minimizando o risco de reversões longo.

## <a name="transactions-and-logging"></a>Registo de transações e
As transações são um componente importante de um mecanismo de banco de dados relacional. SQL Data Warehouse utiliza transações durante a modificação de dados. Essas transações podem ser explícito ou implícito. Instruções de único INSERT, UPDATE e DELETE são todos os exemplos de transações implícitas. Transações explícitas utilizam BEGIN TRAN, TRAN consolidação ou reversão TRAN. Transações explícitas são normalmente utilizadas quando várias instruções de modificação tem de ser transformados numa unidade atômica única. 

O Azure SQL Data Warehouse confirma as alterações à base de dados com os registos de transação. Cada distribuição tem seu próprio log de transação. Escritas de log de transação são automáticas. Não existe nenhuma configuração necessária. No entanto, embora este processo garante a gravação de introduzir uma sobrecarga no sistema. Pode minimizar esse impacto ao escrever código eficiente ao nível das transações. Código eficiente ao nível das transações amplamente cair em duas categorias.

* Utilize construções sempre que possível de registo mínimo
* Processar dados com um âmbito batches para evitar singulares transações de longa execução
* Adotar uma padrão de grandes alterações de uma determinada partição da mudança de partições

## <a name="minimal-vs-full-logging"></a>Mínimo vs. registo completo
Ao contrário das operações totalmente conectadas, o que utilizam o registo de transações para controlar todas as alterações de linha, operações minimamente registradas mantenha um registo de alocações de extensão e apenas alterações de metadados. Por conseguinte, o registo mínimo envolve registo apenas as informações necessárias para reverter a transação após uma falha, ou para uma solicitação explícita (reversão TRAN). Como muito menos informações são controladas no registo de transações, uma operação registada minimamente executa melhor do que uma operação de totalmente registada tamanho da mesma forma. Além disso, uma vez que as gravações menos passam o registo de transações, é gerada uma menor quantidade de dados de registo e é por isso, e/s mais eficiente.

Os limites de segurança de transação aplicam-se apenas às operações totalmente com sessão iniciadas.

> [!NOTE]
> Operações minimamente registradas podem participar em transações explícitas. Como todas as alterações nas estruturas de alocação são controladas, é possível reverter operações minimamente registradas. 
> 
> 

## <a name="minimally-logged-operations"></a>Operações minimamente registradas
As seguintes operações são capazes de que está sendo registrado no mínimo:

* CRIAR TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERT..SELECT
* CRIAR ÍNDICE
* ALTER RECOMPILAÇÃO DE ÍNDICE
* REMOVER O ÍNDICE
* TRUNCAR A TABELA
* REMOVER TABELA
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Operações de movimento de dados internos (como difusão e SHUFFLE) não são afetadas pelo limite de segurança da transação.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Um mínimo de iniciar sessão com o carregamento em massa
CTAS e INSERT... SELECIONE é as duas operações de carregamento em massa. No entanto, ambos são influenciados pela definição da tabela de destino e dependem do cenário de carga. A tabela seguinte explica quando operações em massa são totalmente ou minimamente registadas:  

| Índice principal | Cenário de carga | Modo de registo |
| --- | --- | --- |
| Área dinâmica para dados |Qualquer |**Mínimo** |
| Índice em cluster |Tabela de destino vazia |**Mínimo** |
| Índice em cluster |Linhas carregadas não se sobrepõem com páginas existentes no destino |**Mínimo** |
| Índice em cluster |Linhas de carregá-lo se sobrepõe a páginas existentes no destino |Completo |
| Índice Columnstore em cluster |Tamanho do lote > = 102,400 por partição alinhada distribuição |**Mínimo** |
| Índice Columnstore em cluster |Tamanho < 102,400 por partição alinhada com a distribuição do batch |Completo |

Vale a pena observar que qualquer escritas para atualizar índices secundários ou não em cluster será sempre operações totalmente registadas.

> [!IMPORTANT]
> SQL Data Warehouse tem 60 distribuições. Por conseguinte, supondo que todas as linhas sejam igualmente distribuídas e de destino numa única partição, o batch tem de conter 6,144,000 linhas ou maior do que no mínimo, será registado escrita em índices Columnstore em cluster. Se a tabela está particionada e as linhas que estão sendo inseridas span limites de partição, em seguida, precisará 6,144,000 linhas por limites de partição, partindo do princípio de distribuição de dados uniforme. Cada partição em cada distribuição independentemente deve exceder o limiar de 102,400 linhas para a inserção minimamente ter sessão iniciada para a distribuição.
> 
> 

Carregar dados para uma tabela não vazia com um índice agrupado, muitas vezes, pode conter uma mistura de linhas totalmente conectadas e minimamente registradas. Um índice em cluster é uma árvore balanceada (árvore b) de páginas. Se a página a ser escritos já contém linhas de outra transação, em seguida, essas escritas serão totalmente registadas. No entanto, se a página está vazia, em seguida, a escrita a essa página será minimamente registada.

## <a name="optimizing-deletes"></a>Otimizar as eliminações
DELETE é uma operação registada totalmente.  Se precisar de eliminar uma grande quantidade de dados numa tabela ou de uma partição, muitas vezes, faz mais sentido `SELECT` os dados que deseja manter, que podem ser executados como uma operação minimamente registrada.  Para selecionar os dados, criar uma nova tabela com [CTAS](sql-data-warehouse-develop-ctas.md).  Depois de criado, utilize [mudar o nome](/sql/t-sql/statements/rename-transact-sql) alternar sua tabela antiga com a tabela recém-criada.

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

## <a name="optimizing-updates"></a>Otimizar as atualizações
ATUALIZAÇÃO é uma operação registada totalmente.  Se precisar de atualizar um grande número de linhas numa tabela ou de uma partição, geralmente é muito mais eficiente para utilizar como uma operação registada minimamente [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para fazer isso.

No exemplo abaixo uma tabela completa atualização foi convertida num CTAS para que o registo mínimo é possível.

Neste caso, estamos retrospectively a adicionar um valor do desconto para as vendas na tabela:

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
> Volte a criar tabelas grandes pode beneficiar da utilização de recursos de gerenciamento de carga de trabalho do armazém de dados SQL. Para obter mais informações, consulte [classes de recursos para a gestão da carga de trabalho](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Otimizando o com a mudança de partição
Se deparar com modificações em grande escala dentro de um [partição de tabela](sql-data-warehouse-tables-partition.md), em seguida, uma partição de troca padrão faz sentido. Se a modificação de dados é significativa e se estende por várias partições, em seguida, iterar sobre as partições alcança o mesmo resultado.

Os passos para efetuar uma alternância de partição são os seguintes:

1. Criar vazio partição
2. Efetuar a atualização como um CTAS
3. Mudar os dados existentes para a tabela de saída
4. Mude os novos dados
5. Limpar os dados

No entanto, para ajudar a identificar as partições para mudar, crie o seguinte procedimento auxiliar.  

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

Este procedimento maximiza a reutilização de código e mantém a exemplo mais compacto da mudança de partições.

O código a seguir demonstra os passos mencionados anteriormente para alcançar uma rotina de mudança de partições completa.

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

## <a name="minimize-logging-with-small-batches"></a>Minimizar o registo com pequenos lotes
Para operações de modificação de dados grandes, talvez faça sentido para dividir a operação em segmentos ou de lotes para definir o âmbito a unidade de trabalho.

Um código a seguir é um exemplo funcional. O tamanho do lote já foi definido como um número trivial para destacar a técnica. Na realidade, o tamanho do lote seria significativamente maior. 

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

## <a name="pause-and-scaling-guidance"></a>Colocar em pausa e orientações de dimensionamento
O Azure SQL Data Warehouse permite-lhe [colocar em pausa, retomar e dimensionar](sql-data-warehouse-manage-compute-overview.md) seu armazém de dados a pedido. Ao colocar em pausa ou dimensionar o seu armazém de dados SQL, é importante compreender que todas as transações em trânsito estão terminadas imediatamente; fazendo com que todas as transações abertas ser revertidas. Se a carga de trabalho tinha emitido uma modificação de dados incompleta e em execução há muito tempo antes da operação de colocar em pausa ou dimensionamento, em seguida, esse trabalho terão de ser anulada. Este anular pode afetar o tempo que demora a colocar em pausa ou dimensionar a sua base de dados do Azure SQL Data Warehouse. 

> [!IMPORTANT]
> Ambos `UPDATE` e `DELETE` são operações totalmente registradas e para que estes desfazer/refazer operações podem demorar significativamente mais do que o equivalente minimamente conectado de operações. 
> 
> 

É o melhor cenário permitir a entrada de transações de modificação de dados de voo concluídas antes de colocar em pausa ou dimensionar o SQL Data Warehouse. No entanto, este cenário poderá nem sempre ser prático. Para mitigar o risco de uma reversão longa, considere uma das seguintes opções:

* Reescrever através de operações de longa execução [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Quebrar a operação em segmentos; operando num subconjunto das linhas

## <a name="next-steps"></a>Passos Seguintes
Ver [transações no SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) para saber mais sobre os níveis de isolamento e limites transacionais.  Para uma descrição geral de outras práticas recomendadas, consulte [práticas recomendadas do armazém de dados SQL](sql-data-warehouse-best-practices.md).

