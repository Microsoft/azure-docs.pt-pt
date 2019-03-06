---
title: Tabelas temporárias no SQL Data Warehouse | Documentos da Microsoft
description: Documentação de orientação essencial para utilizar tabelas temporárias e destaca os princípios de tabelas temporárias ao nível de sessão.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: c989e53113557219e13dd730ac43621d3824baac
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57434764"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelas temporárias no SQL Data Warehouse
Este artigo contém orientações essenciais para a utilização de tabelas temporárias e realça os princípios de tabelas temporárias ao nível de sessão. Usando as informações deste artigo pode ajudá-lo a modulariza o código, melhorando a capacidade de reutilização e facilidade de manutenção do seu código.

## <a name="what-are-temporary-tables"></a>O que são tabelas temporárias?
Tabelas temporárias são úteis durante o processamento de dados - especialmente durante a transformação em que os resultados intermediários são transitórios. No SQL Data Warehouse, as tabelas temporárias existem no nível da sessão.  Eles só estão visíveis para a sessão em que foram criados e são automaticamente removidos quando termina a sessão.  Tabelas temporárias oferecem um benefício de desempenho, pois seus resultados são escritos para o local em vez de armazenamento remoto.  Tabelas temporárias são ligeiramente diferentes no armazém de dados SQL do Azure SQL Database do Azure como podem ser acedidos a partir de qualquer lugar dentro da sessão, incluindo dentro e fora de um procedimento armazenado.

## <a name="create-a-temporary-table"></a>Criar uma tabela temporária
Tabelas temporárias são criadas atribuindo o nome de tabela com um `#`.  Por exemplo:

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

Tabelas temporárias também podem ser criadas com um `CTAS` usando exatamente a mesma abordagem:

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
``` 

> [!NOTE]
> `CTAS` é um comando poderosas e tem a vantagem adicional de que está a ser eficiente na sua utilização do espaço de registo de transação. 
> 
> 

## <a name="dropping-temporary-tables"></a>Remover tabelas temporárias
Quando é criada uma nova sessão, não tabelas temporárias devem existir.  No entanto, se chamasse o mesmo procedimento armazenado, que cria uma temporária com o mesmo nome, para garantir que seus `CREATE TABLE` instruções são com êxito uma verificação de pré-existência simples com um `DROP` pode ser utilizado como no exemplo seguinte:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para codificar a consistência, é uma boa prática utilizar este padrão para tabelas e tabelas temporárias.  Também é uma boa idéia usar `DROP TABLE` para remover as tabelas temporárias quando tiver terminado com eles em seu código.  No desenvolvimento de procedimento armazenado, é comum ver que os comandos de menu agrupadas no final de um procedimento para garantir que esses objetos são limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Código modularizando
Uma vez que as tabelas temporárias podem ser vistas em qualquer lugar numa sessão de utilizador, eles podem ser explorados para o ajudar a modularizar o código da aplicação.  Por exemplo, o seguinte procedimento armazenado gera DDL para atualizar todas as estatísticas na base de dados pelo nome da estatística.

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

Nesta fase, a única ação que tenha ocorrido é a criação de um procedimento armazenado que gera uma tabela temporária, stats_ddl #, com instruções DDL.  Este procedimento armazenado ignora #stats_ddl se já existe para garantir que ele não falha se mais de uma vez executado dentro de uma sessão.  No entanto, uma vez que não existe nenhum `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado é concluído, ele deixa a tabela criada para que possa ser lido fora o procedimento armazenado.  No SQL Data Warehouse, ao contrário de outras bases de dados do SQL Server, é possível utilizar a tabela temporária fora o procedimento que o criou.  Tabelas temporárias do SQL Data Warehouse podem ser utilizadas **em qualquer lugar** dentro da sessão. Isso pode levar a mais modular e gerenciável de código como no exemplo seguinte:

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

## <a name="temporary-table-limitations"></a>Limitações de tabela temporária
O SQL Data Warehouse impõe algumas limitações na implementação de tabelas temporárias.  Atualmente, apenas a sessão de tabelas temporárias de âmbito são suportadas.  Tabelas temporárias globais não são suportadas.  Além disso, não não possível criar vistas de tabelas temporárias.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o desenvolvimento de tabelas, veja a [descrição geral da tabela](sql-data-warehouse-tables-overview.md).

