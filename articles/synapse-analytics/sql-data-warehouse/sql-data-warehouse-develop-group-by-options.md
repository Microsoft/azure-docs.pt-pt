---
title: Usando grupo por opções
description: Dicas para implementar grupo por opções para piscinas SQL dedicadas em Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: cdac3b1e502a203c272db75eb4345446b0f7c0c8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462758"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Grupo por opções para piscinas SQL dedicadas em Azure Synapse Analytics

Neste artigo, você encontrará dicas para implementar grupo por opções em piscinas SQL dedicadas.

## <a name="what-does-group-by-do"></a>O que o GRUPO BY faz?

A cláusula [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL agrega dados a um conjunto sumário de linhas. O GROUP BY tem algumas opções que a piscina SQL dedicada não suporta. Estas opções têm soluções alternativas, que são as seguintes:

* GRUPO BY com ROLLUP
* CONJUNTOS DE AGRUPAMENTO
* GRUPO BY com CUBO

## <a name="rollup-and-grouping-sets-options"></a>Opções de conjuntos rollup e de agrupamento

A opção mais simples aqui é usar a UNION ALL para realizar o rollup em vez de confiar na sintaxe explícita. O resultado é exatamente o mesmo.

O exemplo a seguir utilizando a declaração GROUP BY com a opção ROLLUP:

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

## <a name="cube-options"></a>Opções de cubo

É possível criar um grupo by with cube usando a abordagem UNION ALL. O problema é que o código pode rapidamente tornar-se pesado e inflexível. Para atenuar esta questão, pode utilizar esta abordagem mais avançada.

Utilizando o exemplo anterior, o primeiro passo é definir o 'cubo' que define todos os níveis de agregação que queremos criar.

Tome nota da CROSS JOIN das duas tabelas derivadas, uma vez que isso gera todos os níveis para nós. O resto do código está lá para formatação:

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

A imagem a seguir mostra os resultados do CTAS:

![Grupo por cubo](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

O terceiro passo é dar a volta ao nosso cubo de colunas executando a agregação. A consulta será executada uma vez para cada linha da #Cube tabela temporária. Os resultados são armazenados na tabela temporária #Results:

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

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
