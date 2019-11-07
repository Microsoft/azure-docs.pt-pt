---
title: Tabelas temporárias
description: Diretrizes essenciais para o uso de tabelas temporárias no Azure SQL Data Warehouse, destacando os princípios das tabelas temporárias em nível de sessão.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 23a5825a32c602f70aff1d9f577ce13d3e9f2260
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685443"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelas temporárias no SQL Data Warehouse
Este artigo contém diretrizes essenciais para o uso de tabelas temporárias e realça os princípios de tabelas temporárias em nível de sessão. O uso das informações neste artigo pode ajudá-lo a modularizar seu código, melhorando a reusabilidade e a facilidade de manutenção do seu código.

## <a name="what-are-temporary-tables"></a>O que são tabelas temporárias?
As tabelas temporárias são úteis durante o processamento de dados – especialmente durante a transformação em que os resultados intermediários são transitórios. Em SQL Data Warehouse, existem tabelas temporárias no nível da sessão.  Eles só ficam visíveis para a sessão na qual foram criados e são descartados automaticamente quando a sessão faz logoff.  As tabelas temporárias oferecem um benefício de desempenho porque seus resultados são gravados em local, em vez de armazenamento remoto.

## <a name="create-a-temporary-table"></a>Criar uma tabela temporária
As tabelas temporárias são criadas por meio da prefixação do nome da tabela com um `#`.  Por exemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

As tabelas temporárias também podem ser criadas com um `CTAS` usando exatamente a mesma abordagem:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
``` 

> [!NOTE]
> `CTAS` é um comando poderoso e tem a vantagem adicional de ser eficiente em seu uso do espaço de log de transações. 
> 
> 

## <a name="dropping-temporary-tables"></a>Descartando tabelas temporárias
Quando uma nova sessão é criada, nenhuma tabela temporária deve existir.  No entanto, se você estiver chamando o mesmo procedimento armazenado, que cria um temporário com o mesmo nome, para garantir que suas `CREATE TABLE` instruções sejam bem-sucedidas, uma simples verificação de existência com um `DROP` pode ser usada como no exemplo a seguir:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para a consistência de codificação, é uma boa prática usar esse padrão para tabelas e tabelas temporárias.  Também é uma boa ideia usar `DROP TABLE` para remover tabelas temporárias quando você terminar com elas em seu código.  No desenvolvimento de procedimentos armazenados, é comum ver os comandos de remoção agrupados no final de um procedimento para garantir que esses objetos sejam limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizando o código
Como as tabelas temporárias podem ser vistas em qualquer lugar em uma sessão de usuário, isso pode ser explorado para ajudá-lo a modularizar o código do aplicativo.  Por exemplo, o procedimento armazenado a seguir gera DDL para atualizar todas as estatísticas no banco de dados pelo nome da estatística.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Nesse estágio, a única ação que ocorreu é a criação de um procedimento armazenado que gera uma tabela temporária, #stats_ddl, com instruções DDL.  Esse procedimento armazenado descartará #stats_ddl se ele já existir para garantir que ele não falhe se for executado mais de uma vez em uma sessão.  No entanto, como não há `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, ele deixará a tabela criada para que possa ser lida fora do procedimento armazenado.  Em SQL Data Warehouse, ao contrário de outros bancos de dados do SQL Server, é possível usar a tabela temporária fora do procedimento que o criou.  SQL Data Warehouse tabelas temporárias podem ser usadas **em qualquer lugar** dentro da sessão. Isso pode levar a um código mais modular e gerenciável, como no exemplo a seguir:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitações da tabela temporária
SQL Data Warehouse impõe algumas limitações ao implementar tabelas temporárias.  Atualmente, há suporte apenas para tabelas temporárias com escopo de sessão.  Não há suporte para tabelas temporárias globais.  Além disso, as exibições não podem ser criadas em tabelas temporárias.  As tabelas temporárias só podem ser criadas com a distribuição hash ou round robin.  Não há suporte para a distribuição de tabela temporária replicada. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o desenvolvimento de tabelas, consulte a [visão geral da tabela](sql-data-warehouse-tables-overview.md).

