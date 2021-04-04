---
title: Tabelas temporárias
description: Orientação essencial para a utilização de mesas temporárias em piscinas SQL dedicadas, destacando os princípios das tabelas temporárias de nível de sessão.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 077782099d6d61982052dc1690d545e58e928d8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93310686"
---
# <a name="temporary-tables-in-dedicated-sql-pool"></a>Mesas temporárias em piscina SQL dedicada

Este artigo contém orientações essenciais para a utilização de tabelas temporárias e destaca os princípios das tabelas temporárias de nível de sessão. 

A utilização das informações neste artigo pode ajudá-lo a modular o seu código, melhorando tanto a reutilização como a facilidade de manutenção.

## <a name="what-are-temporary-tables"></a>O que são mesas temporárias?

As tabelas temporárias são úteis no processamento de dados, especialmente durante a transformação onde os resultados intermédios são transitórios. Na piscina SQL dedicada, existem mesas temporárias ao nível da sessão.  

As tabelas temporárias só são visíveis para a sessão em que foram criadas e são automaticamente largadas quando a sessão termina.  

As tabelas temporárias oferecem um benefício de desempenho porque os seus resultados são escritos para armazenamento local e não remoto.

As tabelas temporárias são úteis no processamento de dados, especialmente durante a transformação onde os resultados intermédios são transitórios. Com piscina SQL dedicada, existem mesas temporárias ao nível da sessão.  Só são visíveis para a sessão em que foram criados. Como tal, são automaticamente largados quando a sessão termina. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Mesas temporárias em piscina SQL dedicada

No recurso de piscina SQL dedicado, as tabelas temporárias oferecem um benefício de desempenho porque os seus resultados são escritos para armazenamento local e não remoto.

### <a name="create-a-temporary-table"></a>Criar uma tabela temporária

As tabelas temporárias são criadas prefixando o nome da sua mesa com um `#` .  Por exemplo:

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

As tabelas temporárias também podem ser criadas com uma `CTAS` utilização exatamente a mesma:

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
> `CTAS` é um comando poderoso e tem a vantagem adicional de ser eficiente na sua utilização do espaço de log de transações. 
> 
> 

## <a name="dropping-temporary-tables"></a>Largando mesas temporárias
Quando uma nova sessão é criada, não devem existir mesas temporárias.  

Se você está chamando o mesmo procedimento armazenado, o que cria um temporário com o mesmo nome, para garantir que as suas `CREATE TABLE` declarações são bem sucedidas, um simples check-in pré-existência com um `DROP` pode ser usado como no seguinte exemplo:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para a consistência de codificação, é uma boa prática usar este padrão tanto para mesas como para mesas temporárias.  Também é uma boa ideia usar `DROP TABLE` para remover mesas temporárias quando terminar com elas no seu código.  

No desenvolvimento de procedimentos armazenados, é comum ver os comandos de queda agrupados no final de um procedimento para garantir que estes objetos são limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Código modularizador
Uma vez que as tabelas temporárias podem ser vistas em qualquer lugar numa sessão de utilizador, esta capacidade pode ser aproveitada para ajudá-lo a modular o seu código de aplicação.  

Por exemplo, o seguinte procedimento armazenado gera DDL para atualizar todas as estatísticas da base de dados por nome estatístico:

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
FROM    #stats_ddl
;
GO
```

Nesta fase, a única ação que ocorreu foi a criação de um procedimento armazenado que gere uma tabela temporária, #stats_ddl, com declarações do DDL.  

Este procedimento armazenado deixa cair um #stats_ddl existente para garantir que não falhe se for executado mais do que uma vez numa sessão.  

No entanto, uma vez que não existe `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado termina, deixa a tabela criada para que possa ser lida fora do procedimento armazenado.  

Em pool SQL dedicado, ao contrário de outras bases de dados do SQL Server, é possível utilizar a tabela temporária fora do procedimento que a criou.  Mesas temporárias de piscina SQL dedicadas podem ser **usadas em qualquer lugar** dentro da sessão. Esta funcionalidade pode levar a um código mais modular e manejável, como no exemplo seguinte:

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
Piscina DE SQL dedicada impõe um par de limitações na implementação de tabelas temporárias.  Atualmente, apenas são suportadas as tabelas temporárias de sessão.  As Tabelas Temporárias Globais não são apoiadas.  

Além disso, as vistas não podem ser criadas em mesas temporárias.  As tabelas temporárias só podem ser criadas com distribuição de haxixe ou rodapé redondo.  A distribuição de mesa temporária replicada não é suportada. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o desenvolvimento de tabelas, consulte as tabelas de Design usando o artigo [de piscina SQL dedicado.](sql-data-warehouse-tables-overview.md)

