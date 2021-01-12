---
title: CRIAR TABELA COMO SELECT (CTAS)
description: Explicação e exemplos da declaração CREATE TABLE AS SELECT (CTAS) em Synapse SQL para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 95158193a978702392106c8aa3347de211fd1a3e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115451"
---
# <a name="create-table-as-select-ctas"></a>CRIAR TABELA COMO SELECT (CTAS)

Este artigo explica a declaração de T-SQL do QUADRO CREATE AS SELECT (CTAS) em Synapse SQL para o desenvolvimento de soluções. O artigo também fornece exemplos de código.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

A declaração [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) é uma das funcionalidades T-SQL mais importantes disponíveis. CTAS é uma operação paralela que cria uma nova tabela com base na saída de uma declaração SELECT. CTAS é a forma mais simples e rápida de criar e inserir dados numa tabela com um único comando.

## <a name="selectinto-vs-ctas"></a>SELECIONE... INTO vs. CTAS

CTAS é uma versão mais personalizável do [SELECT... Em](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) declaração.

Segue-se um exemplo de um simples SELECT... EM:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECIONE... A INTO não permite alterar nem o método de distribuição nem o tipo de índice como parte da operação. Cria `[dbo].[FactInternetSales_new]` utilizando o tipo de distribuição predefinido de ROUND_ROBIN e a estrutura de tabela padrão do INDEX DA COLUNASAMERADA.

Com ctas, por outro lado, pode especificar tanto a distribuição dos dados da tabela como o tipo de estrutura de tabela. Para converter o exemplo anterior em CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Se estiver apenas a tentar alterar o índice na sua operação CTAS, e a tabela de origem estiver distribuída, mantenha a mesma coluna de distribuição e tipo de dados. Isto evita o movimento de dados de distribuição cruzada durante a operação, o que é mais eficiente.

## <a name="use-ctas-to-copy-a-table"></a>Use CTAS para copiar uma tabela

Talvez uma das utilizações mais comuns do CTAS seja a criação de uma cópia de uma tabela para alterar o DDL. Digamos que criaste originalmente a tua mesa `ROUND_ROBIN` como, e agora queres mudá-la para uma mesa distribuída numa coluna. CTAS é como mudaria a coluna de distribuição. Também pode utilizar CTAS para alterar tipos de divisórias, indexação ou colunas.

Digamos que criou esta tabela utilizando o tipo de distribuição padrão de , não especificando uma coluna de `ROUND_ROBIN` distribuição no `CREATE TABLE` .

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
    CustomerPONumber nvarchar(25));
```

Agora quer criar uma nova cópia desta tabela, com `Clustered Columnstore Index` uma, para que possa aproveitar o desempenho das tabelas de Colunas Agrupadas. Também pretende distribuir esta `ProductKey` tabela, porque está a antecipar a junção nesta coluna e quer evitar o movimento de dados durante as junções `ProductKey` . Por último, também pretende adicionar divisórias , para `OrderDateKey` que possa eliminar rapidamente dados antigos deixando cair divisórias antigas. Aqui está a declaração da CTAS, que copia a sua velha mesa numa nova mesa.

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

Finalmente, pode mudar o nome das suas mesas, trocar na sua nova mesa e depois largar a sua velha mesa.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Use CTAS para trabalhar em torno de funcionalidades não apoiadas

Também pode utilizar ctas para trabalhar em torno de algumas das funcionalidades não apoiadas listadas abaixo. Este método pode muitas vezes revelar-se útil, porque não só o seu código será compatível, como muitas vezes será executado mais rapidamente em Synapse SQL. Este desempenho é o resultado do seu design totalmente paralelo. Os cenários incluem:

* ANSI junta-se a UPDATEs
* ANSI JOINs em DELETEs
* Declaração da MERGE

> [!TIP]
> Tente pensar "CTAS primeiro". Resolver um problema usando CTAS é geralmente uma boa abordagem, mesmo que esteja a escrever mais dados como resultado.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI associa-se a substituição para declarações de atualização

Pode descobrir que tem uma atualização complexa. A atualização junta mais de duas tabelas utilizando a sintaxe de junção ANSI para executar a ATUALIZAÇÃO ou DELETE.

Imagine que teve que atualizar esta tabela:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

A consulta original pode ter parecido com este exemplo:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

O Synapse SQL não suporta a ANSI junta-se à `FROM` cláusula de uma `UPDATE` declaração, pelo que não pode usar o exemplo anterior sem modificá-lo.

Pode utilizar uma combinação de CTAS e uma junção implícita para substituir o exemplo anterior:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-merge"></a>ANSI junta-se a substituição da MERGE 

No Azure Synapse Analytics, [a MERGE](/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver15) (pré-visualização) com NÃO CORRESPONDED BY TARGET requer que o alvo seja uma tabela distribuída HASH.  Os utilizadores podem utilizar o ANSI JOIN com [UPDATE](/sql/t-sql/queries/update-transact-sql?view=sql-server-ver15) ou [DELETE](/sql/t-sql/statements/delete-transact-sql?view=sql-server-ver15) como uma solução alternativa para modificar os dados da tabela-alvo com base no resultado de se juntar a outra tabela.  Eis um exemplo.

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explicitamente, indicar o tipo de dados e a anulação da produção

Ao migrar o código, poderá encontrar-se a correr através deste tipo de padrão de codificação:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Podes pensar que devias migrar este código para ctas, e estarias correto. No entanto, há um problema escondido aqui.

O seguinte código não produz o mesmo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Note que a coluna "resultado" transporta os valores de tipo de dados e de nulidade da expressão. Transportar o tipo de dados para a frente pode levar a variações subtis de valores se não tiver cuidado.

Experimente este exemplo:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

O valor armazenado para o resultado é diferente. À medida que o valor persistido na coluna de resultados é usado noutras expressões, o erro torna-se ainda mais significativo.

![Screenshot dos resultados do CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Isto é importante para a migração de dados. Mesmo que a segunda consulta seja indiscutivelmente mais precisa, há um problema. Os dados seriam diferentes em comparação com o sistema de origem, o que leva a questões de integridade na migração. Este é um daqueles casos raros em que a resposta "errada" é, na verdade, a correta!

A razão pela qual vemos uma disparidade entre os dois resultados deve-se ao tipo de fundição implícita. No primeiro exemplo, a tabela define a definição da coluna. Quando a linha é inserida, ocorre uma conversão implícita do tipo. No segundo exemplo, não existe conversão implícita do tipo, uma vez que a expressão define o tipo de dados da coluna.

Note também que a coluna no segundo exemplo foi definida como uma coluna NULable, enquanto no primeiro exemplo não o fez. Quando a tabela foi criada no primeiro exemplo, a nulidade da coluna foi explicitamente definida. No segundo exemplo, foi deixado à expressão, e por defeito resultaria numa definição NU.

Para resolver estas questões, deve definir explicitamente a conversão e a nulidade do tipo na parte SELECT da declaração CTAS. Não é possível definir estas propriedades em 'CREATE TABLE'.
O exemplo a seguir demonstra como corrigir o código:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenha em atenção o seguinte:

* Pode utilizar CAST ou CONVERT.
* Utilize a ISNULL, não a COALESCE, para forçar a NULLability. Consulte a seguinte nota.
* ISNULL é a função mais externa.
* A segunda parte da ISNULL é uma constante, 0.

> [!NOTE]
> Para que a nulidade seja corretamente definida, é vital usar a ISNULL e não a COALESCE. Coalesce não é uma função determinística, pelo que o resultado da expressão será sempre NULable. ISNULL é diferente. É determinístico. Portanto, quando a segunda parte da função ISNULL é uma constante ou literal, o valor resultante não será NULO.

Garantir a integridade dos seus cálculos também é importante para a comutação de divisórias de mesa. Imagine que tem esta tabela definida como uma tabela de factos:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

No entanto, o campo de quantidade é uma expressão calculada. Não faz parte dos dados de origem.

Para criar o conjunto de dados dividido, pode querer utilizar o seguinte código:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

A consulta correria perfeitamente bem. O problema surge quando se tenta fazer o interruptor de partição. As definições da mesa não coincidem. Para que as definições da tabela correspondam, modifique os CTAS para adicionar uma `ISNULL` função para preservar o atributo de nulidade da coluna.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Você pode ver que tipo de consistência e manter propriedades de anulação em um CTAS é uma melhor prática de engenharia. Ajuda a manter a integridade nos seus cálculos, e também garante que a comutação de divisórias é possível.

O CTAS é uma das declarações mais importantes no Sinaapse SQL. Certifique-se de compreendê-lo completamente. Consulte a [documentação ctas.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a [visão geral](sql-data-warehouse-overview-develop.md)do desenvolvimento.