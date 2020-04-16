---
title: Utilize mesas temporárias com Synapse SQL
description: Orientação essencial para a utilização de tabelas temporárias em Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428761"
---
# <a name="temporary-tables-in-synapse-sql"></a>Mesas temporárias em Synapse SQL

Este artigo contém orientações essenciais para a utilização de tabelas temporárias e destaca os princípios das tabelas temporárias de nível de sessão no Synapse SQL. 

Tanto o pool SQL como os recursos sql on-demand (pré-visualização) podem utilizar tabelas temporárias. A SQL on-demand tem limitações que são discutidas no final deste artigo. 

## <a name="what-are-temporary-tables"></a>O que são mesas temporárias?

As tabelas temporárias são úteis no processamento de dados, especialmente durante a transformação em que os resultados intermédios são transitórios. Com synapse SQL, mesas temporárias existem ao nível da sessão.  Só são visíveis para a sessão em que foram criados. Como tal, são automaticamente retirados quando a sessão se inicia. 

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

### <a name="dropping-temporary-tables"></a>Largando mesas temporárias
Quando uma nova sessão é criada, não devem existir tabelas temporárias.  No entanto, se estiver a chamar o mesmo procedimento armazenado que cria `CREATE TABLE` um temporário com o mesmo nome, para garantir que as suas declarações são bem sucedidas, utilize um simples cheque pré-existência com: `DROP` 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para codificar a consistência, é uma boa prática usar este padrão tanto para mesas como para mesas temporárias.  Também é uma boa ideia `DROP TABLE` usar para remover mesas temporárias quando terminar com elas.  

No desenvolvimento de procedimentos armazenados, é comum ver os comandos de entrega agrupados no final de um procedimento para garantir que estes objetos são limpos.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>Código modular
As tabelas temporárias podem ser utilizadas em qualquer lugar numa sessão de utilizador. Esta capacidade pode então ser explorada para ajudá-lo a modular o seu código de aplicação.  Para demonstrar, o seguinte procedimento armazenado gera DDL para atualizar todas as estatísticas na base de dados por nome estatístico:

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

Nesta fase, a única ação que ocorreu é a criação de um procedimento armazenado que gere a #stats_ddl tabela temporária.  O procedimento armazenado cai #stats_ddl se já existe. Esta queda garante que não falha se correr mais de uma vez numa sessão.  

Uma vez que `DROP TABLE` não há um no final do procedimento armazenado, quando o procedimento armazenado termina, a mesa criada permanece e pode ser lida fora do procedimento armazenado.  

Ao contrário de outras bases de dados do SQL Server, o Synapse SQL permite-lhe utilizar a tabela temporária fora do procedimento que a criou.  As tabelas temporárias criadas através da piscina SQL podem ser usadas **em qualquer lugar** dentro da sessão. Como resultado, terá um código mais modular e manejável, como demonstrado na amostra abaixo:

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

### <a name="temporary-table-limitations"></a>Limitações temporárias da tabela

O pool SQL tem algumas limitações de implementação para tabelas temporárias:

- Apenas são apoiadas mesas temporárias de sessão.  As Mesas Temporárias Globais não são apoiadas.
- Não se podem criar vistas em mesas temporárias.
- As tabelas temporárias só podem ser criadas com hash ou distribuição de robin redondo.  A distribuição temporária de mesa replicada não é suportada. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Tabelas temporárias em SQL a pedido (pré-visualização)

As tabelas temporárias na SQL a pedido são suportadas, mas a sua utilização é limitada. Não podem ser usados em consultas que visam ficheiros. 

Por exemplo, não pode aderir a uma tabela temporária com dados de ficheiros armazenados. O número de tabelas temporárias é limitado a 100, e o seu tamanho total é limitado a 100MB.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o desenvolvimento de tabelas, consulte as tabelas de design utilizando o artigo de [recursos Synapse SQL.](develop-tables-overview.md)

