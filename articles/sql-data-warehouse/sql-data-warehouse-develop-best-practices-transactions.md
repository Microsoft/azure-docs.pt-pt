---
title: Otimizando transações
description: Saiba como otimizar o desempenho do seu código transacional no Azure SQL Data Warehouse, minimizando o risco de reversões longas.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b8b8be9467ade870e57355be91b0de329b0f6217
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692859"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Otimizando transações no Azure SQL Data Warehouse
Saiba como otimizar o desempenho do seu código transacional no Azure SQL Data Warehouse, minimizando o risco de reversões longas.

## <a name="transactions-and-logging"></a>Transações e registro em log
As transações são um componente importante de um mecanismo de banco de dados relacional. SQL Data Warehouse usa transações durante a modificação de dados. Essas transações podem ser explícitas ou implícitas. Instruções INSERT, UPDATE e DELETE únicas são exemplos de transações implícitas. As transações explícitas usam BEGIN TRAN, COMMIT TRAN ou ROLLBACK TRAN. As transações explícitas normalmente são usadas quando várias instruções de modificação precisam ser ligadas juntas em uma única unidade atômica. 

O Azure SQL Data Warehouse confirma as alterações no banco de dados usando logs de transação. Cada distribuição tem seu próprio log de transações. As gravações do log de transações são automáticas. Não há nenhuma configuração necessária. No entanto, embora esse processo garanta a gravação, ele introduz uma sobrecarga no sistema. Você pode minimizar esse impacto escrevendo código transacionalmente eficiente. O código transacionalmente eficiente se enquadra amplamente em duas categorias.

* Use construções de log mínimas sempre que possível
* Processar dados usando lotes com escopo para evitar transações de longa execução singulares
* Adote um padrão de troca de partição para grandes modificações em uma determinada partição

## <a name="minimal-vs-full-logging"></a>Mínimo vs. log completo
Ao contrário de operações totalmente registradas, que usam o log de transações para controlar todas as alterações de linha, as operações minimamente registradas controlam as alocações de extensão e as alterações de metadados. Portanto, o registro em log mínimo envolve registrar somente as informações necessárias para reverter a transação após uma falha ou para uma solicitação explícita (ROLLBACK TRAN). À medida que menos informações são rastreadas no log de transações, uma operação minimamente registrada tem um desempenho melhor do que uma operação totalmente registrada no tamanho da mesma forma. Além disso, como menos gravações passam pelo log de transações, uma quantidade muito menor de dados de log é gerada e, portanto, é mais eficiente de e/s.

Os limites de segurança da transação se aplicam somente a operações totalmente registradas em log.

> [!NOTE]
> Operações minimamente registradas podem participar de transações explícitas. Como todas as alterações nas estruturas de alocação são controladas, é possível reverter operações minimamente registradas. 
> 
> 

## <a name="minimally-logged-operations"></a>Operações minimamente registradas
As seguintes operações podem ser minimamente registradas em log:

* CREATE TABLE como SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERIR.. Não
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* REMOVER TABELA
* ALTERAR PARTIÇÃO DO COMUTADOR DE TABELA

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> As operações de movimentação de dados internas (como difusão e ordem aleatória) não são afetadas pelo limite de segurança da transação.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Log mínimo com carregamento em massa
CTAS e INSERT... SELECT são ambas operações de carregamento em massa. No entanto, ambos são influenciados pela definição da tabela de destino e dependem do cenário de carregamento. A tabela a seguir explica quando as operações em massa são totalmente ou minimamente registradas em log:  

| Índice primário | Cenário de carregamento | Modo de log |
| --- | --- | --- |
| Área dinâmica para dados |Qualquer |**Muito** |
| Índice clusterizado |Tabela de destino vazia |**Muito** |
| Índice clusterizado |As linhas carregadas não se sobrepõem às páginas existentes no destino |**Muito** |
| Índice clusterizado |Linhas carregadas sobrepõem-se às páginas existentes no destino |Completo |
| Índice Columnstore clusterizado |Tamanho do lote > = 102.400 por distribuição alinhada por partição |**Muito** |
| Índice Columnstore clusterizado |Tamanho do lote < 102.400 por distribuição alinhada por partição |Completo |

Vale a pena observar que qualquer gravação para atualizar índices secundários ou não clusterizados será sempre operações totalmente registradas em log.

> [!IMPORTANT]
> SQL Data Warehouse tem 60 distribuições. Portanto, supondo que todas as linhas sejam distribuídas uniformemente e sejam iniciadas em uma única partição, seu lote precisará conter 6.144.000 linhas ou mais para ser minimamente registrado ao gravar em um índice Columnstore clusterizado. Se a tabela for particionada e as linhas que estão sendo inseridas ultrapassarem os limites de partição, você precisará de 6.144.000 linhas por limite de partição presumindo até mesmo distribuição de dados. Cada partição em cada distribuição deve exceder de forma independente o limite de 102.400 linhas para que a inserção seja minimamente registrada na distribuição.
> 
> 

Carregar dados em uma tabela não vazia com um índice clusterizado geralmente pode conter uma mistura de linhas totalmente registradas e registradas minimamente. Um índice clusterizado é uma árvore equilibrada (árvore b) de páginas. Se a página que está sendo gravada já contiver linhas de outra transação, essas gravações serão totalmente registradas. No entanto, se a página estiver vazia, a gravação nessa página será registrada minimamente.

## <a name="optimizing-deletes"></a>Otimizando exclusões
DELETE é uma operação totalmente registrada em log.  Se você precisar excluir uma grande quantidade de dados em uma tabela ou em uma partição, geralmente faz mais sentido `SELECT` os dados que você deseja manter, que podem ser executados como uma operação minimamente registrada em log.  Para selecionar os dados, crie uma nova tabela com [CTAS](sql-data-warehouse-develop-ctas.md).  Depois de criado, use [renomear](/sql/t-sql/statements/rename-transact-sql) para alternar a tabela antiga com a tabela recém-criada.

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

## <a name="optimizing-updates"></a>Otimizando atualizações
UPDATE é uma operação totalmente registrada em log.  Se você precisar atualizar um grande número de linhas em uma tabela ou em uma partição, muitas vezes pode ser muito mais eficiente usar uma operação minimamente registrada em log, como [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para fazer isso.

No exemplo abaixo, uma atualização de tabela completa foi convertida em um CTAS para que o registro em log mínimo seja possível.

Nesse caso, estamos adicionando de forma retrospectiva um valor de desconto às vendas na tabela:

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
> Recriar tabelas grandes pode se beneficiar do uso de SQL Data Warehouse recursos de gerenciamento de carga de trabalho. Para obter mais informações, consulte [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Otimizando com a alternância de partição
Se estiver enfrentando modificações em larga escala dentro de uma [partição de tabela](sql-data-warehouse-tables-partition.md), um padrão de troca de partição faz sentido. Se a modificação de dados for significativa e abranger várias partições, a iteração pelas partições atingirá o mesmo resultado.

As etapas para executar uma opção de partição são as seguintes:

1. Criar uma partição vazia
2. Executar a ' atualização ' como um CTAS
3. Mudar os dados existentes para a tabela de saída
4. Alternar os novos dados
5. Limpar os dados

No entanto, para ajudar a identificar as partições a serem comutadas, crie o seguinte procedimento auxiliar.  

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

Esse procedimento maximiza a reutilização de código e mantém o exemplo de alternância de partição mais compacto.

O código a seguir demonstra as etapas mencionadas anteriormente para obter uma rotina de alternância de partição completa.

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

## <a name="minimize-logging-with-small-batches"></a>Minimizar o log com lotes pequenos
Para operações de modificação de dados grandes, pode fazer sentido dividir a operação em partes ou lotes para o escopo da unidade de trabalho.

Um código a seguir é um exemplo funcional. O tamanho do lote foi definido como um número trivial para realçar a técnica. Na realidade, o tamanho do lote seria significativamente maior. 

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

## <a name="pause-and-scaling-guidance"></a>Diretrizes de pausa e dimensionamento
O SQL Data Warehouse do Azure permite [pausar, retomar e dimensionar](sql-data-warehouse-manage-compute-overview.md) seu data warehouse sob demanda. Ao pausar ou dimensionar seu SQL Data Warehouse, é importante entender que todas as transações em andamento são encerradas imediatamente; fazendo com que todas as transações abertas sejam revertidas. Se sua carga de trabalho tiver emitido uma modificação de dados de execução longa e incompleta antes da operação de pausa ou escala, esse trabalho precisará ser desfeito. Isso pode afetar o tempo necessário para pausar ou dimensionar seu banco de dados SQL Data Warehouse do Azure. 

> [!IMPORTANT]
> Tanto `UPDATE` quanto `DELETE` são operações totalmente registradas em log e, portanto, essas operações de desfazer/refazer podem levar significativamente mais tempo do que as operações de log minimamente equivalentes. 
> 
> 

O melhor cenário é permitir que as transações de modificação de dados em voo sejam concluídas antes de pausar ou dimensionar SQL Data Warehouse. No entanto, esse cenário talvez nem sempre seja prático. Para reduzir o risco de uma reversão longa, considere uma das seguintes opções:

* Reescrever operações de longa execução usando [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Interromper a operação em partes; operando em um subconjunto das linhas

## <a name="next-steps"></a>Passos seguintes
Consulte [transações em SQL data warehouse](sql-data-warehouse-develop-transactions.md) para saber mais sobre os níveis de isolamento e limites transacionais.  Para obter uma visão geral de outras práticas recomendadas, consulte [SQL data warehouse práticas recomendadas](sql-data-warehouse-best-practices.md).

