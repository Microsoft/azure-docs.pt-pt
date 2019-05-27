---
title: Criar TABLE AS SELECT (CTAS) no armazém de dados SQL do Azure | Documentos da Microsoft
description: Explicação e exemplos da instrução CREATE TABLE AS SELECT (CTAS) no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 91de474cc0610099b4264cc6d0dfbd26e8df0618
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851450"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Criar TABLE AS SELECT (CTAS) no armazém de dados SQL do Azure

Este artigo explica a instrução CREATE TABLE AS SELECT (CTAS). o T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções. O artigo também fornece exemplos de código.

## <a name="create-table-as-select"></a>CRIAR TABLE AS SELECT

O [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrução (CTAS) é um dos recursos mais importantes de T-SQL disponíveis. CTAS é uma operação simultânea que cria uma nova tabela com base no resultado de uma instrução SELECT. CTAS é a forma mais simples e mais rápida de criar e inserir dados para uma tabela com um único comando.

## <a name="selectinto-vs-ctas"></a>SELECT...INTO vs. CTAS

CTAS é uma versão mais personalizável do [SELECIONE... EM](/sql/t-sql/queries/select-into-clause-transact-sql) instrução.

Segue-se um exemplo de um simple SELECT... PARA:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECIONE... PARA não lhe permite alterar o método de distribuição ou o tipo de índice como parte da operação. Criar `[dbo].[FactInternetSales_new]` com o tipo de distribuição de padrão de round robin e a estrutura da tabela padrão do índice COLUMNSTORE em cluster.

Por outro lado, com CTAS, pode especificar ambas a distribuição dos dados da tabela, bem como o tipo de estrutura da tabela. Para converter o exemplo anterior para CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Se estiver apenas a tentar alterar o índice em sua operação CTAS e a tabela de origem é distribuídas com hash, manter o mesmo tipo de coluna e os dados de distribuição. Isso evita o movimento de dados de distribuição entre durante a operação, o que é mais eficiente.

## <a name="use-ctas-to-copy-a-table"></a>Utilizar CTAS para copiar uma tabela

Talvez um dos usos mais comuns de CTAS é a criação de uma cópia de uma tabela para alterar a DDL. Vamos dizer que originalmente criou sua tabela como `ROUND_ROBIN`e agora pretende alterá-lo para uma tabela distribuída numa coluna. CTAS é como alteraria a coluna de distribuição. Também pode utilizar CTAS para alterar os tipos de criação de partições, indexar ou coluna.

Vamos dizer que criou nesta tabela com o tipo de distribuição predefinido da `ROUND_ROBIN`, não especificar uma coluna de distribuição no `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Agora pretende criar uma nova cópia desta tabela, com um `Clustered Columnstore Index`, por isso, pode tirar partido do desempenho das tabelas Columnstore em cluster. Também queira distribuir esta tabela em `ProductKey`, porque está prevendo associações nesta coluna e quiser evitar o movimento de dados durante as associações no `ProductKey`. Por último, também queira adicionar a criação de partições no `OrderDateKey`, por isso, pode rapidamente para eliminar dados antigos remover partições antigas. Eis a instrução CTAS, que copia a sua tabela antiga para uma nova tabela.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Por fim, pode renomear as suas tabelas, para trocar a sua nova tabela e, em seguida, remover a tabela antiga.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Utilizar CTAS para trabalhar em torno de funcionalidades não suportadas

Também pode utilizar CTAS para contornar um número de funcionalidades não suportadas, listados abaixo. Este método, muitas vezes, pode ser útil, porque não apenas seu código estarão em conformidade, mas será sempre executado mais rapidamente no SQL Data Warehouse. Este desempenho é um resultado de seu design totalmente em paralelo. Os cenários incluem:

* ASSOCIAÇÕES de ANSI em atualizações
* Associações de ANSI em eliminações
* Instrução de intercalação

> [!TIP]
> Tente imaginar "CTAS primeiro." Resolver um problema ao utilizar CTAS geralmente é uma boa abordagem, mesmo se estiver escrevendo mais dados como resultado.

## <a name="ansi-join-replacement-for-update-statements"></a>Substituição de associação ANSI para instruções de atualização

Pode achar que tem uma atualização complexa. A atualização junta-se mais de duas tabelas em conjunto, utilizando a sintaxe de junção ANSI para executar o UPDATE ou DELETE.

Imagine que tinha que atualizar esta tabela:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

A consulta original poderia se parecer com este exemplo:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Não suporta associações ANSI em que o SQL Data Warehouse a `FROM` cláusula de um `UPDATE` instrução, pelo que não é possível utilizar o exemplo anterior sem modificá-la.

Pode usar uma combinação de um CTAS e um join implícito para substituir o exemplo anterior:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Substituição de associação ANSI para eliminar instruções

Às vezes, a melhor abordagem para a eliminação de dados é utilizar CTAS, especialmente para `DELETE` instruções que utilizar ANSI Junte-se a sintaxe. Isto acontece porque o SQL Data Warehouse não suporta associações ANSI no `FROM` cláusula de um `DELETE` instrução. Em vez de eliminar os dados, selecione os dados que quer manter.

Segue-se um exemplo de um convertido `DELETE` instrução:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Substitua as instruções de intercalação

Pode substituir as instruções de intercalação, pelo menos em parte, ao utilizar CTAS. Pode combinar a `INSERT` e o `UPDATE` numa única instrução. Quaisquer registos eliminados devem ser impedidos do `SELECT` instrução omitir dos resultados.

O exemplo seguinte é para um `UPSERT`:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Declare explicitamente o tipo de dados e de nulidade da saída

Após a migração de código, pode achar que encontrar este tipo de padrão de codificação:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Pode pensar que migrar este código para CTAS, e estaria correta. No entanto, existe um problema de oculto aqui.

O código a seguir não produzir o mesmo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Tenha em atenção que a coluna "resultado" são seguidos os valores de tipo e permissão de valores NULL de dados da expressão. Tipo para a frente com os dados pode levar a sutis variações nos valores se não tiver cuidado.

Experimente este exemplo:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

O valor armazenado para o resultado é diferente. Como o valor persistente na coluna de resultado é utilizado em outras expressões, o erro se torna ainda mais significativo.

![Resultados de captura de ecrã de CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Isso é importante para migrações de dados. Mesmo que a segunda consulta seja comprovadamente mais precisa, existe um problema. Os dados seriam diferentes em comparação com o sistema de origem, e isso leva a questões de integridade na migração. Este é um dos raros casos em que a resposta "errada" é, na verdade, a certa!

O motivo pelo qual que podemos ver uma disparidade entre os dois resultados é devido a conversão de tipo implícito. No primeiro exemplo, a tabela define a definição da coluna. Quando a linha é inserida, ocorre uma conversão de tipo implícito. No segundo exemplo, não existe nenhuma conversão implícita de tipo como a expressão define o tipo de dados da coluna.

Observe também que a coluna no segundo exemplo foi definida como uma coluna que pode ser nula, enquanto que no primeiro exemplo não tem. Quando a tabela foi criada no primeiro exemplo, nulidade da coluna foi explicitamente definida. No segundo exemplo, ele foi deixado para a expressão e, por padrão, iria resultar numa definição de NULL.

Para resolver estes problemas, deve definir explicitamente a conversão de tipo e a permissão de valores NULL na parte SELECT da instrução CTAS. Não é possível definir estas propriedades em 'CREATE TABLE'.
O exemplo seguinte demonstra como corrigir o código:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenha em atenção o seguinte:

* Pode usar CAST ou converter.
* Utilize ISNULL, não COALESCE, para forçar a permissão de valores NULL. Consulte a nota seguinte.
* ISNULL é a função mais externa.
* A segunda parte do ISNULL é uma constante, 0.

> [!NOTE]
> Para a permissão de valores NULL ser definidas corretamente, é vital para utilizar ISNULL e não COALESCE. COALESCE não é uma função determinista e, por isso, o resultado da expressão sempre serão NULLable. ISNULL é diferente. É determinístico. Portanto, quando a segunda parte da função ISNULL é uma constante ou um literal, o valor resultante será NOT NULL.

Garantindo a integridade dos seus cálculos também é importante para a alternância de partição de tabela. Imagine-se de que tenha nesta tabela são definidos como uma tabela de fatos:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

No entanto, o campo de valor é uma expressão calculada. Não é parte dos dados de origem.

Para criar o conjunto de dados particionado, talvez queira usar o seguinte código:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

A consulta seria executado perfeitamente bem. O problema surge quando tentar fazer a alternância de partição. As definições de tabela não correspondem. Para tornar as definições de tabela corresponder, modifique o CTAS para adicionar um `ISNULL` função para preservar o atributo de nulidade da coluna.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Pode ver que tipo de consistência e a manutenção de propriedades de nulidade num CTAS é uma prática recomendada de engenharia. Ele ajuda a manter a integridade nos seus cálculos e também garante que a partição de troca é possível.

CTAS é uma das declarações a mais importantes no SQL Data Warehouse. Certifique-se de que compreender totalmente. Consulte a [documentação CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais sugestões de desenvolvimento, consulte a [descrição geral do desenvolvimento](sql-data-warehouse-overview-develop.md).

