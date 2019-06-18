---
title: Criar, atualizar estatísticas - Azure SQL Data Warehouse | Documentos da Microsoft
description: Recomendações e exemplos para criar e atualizar as estatísticas de otimização de consultas em tabelas no armazém de dados SQL do Azure.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: c5043d99dd130bc7dc7b35eaa5ecadf11d7644db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65851532"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Estatísticas de tabela no Azure SQL Data Warehouse

Recomendações e exemplos para criar e atualizar as estatísticas de otimização de consultas em tabelas no armazém de dados SQL do Azure.

## <a name="why-use-statistics"></a>Por que usar as estatísticas

Quanto mais o Azure SQL Data Warehouse sabe sobre os seus dados, mais rapidamente, pode executar consultas em relação a ele. Depois de carregar dados para o SQL Data Warehouse, a coletar estatísticas nos seus dados é uma das coisas mais importantes que pode fazer para otimizar as suas consultas. O otimizador de consultas do SQL Data Warehouse é um otimizador baseado no custo. Ele compara o custo de vários planos de consulta e, em seguida, escolhe o plano com o custo mais baixo. Na maioria dos casos, ele escolhe o plano que executará o mais rápido. Por exemplo, se o otimizador estima que a data que está a filtrar na sua consulta retornará uma linha irá escolher um plano. Se ele estima que a data selecionada retornará 1 milhão de linhas, ela retornará um plano diferente.

## <a name="automatic-creation-of-statistic"></a>Criação automática de estatística

Quando a opção de AUTO_CREATE_STATISTICS de base de dados está ativado, o SQL Data Warehouse analisa entrada consultas do utilizador para as estatísticas não encontradas. Se as estatísticas em falta, o otimizador de consultas cria estatísticas em colunas individuais na condição de predicado ou uma associação de consulta para melhorar as estimativas de cardinalidade para o plano de consulta. Criação automática de estatísticas está atualmente ativada por predefinição.

Pode verificar se o seu armazém de dados tem AUTO_CREATE_STATISTICS configuradas ao executar o seguinte comando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se o seu armazém de dados não tiver AUTO_CREATE_STATISTICS configurado, recomendamos que ative esta propriedade ao executar o seguinte comando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Essas instruções irão disparar a criação automática de estatísticas de:

- SELECIONAR
- INSERIR SELEÇÃO
- CTAS
- UPDATE
- DELETE
- EXPLIQUE quando é detetado um contendo uma junção ou a presença de um predicado

> [!NOTE]
> Criação automática de estatísticas não são criados em tabelas temporárias ou externas.

Criação automática de estatísticas é feita de forma síncrona para que o utilizador possa incorrer desempenho de consulta um pouco degradado se as colunas estão em falta as estatísticas. O tempo para criar estatísticas para uma única coluna depende do tamanho da tabela. Para evitar a degradação do desempenho mensuráveis, especialmente de benchmark de desempenho, deve certificar-se de estatísticas foram criadas pela primeira vez ao executar a carga de trabalho de benchmark antes do sistema de criação de perfis.

> [!NOTE]
> A criação de estatísticas será registada no [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) sob um contexto diferente.

Quando são criadas estatísticas automáticas, eles serão tomam a forma: _WA_Sys_< id de coluna de 8 dígitos em hexadecimal > _ < id da tabela 8 dígitos em hexadecimal >. Pode ver estatísticas que já foram criadas ao executar o [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest) comando:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

O nome_tabela é o nome da tabela que contém as estatísticas para apresentar. Não pode ser uma tabela externa. O destino é o nome do índice de destino, as estatísticas ou coluna para o qual devem ser apresentadas informações de estatísticas.

## <a name="updating-statistics"></a>Atualizar as estatísticas

Um procedimento recomendado é atualizar estatísticas em colunas de data por dia à medida que são adicionadas novas datas. Cada novas linhas de tempo são carregados para o armazém de dados, as datas de carga novo ou datas de transação são adicionadas. Estes alterar a distribuição de dados e tornam as estatísticas Desatualizadas. Por outro lado, estatísticas numa coluna de país/região numa tabela de cliente nunca poderão ter de ser atualizado, uma vez que a distribuição dos valores em geral, não é alterado. Partindo do princípio de que a distribuição é constante entre os clientes, adicionar novas linhas para a variação de tabela não irá alterar a distribuição de dados. No entanto, se o armazém de dados contém apenas um país/região e importar dados a partir de um novo país/região, resultando em dados a partir de vários países/regiões a ser armazenados, em seguida, terá de atualizar as estatísticas na coluna de país/região.

Seguem-se a atualizar as estatísticas de recomendações:

|||
|-|-|
| **Frequência de atualizações de estatísticas**  | Conservador: Diariamente </br> Depois de carregar ou transformar os seus dados |
| **Amostragem** |  Menos de 1 milhões de linhas, utilize a amostragem de predefinição (20 por cento). </br> Com mais de 1 milhões de linhas, utilize a amostragem da dois por cento. |

Uma das primeiras perguntas para fazer quando estiver a resolver problemas uma consulta é, **"São as estatísticas atualizadas?"**

Essa questão não é um que possa ser respondido por idade dos dados. Um objeto de estatísticas atualizados pode ser antigo, não se tiver ocorrido nenhuma alteração material aos dados subjacentes. Quando o número de linhas foi alterado substancialmente ou existe uma alteração material na distribuição de valores para uma coluna *, em seguida,* está na altura de atualizar as estatísticas.

Não existe nenhuma vista de gestão dinâmica para determinar se os dados na tabela foi alterado, uma vez que as estatísticas de tempo passadas foram atualizadas. Saber a idade as estatísticas pode fornecer com parte da imagem. Pode usar a seguinte consulta para determinar a última vez que as estatísticas foram atualizadas em cada tabela.

> [!NOTE]
> Se houver uma alteração material na distribuição de valores para uma coluna, deve atualizar as estatísticas, independentemente da última vez que eles foram atualizados.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Colunas de datas** num data warehouse, por exemplo, normalmente precisa frequentes atualizações de estatísticas. Cada novas linhas de tempo são carregados para o armazém de dados, as datas de carga novo ou datas de transação são adicionadas. Estes alterar a distribuição de dados e tornam as estatísticas Desatualizadas. Por outro lado, as estatísticas numa coluna de género numa tabela de clientes poderão nunca precisam de ser atualizados. Partindo do princípio de que a distribuição é constante entre os clientes, adicionar novas linhas para a variação de tabela não irá alterar a distribuição de dados. No entanto, se o seu armazém de dados contém apenas um sexo e uma nova resulta de requisito em vários géneros, em seguida, terá de atualizar as estatísticas na coluna de género.

Para obter mais informações, consulte as orientações gerais para [estatísticas](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementar o gerenciamento de estatísticas

Muitas vezes, é uma boa idéia para expandir o seu processo de carregamento de dados para se certificar de que as estatísticas são atualizadas no final da carga. O carregamento de dados é quando tabelas com mais frequência alteram seu tamanho e/ou a distribuição dos valores dos mesmos. Portanto, isso é um lugar lógico para implementar alguns processos de gestão.

Os seguintes princípios orientadores são fornecidos para atualizar as estatísticas durante o processo de carregamento:

* Certifique-se de que cada tabela carregada tem, pelo menos, um objeto de estatísticas atualizado. Este procedimento atualiza as informações de tamanho (contagem de linhas e contagem de páginas) de tabela como parte da atualização de estatísticas.
* Se concentrar em colunas que participam em cláusulas de associação, GROUP BY, ORDER BY e DISTINCT.
* Considere a atualização de "ascendente chave" colunas como datas de transação com mais frequência, uma vez que estes valores não serão incluídos no histograma estatísticas.
* Considere atualizar colunas de distribuição estático menor com frequência.
* Lembre-se de que cada objeto de estatística é atualizado na sequência. Basta implementar `UPDATE STATISTICS <TABLE_NAME>` nem sempre é ideal, especialmente para tabelas grandes com muitas de objetos de estatísticas.

Para obter mais informações, consulte [estimativa de cardinalidade](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Exemplos: Criar estatísticas

Estes exemplos mostram como utilizar várias opções para a criação de estatísticas. As opções que utilizar para cada coluna dependem as características dos dados e como a coluna será utilizada em consultas.

### <a name="create-single-column-statistics-with-default-options"></a>Criar estatísticas de coluna única com opções padrão

Para criar estatísticas numa coluna, forneça um nome para o objeto de estatísticas e o nome da coluna.

Essa sintaxe utiliza todas as opções padrão. Por predefinição, os exemplos do SQL Data Warehouse **20 por cento** da tabela ao criar estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única, examinando cada linha

A frequência de amostragem de predefinição de 20 por cento é suficiente para a maioria das situações. No entanto, pode ajustar a frequência de amostragem.

Para a tabela completa de exemplo, utilize esta sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única, especificando o tamanho da amostra

Em alternativa, pode especificar o tamanho da amostra como uma percentagem:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Criar estatísticas de coluna única em apenas algumas das linhas

Também pode criar estatísticas numa parte das linhas na tabela. Isso é chamado uma estatística filtrada.

Por exemplo, pode utilizar as estatísticas filtradas quando planear consultar uma partição específica de uma grande tabela particionada. Ao criar estatísticas em apenas os valores de partição, a precisão das estatísticas irá melhorar e, portanto, melhorar o desempenho das consultas.

Este exemplo cria estatísticas num intervalo de valores. Os valores podem ser facilmente definidos de acordo com o intervalo de valores numa partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para o otimizador de consultas considerar a utilização de estatísticas filtradas quando ele escolhe o plano de consulta distribuída, a consulta deverá ser colocado dentro da definição do objeto de estatísticas. Usando o exemplo anterior, o onde cláusula tem de especificar valores de col1 entre 2000101 e 20001231 da consulta.

### <a name="create-single-column-statistics-with-all-the-options"></a>Criar estatísticas de coluna única com todas as opções

Também pode combinar as opções em conjunto. O exemplo seguinte cria um objeto de estatísticas filtradas com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, consulte [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Criar estatísticas de várias colunas

Para criar um objeto de estatísticas de várias colunas, basta utilizar os exemplos anteriores, mas especificar mais colunas.

> [!NOTE]
> O histograma, que é utilizado para calcular o número de linhas no resultado da consulta, só está disponível para a primeira coluna listada na definição do objeto de estatísticas.

Neste exemplo, o histograma está ativada *produto\_categoria*. Estatísticas de coluna para várias são calculadas *produto\_categoria* e *produto\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Porque há uma correlação entre *produto\_categoria* e *produto\_sub\_categoria*, um objeto de estatísticas de várias colunas pode ser úteis se estes colunas são acedidas ao mesmo tempo.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Criar estatísticas em todas as colunas numa tabela

Uma forma de criar estatísticas é emitir comandos de CREATE STATISTICS depois de criar a tabela:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Usar um procedimento armazenado para criar estatísticas em todas as colunas numa base de dados

O SQL Data Warehouse não tem um procedimento armazenado do sistema equivalente a sp_create_stats no SQL Server. Este procedimento armazenado cria um objeto de estatísticas de coluna única em cada coluna da base de dados que já não tem as estatísticas.

O exemplo seguinte irá ajudá-lo a começar com o seu design de banco de dados. Fique à vontade para adaptá-lo às suas necessidades:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para criar estatísticas em todas as colunas na tabela com as predefinições, execute o procedimento armazenado.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Para criar estatísticas em todas as colunas na tabela utilizando uma fullscan, chame este procedimento:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Para criar estatísticas de amostragem em todas as colunas na tabela, introduza 3 e a percentagem de exemplo. Este procedimentos utiliza uma taxa de amostragem de 20 por cento.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

Para criar estatísticas em todas as colunas de objeto de amostragem

## <a name="examples-update-statistics"></a>Exemplos: Atualizar estatísticas

Atualizar as estatísticas, pode:

- Atualize um objeto de estatísticas. Especifique o nome do objeto de estatísticas que pretende atualizar.
- Atualize todos os objetos de estatísticas numa tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específicas.

### <a name="update-one-specific-statistics-object"></a>Atualizar um objeto de estatísticas específicas

Utilize a seguinte sintaxe para atualizar um objeto de estatísticas específicas:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos de estatísticas específico, pode minimizar o tempo e os recursos necessários para gerir estatísticas. Isso requer algumas pensado para escolher os melhor objetos de estatísticas para atualizar.

### <a name="update-all-statistics-on-a-table"></a>Atualizar todas as estatísticas numa tabela

Um método simples para atualizar todos os objetos de estatísticas numa tabela é:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

A instrução de UPDATE STATISTICS é fácil de usar. Lembre-se de que ele atualiza *todos os* estatísticas na tabela e, portanto, poderá realizar mais trabalho do que é necessário. Se o desempenho não é um problema, esta é a forma mais fácil e mais completa para garantir que as estatísticas são atualizadas.

> [!NOTE]
> Ao atualizar todas as estatísticas numa tabela, o SQL Data Warehouse faz uma análise para a tabela para cada objeto de estatísticas de exemplo. Se a tabela é grande e tem o número de colunas e estatísticas de muitos, poderá ser mais eficiente para atualizar as estatísticas de individuais com base na necessidade.

Para uma implementação de um `UPDATE STATISTICS` procedimento, consulte [tabelas temporárias](sql-data-warehouse-tables-temporary.md). O método de implementação é ligeiramente diferente do anterior `CREATE STATISTICS` procedimento, mas o resultado é o mesmo.

Para a sintaxe completa, consulte [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Metadados de estatísticas

Existem várias vistas de sistema e as funções que pode utilizar para obter informações sobre as estatísticas. Por exemplo, pode ver se um objeto de estatísticas poderá estar desatualizado através da função de estatísticas-date para ver quando estatísticas foram criadas ou atualizadas pela última vez.

### <a name="catalog-views-for-statistics"></a>Exibições de catálogo para estatísticas

Estas vistas de sistema fornecem informações sobre estatísticas de:

| Vista de catálogo | Descrição |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Uma linha para cada coluna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Uma linha para cada objeto na base de dados. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Uma linha para cada esquema na base de dados. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Uma linha para cada objeto de estatísticas. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Uma linha para cada coluna no objeto de estatísticas. Links para sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Uma linha para cada tabela (inclui as tabelas externas). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Uma linha para cada tipo de dados. |

### <a name="system-functions-for-statistics"></a>Funções de sistema para estatísticas

Estas funções de sistema são úteis para trabalhar com estatísticas:

| Função de sistema | Descrição |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Data que da última atualização o objeto de estatísticas. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Resumidas nível e informações detalhadas sobre a distribuição dos valores como compreendido pelo objeto de estatísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar colunas de estatísticas e as funções numa exibição

Esta vista apresenta as colunas que estão relacionadas com a estatísticas e resulta em conjunto a partir da função STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Exemplos de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mostra os dados mantidos dentro de um objeto de estatísticas. Estes dados é fornecido em três partes:

- Cabeçalho
- Vetor de densidade
- Histograma

Os metadados de cabeçalho sobre as estatísticas. O histograma mostra a distribuição dos valores na primeira coluna chave do objeto de estatísticas. O vetor de densidade mede a correlação entre colunas. O SQL Data Warehouse calcula as estimativas de cardinalidade com quaisquer dados no objeto de estatísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar o cabeçalho, a densidade e o histograma

Neste exemplo simples mostra todas as três partes de um objeto de estatísticas:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Mostrar um ou mais partes de DBCC SHOW_STATISTICS()

Se só estiver interessado em visualizar partes específicas, utilize o `WITH` cláusula e especificar as partes que pretende ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Diferenças de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mais rigorosa é implementado no armazém de dados SQL em comparação com o SQL Server:

- Funcionalidades não-documentadas não são suportadas.
- Não é possível utilizar Stats_stream.
- Não é possível associar os resultados para subconjuntos específicos dos dados de estatísticas. Por exemplo, STAT_HEADER DENSITY_VECTOR de associação.
- NO_INFOMSGS não pode ser definido para supressão de mensagem.
- Não é possível utilizar nomes de estatísticas entre colchetes.
- Não é possível utilizar nomes de coluna para identificar objetos de estatísticas.
- Erro personalizado 2767 não é suportado.

## <a name="next-steps"></a>Passos Seguintes

Para melhorar ainda mais o desempenho das consultas, consulte [monitorizar a carga de trabalho](sql-data-warehouse-manage-monitor.md)
