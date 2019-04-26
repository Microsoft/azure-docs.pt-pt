---
title: Utilizar o grupo por opções no Azure SQL Data Warehouse | Documentos da Microsoft
description: Sugestões para a implementação de grupo por opções no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 69a76ae9d6f355fe401b438ec2ab89d6606ba46c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402351"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Agrupar por opções no SQL Data Warehouse
Sugestões para a implementação de grupo por opções no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="what-does-group-by-do"></a>O que faz GROUP BY?

O [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) cláusula de T-SQL agrega os dados para um conjunto de resumo de linhas. GROUP BY tem algumas opções que não suporta o SQL Data Warehouse. Estas opções têm soluções alternativas.

Estas opções são

* GROUP BY com ROLLUP
* CONJUNTOS DE AGRUPAMENTO
* GROUP BY com cubo

## <a name="rollup-and-grouping-sets-options"></a>Opções de conjuntos de agregação e agrupamento
A opção mais simples aqui é usar UNION ALL em vez disso, para executar o rollup em vez de depender de uma sintaxe explícita. O resultado é exatamente o mesmo

O exemplo a seguir utilizando a instrução GROUP BY com a opção de agregação:
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

Ao utilizar a agregação, o exemplo anterior solicita as agregações seguintes:

* País e região
* País
* Total geral

Para substituir o ROLLUP e retornar os mesmos resultados, pode utilizar UNION ALL e especificar explicitamente as agregações necessárias:

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

Para substituir GROUPING SETS, aplica-se do princípio de exemplo. Só precisa de criar secções UNION ALL para obter os níveis de agregação que pretende ver.

## <a name="cube-options"></a>Opções de cubo
É possível criar um grupo por com cubo usando a abordagem UNION ALL. O problema é que o código pode rapidamente tornar-se complicado e difícil. Para atenuar isso, pode usar esta mais avançada abordagem.

Vamos utilizar o exemplo acima.

A primeira etapa é definir o 'cube' que define todos os níveis de agregação que Desejamos criar. É importante observar de CROSS JOIN das duas tabelas derivadas. Todos os níveis de é gerado para nós. O restante do código existe realmente para formatação.

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

O código a seguir mostra os resultados do CTAS:

![Agrupar por cubo](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

O segundo passo é especificar uma tabela de destino para armazenar os resultados intermediárias:

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

O terceiro passo é executar um loop através de nossa cubo de executar a agregação de colunas. A consulta será executada uma vez para cada linha na tabela temporária #Cube e armazenar os resultados na tabela temporária #Results

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

Por último, pode retornar os resultados, simplesmente lendo da tabela temporária #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Ao dividir o código em seções e gerar uma construção de loop, o código se torna mais gerenciável e passível de manutenção.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento](sql-data-warehouse-overview-develop.md).

