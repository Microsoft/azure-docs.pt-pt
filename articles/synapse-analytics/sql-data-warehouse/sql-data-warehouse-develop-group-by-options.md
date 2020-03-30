---
title: Usando o grupo por opções
description: Dicas para implementar grupo por opções no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f77445e80e701053b7fbfa1aa559248cf505353c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350517"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Grupo por opções no Armazém de Dados SQL
Dicas para implementar grupo por opções no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="what-does-group-by-do"></a>O que faz o GROUP BY?

A cláusula [Group BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL agrega dados a um conjunto de linhas sumárias. Group BY tem algumas opções que o SQL Data Warehouse não suporta. Estas opções têm suposições.

Estas opções são

* GROUP BY com ROLLUP
* CONJUNTOS DE AGRUPAMENTOS
* GRUPO BY COM CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup e agrupamento define opções
A opção mais simples aqui é utilizar o UNION ALL em vez de realizar o rollup em vez de confiar na sintaxe explícita. O resultado é exatamente o mesmo

O seguinte exemplo utilizando a declaração do GROUP BY com a opção ROLLUP:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Ao utilizar o ROLLUP, o exemplo anterior solicita as seguintes agregações:

* País e Região
* País
* Total Geral

Para substituir o ROLLUP e devolver os mesmos resultados, pode utilizar o UNION ALL e especificar explicitamente as agregações necessárias:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para substituir os CONJUNTOS DE AGRUPAMENTO, aplica-se o princípio da amostra. Basta criar secções UNION ALL para os níveis de agregação que pretende ver.

## <a name="cube-options"></a>Opções de cubos
É possível criar um GRUPO POR CUBE utilizando a abordagem UNION ALL. O problema é que o código pode rapidamente tornar-se pesado e desajeitado. Para mitigar isto, pode usar esta abordagem mais avançada.

Vamos usar o exemplo acima.

O primeiro passo é definir o 'cubo' que define todos os níveis de agregação que queremos criar. É importante tomar nota da CRUZ JUNTA das duas tabelas derivadas. Isto gera todos os níveis para nós. O resto do código está lá para a formatação.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

O seguinte mostra os resultados dos CTAS:

![Grupo por cubo](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

O segundo passo é especificar uma tabela-alvo para armazenar resultados provisórios:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

O terceiro passo é dar a volta ao nosso cubo de colunas que realizam a agregação. A consulta será executada uma vez por cada linha da mesa temporária #Cube e armazenará os resultados na tabela temporária #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Por último, pode devolver os resultados simplesmente lendo a partir da #Results tabela temporária

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Ao quebrar o código em secções e gerar uma construção em loop, o código torna-se mais manejável e manejável.

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)

