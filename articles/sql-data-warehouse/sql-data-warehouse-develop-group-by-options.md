---
title: Usando opções agrupar por
description: Dicas para implementar as opções agrupar por no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8d5d0f28fa210a56460ced25323b96a68f245895
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685845"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Opções de agrupar por no SQL Data Warehouse
Dicas para implementar as opções agrupar por no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="what-does-group-by-do"></a>O que o GROUP BY faz?

A cláusula [Group by](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL agrega dados a um conjunto de linhas de resumo. GROUP BY tem algumas opções às quais SQL Data Warehouse não oferece suporte. Essas opções têm soluções alternativas.

Essas opções são

* Agrupar por com ROLLUP
* CONJUNTOS DE AGRUPAMENTOS
* Agrupar por com cubo

## <a name="rollup-and-grouping-sets-options"></a>Opções de ROLLUP e agrupamento de conjuntos
A opção mais simples aqui é usar UNION ALL em vez de executar o rollup em vez de depender da sintaxe explícita. O resultado é exatamente o mesmo

O exemplo a seguir usa a instrução GROUP BY com a opção ROLLUP:
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

Usando ROLLUP, o exemplo anterior solicita as seguintes agregações:

* País e região
* País
* Total Geral

Para substituir o ROLLUP e retornar os mesmos resultados, você pode usar UNION ALL e especificar explicitamente as agregações necessárias:

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

Para substituir os conjuntos de AGRUPAMENTOs, o princípio de exemplo se aplica. Você só precisa criar seções UNION ALL para os níveis de agregação que deseja ver.

## <a name="cube-options"></a>Opções de cubo
É possível criar um GROUP BY com o cubo usando a abordagem UNION ALL. O problema é que o código pode rapidamente se tornar complicado e difícil. Para atenuar isso, você pode usar essa abordagem mais avançada.

Vamos usar o exemplo acima.

A primeira etapa é definir o ' cubo ' que define todos os níveis de agregação que desejamos criar. É importante observar a junção CRUZada das duas tabelas derivadas. Isso gera todos os níveis para nós. O restante do código está realmente lá para formatação.

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

Veja a seguir os resultados do CTAS:

![Agrupar por cubo](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

A segunda etapa é especificar uma tabela de destino para armazenar os resultados provisórios:

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

A terceira etapa é executar um loop sobre nosso cubo de colunas executando a agregação. A consulta será executada uma vez para cada linha na #Cube tabela temporária e armazenará os resultados na tabela #Results Temp

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

Por fim, você pode retornar os resultados simplesmente lendo na tabela temporária #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Ao dividir o código em seções e gerar uma construção de loop, o código torna-se mais gerenciável e passível de manutenção.

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

