---
title: Tabelas temporárias
description: Orientação essencial para a utilização de tabelas temporárias na piscina Synapse SQL, destacando os princípios das tabelas temporárias de nível de sessão.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408033"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Mesas temporárias na piscina Synapse SQL
Este artigo contém orientações essenciais para a utilização de tabelas temporárias e destaca os princípios das tabelas temporárias de nível de sessão. 

A utilização da informação neste artigo pode ajudá-lo a modular o seu código, melhorando tanto a reutilização como a facilidade de manutenção.

## <a name="what-are-temporary-tables"></a>O que são mesas temporárias?
As tabelas temporárias são úteis no processamento de dados, especialmente durante a transformação em que os resultados intermédios são transitórios. Na piscina SQL, existem mesas temporárias ao nível da sessão.  

As tabelas temporárias só são visíveis para a sessão em que foram criadas e são automaticamente retiradas quando a sessão se desliga.  

As tabelas temporárias oferecem um benefício de desempenho porque os seus resultados são escritos para armazenamento local e não remoto.

As tabelas temporárias são úteis no processamento de dados, especialmente durante a transformação em que os resultados intermédios são transitórios. Com o SQL Analytics, existem mesas temporárias ao nível da sessão.  Só são visíveis para a sessão em que foram criados. Como tal, são automaticamente retirados quando a sessão se inicia. 

## <a name="temporary-tables-in-sql-pool"></a>Mesas temporárias na piscina SQL

No recurso de piscina SQL, as tabelas temporárias oferecem um benefício de desempenho porque os seus resultados são escritos para armazenamento local e não remoto.

### <a name="create-a-temporary-table"></a>Criar uma tabela temporária

As tabelas temporárias são criadas `#`prefixando o seu nome de mesa com a .  Por exemplo:

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

As tabelas temporárias `CTAS` também podem ser criadas com uma utilização exatamente da mesma abordagem:

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
> `CTAS`é um comando poderoso e tem a vantagem adicional de ser eficiente na sua utilização do espaço de registo de transações. 
> 
> 

## <a name="dropping-temporary-tables"></a>Largando mesas temporárias
Quando uma nova sessão é criada, não devem existir tabelas temporárias.  

Se está a chamar o mesmo procedimento armazenado, o que cria um `CREATE TABLE` temporário com o mesmo nome, `DROP` para garantir que as suas declarações são bem sucedidas, um simples cheque pré-existência com um pode ser usado como no seguinte exemplo:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para codificar a consistência, é uma boa prática usar este padrão tanto para mesas como para mesas temporárias.  Também é uma boa ideia `DROP TABLE` usar para remover mesas temporárias quando terminar com elas no seu código.  

No desenvolvimento de procedimentos armazenados, é comum ver os comandos de entrega agrupados no final de um procedimento para garantir que estes objetos são limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Código modular
Uma vez que as tabelas temporárias podem ser vistas em qualquer lugar numa sessão de utilizador, esta capacidade pode ser alavancada para ajudá-lo a modular o seu código de aplicação.  

Por exemplo, o seguinte procedimento armazenado gera DDL para atualizar todas as estatísticas na base de dados por nome estatístico:

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

Nesta fase, a única ação que ocorreu é a criação de um procedimento armazenado que gere uma tabela temporária, #stats_ddl, com declarações de DDL.  

Este procedimento armazenado deixa cair uma #stats_ddl existente para garantir que não falha se funcionar mais de uma vez dentro de uma sessão.  

No entanto, `DROP TABLE` uma vez que não existe no final do procedimento armazenado, quando o procedimento armazenado termina, deixa a mesa criada para que possa ser lida fora do procedimento armazenado.  

Na piscina SQL, ao contrário de outras bases de dados do SQL Server, é possível usar a tabela temporária fora do procedimento que a criou.  Mesas temporárias de piscina SQL podem ser usadas **em qualquer lugar** dentro da sessão. Esta funcionalidade pode levar a um código mais modular e manejável como no seguinte exemplo:

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

## <a name="temporary-table-limitations"></a>Limitações temporárias da tabela
O pool SQL impõe algumas limitações na implementação de tabelas temporárias.  Atualmente, apenas são apoiadas tabelas temporárias de sessão.  As Mesas Temporárias Globais não são apoiadas.  

Além disso, não se podem criar vistas em mesas temporárias.  As tabelas temporárias só podem ser criadas com hash ou distribuição de robin redondo.  A distribuição temporária de mesa replicada não é suportada. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o desenvolvimento de tabelas, consulte as tabelas de design utilizando o artigo de [recursos da SQL Analytics.](sql-data-warehouse-tables-overview.md)

