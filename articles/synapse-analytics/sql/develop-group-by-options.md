---
title: Utilizar opções DE GRUPO BY em Synapse SQL
description: A Synapse SQL permite desenvolver soluções implementando diferentes opções de GROUP BY.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 261f75344d250ae8a8d9687f4bcd80535d11716b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429047"
---
# <a name="group-by-options-in-synapse-sql"></a>Opções group BY em Synapse SQL
A Synapse SQL permite desenvolver soluções implementando diferentes opções de GROUP BY. 

## <a name="what-does-group-by-do"></a>O que o GROUP BY faz

A cláusula [Group BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL agrega dados a um conjunto de linhas sumárias.

A SQL on-demand suporta toda a gama de opções GROUP BY. O pool SQL suporta um número limitado de opções GROUP BY.

## <a name="group-by-options-supported-in-sql-pool"></a>Opções group BY suportadas em piscina SQL

Group BY tem algumas opções que o SQL pool não suporta. Estas opções têm suposições, que são as seguintes:

* GROUP BY com ROLLUP
* CONJUNTOS DE AGRUPAMENTOS
* GRUPO BY COM CUBE

### <a name="rollup-and-grouping-sets-options"></a>Rollup e agrupamento define opções

A opção mais simples aqui é usar a UNION ALL para executar o rollup em vez de confiar na sintaxe explícita. O resultado é exatamente o mesmo

O exemplo seguinte utiliza a declaração GROUP BY com a opção ROLLUP:

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

### <a name="cube-options"></a>Opções de cubos

É possível criar um GRUPO BY COM CUBE usando a abordagem UNION ALL. O problema é que o código pode rapidamente tornar-se pesado e desajeitado. Para mitigar esta questão, pode usar esta abordagem mais avançada.

O primeiro passo é definir o 'cubo' que define todos os níveis de agregação que queremos criar. Tome nota do CROSS JOIN das duas tabelas derivadas, pois gera todos os níveis. O resto do código está lá para a formatação.

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

A imagem seguinte mostra os resultados da [TABELA CREATE AS SELECT:](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

![Grupo por cubo](./media/develop-group-by-options/develop-group-by-cube.png)

O segundo passo consiste em especificar um quadro-alvo para armazenar resultados provisórios:

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

O terceiro passo é dar a volta ao cubo das colunas que realizam a agregação. A consulta será executada uma vez por cada linha na mesa temporária #Cube. Os resultados são armazenados na tabela temporária #Results:

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

Por último, pode devolver os resultados lendo a partir da tabela temporária #Results:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Ao dividir o código em secções e gerar uma construção em loop, o código torna-se mais manejável e manejável.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](develop-overview.md)
