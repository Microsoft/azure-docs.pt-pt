---
title: Criando, atualizando estatísticas
description: Recomendações e exemplos para criar e atualizar estatísticas de otimização de consulta em tabelas no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c995358fc0135a1f9b504b57b23ecb3f6b41d6da
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692407"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Estatísticas de tabela no Azure SQL Data Warehouse

Recomendações e exemplos para criar e atualizar estatísticas de otimização de consulta em tabelas no Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Por que usar estatísticas

Quanto mais SQL Data Warehouse do Azure souber sobre seus dados, mais rápido ele poderá executar consultas em relação a ele. Depois de carregar dados no SQL Data Warehouse, coletar estatísticas sobre seus dados é uma das coisas mais importantes que você pode fazer para otimizar suas consultas. O otimizador de consultas do SQL Data Warehouse é um otimizador baseado no custo. Ele compara o custo de vários planos de consulta e escolhe o plano com o menor custo. Na maioria dos casos, ele escolhe o plano que executará o mais rápido. Por exemplo, se o otimizador estimar que a data em que a consulta está filtrando retornará uma linha, ela escolherá um plano. Se ele estimar que a data selecionada retornará 1 milhão linhas, ela retornará um plano diferente.

## <a name="automatic-creation-of-statistic"></a>Criação automática de estatística

Quando a opção de AUTO_CREATE_STATISTICS do banco de dados está ativada, SQL Data Warehouse analisa as consultas de usuário de entrada para obter as estatísticas ausentes. Se as estatísticas estiverem ausentes, o otimizador de consulta criará estatísticas em colunas individuais no predicado de consulta ou condição de junção para melhorar as estimativas de cardinalidade para o plano de consulta. A criação automática de estatísticas está atualmente ativada por padrão.

Você pode verificar se o data warehouse tem AUTO_CREATE_STATISTICS configurado executando o seguinte comando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se o data warehouse não tiver AUTO_CREATE_STATISTICS configurado, recomendamos que você habilite essa propriedade executando o seguinte comando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Essas instruções irão disparar a criação automática de estatísticas:

- SELECIONAR
- INSERIR-SELECIONAR
- CTAS
- UPDATE
- DELETE
- Explique quando contém uma junção ou se a presença de um predicado é detectada

> [!NOTE]
> A criação automática de estatísticas não é criada em tabelas temporárias ou externas.

A criação automática de estatísticas é feita de forma síncrona para que você possa incorrer em um desempenho de consulta ligeiramente degradado se suas colunas estiverem com estatísticas ausentes. O tempo para criar estatísticas para uma única coluna depende do tamanho da tabela. Para evitar degradação de desempenho mensurável, especialmente em benchmarking de desempenho, você deve garantir que as estatísticas tenham sido criadas primeiro executando a carga de trabalho de parâmetro de comparação antes de criar o perfil do sistema.

> [!NOTE]
> A criação de estatísticas será registrada em [Sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) em um contexto de usuário diferente.

Quando as estatísticas automáticas são criadas, elas assumem o formato: _WA_Sys_< ID de coluna de 8 dígitos em Hex > _ < 8 dígitos ID de tabela em hexadecimal >. Você pode exibir estatísticas que já foram criadas executando o comando [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

O table_name é o nome da tabela que contém as estatísticas a serem exibidas. Não pode ser uma tabela externa. O destino é o nome do índice de destino, estatísticas ou coluna para o qual exibir informações de estatísticas.

## <a name="updating-statistics"></a>Atualizando estatísticas

Uma prática recomendada é atualizar as estatísticas nas colunas de data a cada dia à medida que novas datas são adicionadas. Cada vez que novas linhas são carregadas na data warehouse, novas datas de carregamento ou datas de transação são adicionadas. Eles alteram a distribuição de dados e tornam as estatísticas desatualizadas. Por outro lado, as estatísticas em uma coluna de país/região em uma tabela de cliente talvez nunca precisem ser atualizadas, pois a distribuição de valores geralmente não é alterada. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não vai alterar a distribuição de dados. No entanto, se seu data warehouse contiver apenas um país/região e você inserir dados de um novo país/região, resultando em dados de vários países/regiões que estão sendo armazenados, você precisará atualizar as estatísticas na coluna país/região.

A seguir estão as recomendações atualizando as estatísticas:

|||
|-|-|
| **Frequência de atualizações de estatísticas**  | Conservador: diariamente </br> Depois de carregar ou transformar seus dados |
| **Amostragem** |  Menos de 1.000.000.000 linhas, use amostragem padrão (20 por cento). </br> Com mais de 1.000.000.000 linhas, use a amostragem de dois por cento. |

Uma das primeiras perguntas a serem feitas quando você está Solucionando problemas de uma consulta é, **"as estatísticas estão atualizadas?"**

Essa pergunta não é aquela que pode ser respondida pela idade dos dados. Um objeto de estatísticas atualizado pode ser antigo se não houver nenhuma alteração de material nos dados subjacentes. Quando o número de linhas foi alterado substancialmente ou há uma alteração material na distribuição de valores para uma *coluna, é* hora de atualizar as estatísticas.

Não há nenhuma exibição de gerenciamento dinâmico para determinar se os dados dentro da tabela foram alterados desde a última atualização das estatísticas. Conhecer a idade de suas estatísticas pode fornecer uma parte da imagem. Você pode usar a consulta a seguir para determinar a última vez em que suas estatísticas foram atualizadas em cada tabela.

> [!NOTE]
> Se houver uma alteração de material na distribuição de valores para uma coluna, você deverá atualizar as estatísticas independentemente da última vez em que foram atualizadas.

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

As **colunas de data** em uma data warehouse, por exemplo, geralmente precisam de atualizações de estatísticas frequentes. Cada vez que novas linhas são carregadas na data warehouse, novas datas de carregamento ou datas de transação são adicionadas. Eles alteram a distribuição de dados e tornam as estatísticas desatualizadas. Por outro lado, as estatísticas em uma coluna de gênero em uma tabela de cliente talvez nunca precisem ser atualizadas. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não vai alterar a distribuição de dados. No entanto, se seu data warehouse contiver apenas um gênero e um novo requisito resultar em vários gêneros, você precisará atualizar as estatísticas na coluna sexo.

Para obter mais informações, consulte diretrizes gerais para [estatísticas](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementando o gerenciamento de estatísticas

Geralmente, é uma boa ideia estender o processo de carregamento de dados para garantir que as estatísticas sejam atualizadas no final da carga. O carregamento de dados é quando as tabelas mudam com mais freqüência seu tamanho e/ou sua distribuição de valores. Portanto, esse é um local lógico para implementar alguns processos de gerenciamento.

Os seguintes princípios de orientação são fornecidos para atualizar suas estatísticas durante o processo de carregamento:

* Certifique-se de que cada tabela carregada tenha pelo menos um objeto de estatísticas atualizado. Isso atualiza as informações de tamanho da tabela (contagem de linhas e contagem de páginas) como parte da atualização de estatísticas.
* Concentre-se em colunas que participam de cláusulas de junção, agrupar por, ORDENAr por e DISTINCT.
* Considere a atualização de colunas de "chave crescente", como datas de transação com mais frequência, porque esses valores não serão incluídos no histograma de estatísticas.
* Considere atualizar colunas de distribuição estática com menos frequência.
* Lembre-se de que cada objeto de estatística é atualizado em sequência. Simplesmente implementar `UPDATE STATISTICS <TABLE_NAME>` nem sempre é ideal, especialmente para grandes tabelas com muitos objetos de estatísticas.

Para obter mais informações, consulte [estimativa de cardinalidade](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Exemplos: CREATE STATISTICS

Esses exemplos mostram como usar várias opções para criar estatísticas. As opções que você usa para cada coluna dependem das características de seus dados e de como a coluna será usada em consultas.

### <a name="create-single-column-statistics-with-default-options"></a>Criar estatísticas de coluna única com opções padrão

Para criar estatísticas em uma coluna, basta fornecer um nome para o objeto de estatísticas e o nome da coluna.

Essa sintaxe usa todas as opções padrão. Por padrão, SQL Data Warehouse amostras de **20%** da tabela ao criar estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando cada linha

A taxa de amostragem padrão de 20 por cento é suficiente para a maioria das situações. No entanto, você pode ajustar a taxa de amostragem.

Para obter uma amostra da tabela completa, use esta sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única especificando o tamanho da amostra

Como alternativa, você pode especificar o tamanho da amostra como uma porcentagem:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Criar estatísticas de coluna única em apenas algumas das linhas

Você também pode criar estatísticas em uma parte das linhas em sua tabela. Isso é chamado de estatística filtrada.

Por exemplo, você pode usar estatísticas filtradas quando planeja consultar uma partição específica de uma tabela particionada grande. Ao criar estatísticas somente em valores de partição, a precisão das estatísticas será aprimorada e, portanto, melhorará o desempenho da consulta.

Este exemplo cria estatísticas em um intervalo de valores. Os valores podem ser facilmente definidos para corresponder ao intervalo de valores em uma partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para que o otimizador de consulta considere o uso de estatísticas filtradas quando ele escolhe o plano de consulta distribuída, a consulta deve se ajustar dentro da definição do objeto de estatísticas. Usando o exemplo anterior, a cláusula WHERE da consulta precisa especificar valores Col1 entre 2000101 e 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Criar estatísticas de coluna única com todas as opções

Você também pode combinar as opções juntas. O exemplo a seguir cria um objeto de estatísticas filtradas com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, consulte [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Criar estatísticas de várias colunas

Para criar um objeto de estatísticas de várias colunas, basta usar os exemplos anteriores, mas especificar mais colunas.

> [!NOTE]
> O histograma, que é usado para estimar o número de linhas no resultado da consulta, está disponível somente para a primeira coluna listada na definição do objeto de estatísticas.

Neste exemplo, o histograma está no *produto\_categoria*. As estatísticas entre colunas são calculadas no *produto\_categoria* e *\_do produto sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Como há uma correlação entre o *produto\_categoria* e o *produto\_sub\_categoria*, um objeto de estatísticas de várias colunas poderá ser útil se essas colunas forem acessadas ao mesmo tempo.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Criar estatísticas em todas as colunas em uma tabela

Uma maneira de criar estatísticas é emitir comandos CREATE STATISTICs depois de criar a tabela:

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Usar um procedimento armazenado para criar estatísticas em todas as colunas de um banco de dados

SQL Data Warehouse não tem um procedimento armazenado do sistema equivalente a sp_create_stats no SQL Server. Esse procedimento armazenado cria um objeto de estatísticas de coluna única em cada coluna do banco de dados que ainda não tem estatísticas.

O exemplo a seguir ajudará você a começar a usar seu design de banco de dados. Sinta-se à vontade para adaptá-lo às suas necessidades:

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

Para criar estatísticas em todas as colunas na tabela usando os padrões, execute o procedimento armazenado.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Para criar estatísticas em todas as colunas na tabela usando um FULLSCAN, chame este procedimento:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Para criar estatísticas de amostra em todas as colunas na tabela, digite 3 e a porcentagem de amostra. Esses procedimentos usam uma taxa de amostragem de 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

Para criar estatísticas de amostra em todas as colunas

## <a name="examples-update-statistics"></a>Exemplos: atualizar estatísticas

Para atualizar as estatísticas, você pode:

- Atualizar um objeto de estatísticas. Especifique o nome do objeto de estatísticas que você deseja atualizar.
- Atualizar todos os objetos de estatísticas em uma tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específico.

### <a name="update-one-specific-statistics-object"></a>Atualizar um objeto de estatísticas específico

Use a sintaxe a seguir para atualizar um objeto de estatísticas específico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos de estatísticas específicos, você pode minimizar o tempo e os recursos necessários para gerenciar estatísticas. Isso requer algumas idéias para escolher os melhores objetos de estatísticas a serem atualizados.

### <a name="update-all-statistics-on-a-table"></a>Atualizar todas as estatísticas em uma tabela

Um método simples para atualizar todos os objetos de estatísticas em uma tabela é:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

A instrução UPDATE STATISTICs é fácil de usar. Apenas lembre-se de que ele atualiza *todas as* estatísticas na tabela e, portanto, pode executar mais trabalho do que o necessário. Se o desempenho não for um problema, essa é a maneira mais fácil e completa de garantir que as estatísticas estejam atualizadas.

> [!NOTE]
> Ao atualizar todas as estatísticas em uma tabela, SQL Data Warehouse faz uma verificação para obter uma amostra da tabela para cada objeto de estatísticas. Se a tabela for grande e tiver muitas colunas e muitas estatísticas, poderá ser mais eficiente atualizar as estatísticas individuais com base na necessidade.

Para obter uma implementação de um procedimento de `UPDATE STATISTICS`, consulte [tabelas temporárias](sql-data-warehouse-tables-temporary.md). O método de implementação é ligeiramente diferente do procedimento de `CREATE STATISTICS` anterior, mas o resultado é o mesmo.

Para obter a sintaxe completa, consulte [atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Metadados de estatísticas

Há várias exibições e funções do sistema que você pode usar para encontrar informações sobre estatísticas. Por exemplo, você pode ver se um objeto de estatísticas pode estar desatualizado usando a função stats-Date para ver quando as estatísticas foram criadas ou atualizadas pela última vez.

### <a name="catalog-views-for-statistics"></a>Exibições de catálogo para estatísticas

Essas exibições do sistema fornecem informações sobre estatísticas:

| exibição do catálogo | Descrição |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Uma linha para cada coluna. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Uma linha para cada objeto no banco de dados. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Uma linha para cada esquema no banco de dados. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Uma linha para cada objeto de estatísticas. |
| [sys. stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Uma linha para cada coluna no objeto de estatísticas. Retorna links para sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Uma linha para cada tabela (inclui tabelas externas). |
| [sys. table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Uma linha para cada tipo de dados. |

### <a name="system-functions-for-statistics"></a>Funções do sistema para estatísticas

Essas funções do sistema são úteis para trabalhar com estatísticas:

| Função do sistema | Descrição |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Data em que o objeto de estatísticas foi atualizado pela última vez. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Nível de resumo e informações detalhadas sobre a distribuição de valores como compreendidos pelo objeto de estatísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar colunas e funções de estatísticas em uma exibição

Essa exibição traz colunas relacionadas a estatísticas e resultados da função STATS_DATE () juntas.

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

## <a name="dbcc-show_statistics-examples"></a>Exemplos de DBCC SHOW_STATISTICS ()

DBCC SHOW_STATISTICS () mostra os dados mantidos em um objeto de estatísticas. Esses dados são fornecidos em três partes:

- Cabeçalho
- Vetor de densidade
- Histograma

Os metadados de cabeçalho sobre as estatísticas. O histograma exibe a distribuição de valores na primeira coluna de chave do objeto de estatísticas. O vetor de densidade mede a correlação entre colunas. SQL Data Warehouse computa estimativas de cardinalidade com qualquer um dos dados no objeto de estatísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar cabeçalho, densidade e histograma

Este exemplo simples mostra todas as três partes de um objeto de estatísticas:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrar uma ou mais partes do DBCC SHOW_STATISTICS ()

Se você estiver interessado apenas em exibir partes específicas, use a cláusula `WITH` e especifique quais partes deseja ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Diferenças do DBCC SHOW_STATISTICS ()

O DBCC SHOW_STATISTICS () é mais estritamente implementado em SQL Data Warehouse em comparação com SQL Server:

- Não há suporte para recursos não documentados.
- Não é possível usar Stats_stream.
- Não é possível unir resultados para subconjuntos específicos de dados de estatísticas. Por exemplo, STAT_HEADER INGRESSAr DENSITY_VECTOR.
- NO_INFOMSGS não pode ser definida para supressão de mensagem.
- Não é possível usar colchetes em nomes de estatísticas.
- Não é possível usar nomes de coluna para identificar objetos de estatísticas.
- Não há suporte para o erro personalizado 2767.

## <a name="next-steps"></a>Passos seguintes

Para melhorar ainda mais o desempenho da consulta, consulte [monitorar sua carga de trabalho](sql-data-warehouse-manage-monitor.md)
