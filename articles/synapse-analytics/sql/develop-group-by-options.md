---
title: Utilizar opções GRUPO A OPS em Sinapse SQL
description: O Sinaapse SQL permite desenvolver soluções implementando diferentes opções group by.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 0d9d28bacac02326ee781ca56309b7a72e921960
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289174"
---
# <a name="group-by-options-in-synapse-sql"></a>OPÇÕES GRUPO A EM Sinapse SQL
O Sinaapse SQL permite desenvolver soluções implementando diferentes opções group by. 

## <a name="what-does-group-by-do"></a>O que é que o GROUP BY faz

A cláusula [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL agrega dados a um conjunto sumário de linhas.

A SQL on-demand suporta toda a gama de opções GROUP BY. A piscina SQL suporta um número limitado de opções GROUP BY.

## <a name="group-by-options-supported-in-sql-pool"></a>OPÇÕES GRUPO POR APOIO Na Piscina SQL

O GROUP BY tem algumas opções que a piscina SQL não suporta. Estas opções têm soluções alternativas, que são as seguintes:

* GRUPO BY com ROLLUP
* CONJUNTOS DE AGRUPAMENTO
* GRUPO BY com CUBO

### <a name="rollup-and-grouping-sets-options"></a>Opções de conjuntos rollup e de agrupamento

A opção mais simples aqui é usar a UNION ALL para executar o rollup em vez de confiar na sintaxe explícita. O resultado é exatamente o mesmo

O exemplo a seguir utiliza a declaração GROUP BY com a opção ROLLUP:

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

Utilizando o ROLLUP, o exemplo anterior solicita as seguintes agregações:

* País e Região
* País
* Grand Total

Para substituir o ROLLUP e devolver os mesmos resultados, pode utilizar a UNION ALL e especificar explicitamente as agregações necessárias:

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

Para substituir os CONJUNTOS DE AGRUPAMENTO, aplica-se o princípio da amostra. Só precisa de criar secções UNION ALL para os níveis de agregação que quer ver.

### <a name="cube-options"></a>Opções de cubo

É possível criar um grupo by with cube usando a abordagem UNION ALL. O problema é que o código pode rapidamente tornar-se pesado e inflexível. Para atenuar esta questão, pode utilizar esta abordagem mais avançada.

O primeiro passo é definir o 'cubo' que define todos os níveis de agregação que queremos criar. Tome nota da CROSS JOIN das duas tabelas derivadas, uma vez que gera todos os níveis. O resto do código está lá para formatação.

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

A imagem a seguir mostra os resultados do [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true):

![Grupo por cubo](./media/develop-group-by-options/develop-group-by-cube.png)

O segundo passo é especificar uma tabela-alvo para o armazenamento de resultados provisórios:

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

O terceiro passo é dar a volta ao cubo de colunas que executam a agregação. A consulta será executada uma vez para cada linha da #Cube tabela temporária. Os resultados são armazenados na tabela temporária #Results:

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

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)
