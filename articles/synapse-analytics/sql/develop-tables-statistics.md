---
title: Criar e atualizar estatísticas utilizando recursos SQL Azure Synapse
description: Recomendações e exemplos para a criação e atualização de estatísticas de otimização de consultas no Sinaapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b3e1c4b8dec0e62bb2a77939a36e38b61837033a
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638857"
---
# <a name="statistics-in-synapse-sql"></a>Estatísticas em Synapse SQL

Fornecidos neste artigo estão recomendações e exemplos para a criação e atualização de estatísticas de otimização de consultas utilizando os recursos Sinapse SQL: pool SQL dedicado e piscina SQL sem servidor (pré-visualização).

## <a name="statistics-in-dedicated-sql-pool"></a>Estatísticas em piscina SQL dedicada

### <a name="why-use-statistics"></a>Porquê usar estatísticas

Quanto mais dedicado o pool SQL souber dos seus dados, mais rápido poderá executar consultas. Depois de carregar dados num pool de SQL dedicado, recolher estatísticas sobre os seus dados é uma das coisas mais importantes que pode fazer para otimização de consultas.  

O otimizador de consulta de piscina SQL dedicado é um otimizador baseado em custos. Compara o custo de vários planos de consulta, e depois escolhe o plano com o menor custo. Na maioria dos casos, escolhe o plano que executará o mais rápido.

Por exemplo, se o optimizador estimar que a data em que a sua consulta está a ser filtrada voltará a uma linha, escolherá um plano. Se estimar que a data selecionada devolverá 1 milhão de linhas, devolverá um plano diferente.

### <a name="automatic-creation-of-statistics"></a>Criação automática de estatísticas

O motor de piscina SQL dedicado analisará as consultas recebidas dos utilizadores por estatísticas em falta quando a AUTO_CREATE_STATISTICS opção de base de dados estiver definida para `ON` .  Se faltam estatísticas, o otimizador de consulta cria estatísticas sobre colunas individuais na consulta predicado ou aderir à condição. 

Esta função é usada para melhorar as estimativas de cardinalícia para o plano de consulta.

> [!IMPORTANT]
> A criação automática de estatísticas é atualmente ligada por defeito.

Pode verificar se o seu armazém de dados AUTO_CREATE_STATISTICS configurado executando o seguinte comando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se o seu armazém de dados não tiver AUTO_CREATE_STATISTICS ativado, recomendamos que ative esta propriedade executando o seguinte comando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Estas declarações desencadearão a criação automática de estatísticas:

- SELECIONAR
- INSIRA-SELECÇÃO
- CTAS
- UPDATE
- DELETE
- EXPLICAR quando se encontra uma junção ou a presença de um predicado é detetada

> [!NOTE]
> A criação automática de estatísticas não é gerada em quadros temporários ou externos.

A criação automática de estatísticas é feita de forma sincronizada. Por isso, pode incorrer num desempenho de consulta ligeiramente degradado se as suas colunas estiverem a faltar estatísticas. O tempo para criar estatísticas para uma única coluna depende do tamanho da tabela.

Para evitar uma degradação mensurável do desempenho, deve certificar-se de que as estatísticas foram criadas primeiro executando a carga de trabalho de referência antes de perfilar o sistema.

> [!NOTE]
> A criação de estatísticas é registada em [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) num contexto de utilizador diferente.

Quando as estatísticas automáticas forem criadas, assumirão o formulário: _WA_Sys_ <id de coluna de 8 dígitos no Hex>_<id tabela de 8 dígitos em Hex>. Pode ver estatísticas já criadas executando o comando [DBCC SHOW_STATISTICS:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

O table_name é o nome da tabela que contém as estatísticas a exibir, que não podem ser uma tabela externa. O alvo é o nome do índice-alvo, estatísticas ou colunas para as quais apresentar informações estatísticas.

### <a name="update-statistics"></a>Atualizar estatísticas

Uma das melhores práticas é atualizar as estatísticas das colunas de datas todos os dias à medida que novas datas são adicionadas. Cada vez que novas linhas são carregadas no armazém de dados, novas datas de carga ou datas de transação são adicionadas. Estes acréscimos alteram a distribuição de dados e tornam as estatísticas desatualizadas.

As estatísticas sobre uma coluna de país ou região numa tabela de clientes podem nunca precisar de ser atualizadas porque a distribuição de valores geralmente não muda. Assumindo que a distribuição é constante entre os clientes, adicionar novas linhas à variação da tabela não vai alterar a distribuição de dados.

No entanto, quando o seu armazém de dados contém apenas um país ou região e você traz dados de um novo país ou região, então você precisa atualizar estatísticas sobre o país ou coluna da região.

Seguem-se recomendações que atualizam as estatísticas:

|||
|-|-|
| **Frequência das atualizações de estatísticas**  | Conservador: Diariamente </br> Depois de carregar ou transformar os seus dados |
| **Amostragem** |  Menos de mil milhões de linhas, use amostragem padrão (20 por cento). </br> Com mais de mil milhões de filas, use amostragem de 2%. |

### <a name="determine-last-statistics-update"></a>Determinar a última atualização estatística

Uma das primeiras perguntas a fazer quando se está a resolver problemas é: **"As estatísticas estão atualizadas?"**

Esta questão não é uma que possa ser respondida pela idade dos dados. Um objeto estatístico atualizado pode ser antigo se não houver nenhuma alteração material nos dados subjacentes. Quando o número de linhas mudou substancialmente, ou ocorre uma alteração material na distribuição de valores para uma coluna, *então* é hora de atualizar as estatísticas.

Não existe uma visão dinâmica de gestão disponível para determinar se os dados dentro da tabela mudaram desde a última vez que as estatísticas foram atualizadas. Conhecer a idade das suas estatísticas pode fornecer-lhe parte da imagem. 

Pode utilizar a seguinte consulta para determinar a última vez que as suas estatísticas foram atualizadas em cada tabela.

> [!NOTE]
> Se houver uma alteração material na distribuição de valores para uma coluna, deve atualizar as estatísticas independentemente da última vez que foram atualizadas.

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

**As colunas de data** num armazém de dados, por exemplo, geralmente precisam de atualizações estatísticas frequentes. Cada vez que novas linhas são carregadas no armazém de dados, novas datas de carga ou datas de transação são adicionadas. Estes acréscimos alteram a distribuição de dados e tornam as estatísticas desatualizadas.

As estatísticas de uma coluna de género numa tabela de clientes podem nunca precisar de ser atualizadas. Assumindo que a distribuição é constante entre os clientes, adicionar novas linhas à variação da tabela não vai alterar a distribuição de dados.

Mas, se o seu armazém de dados contém apenas um sexo e um novo requisito resulta em vários sexos, então precisa atualizar as estatísticas sobre a coluna de género. 

Para mais informações, reveja o artigo [Estatísticas.](/sql/relational-databases/statistics/statistics)

### <a name="implement-statistics-management"></a>Implementar gestão de estatísticas

Muitas vezes é uma boa ideia alargar o seu processo de carregamento de dados para garantir que as estatísticas são atualizadas no final da carga. A carga de dados é quando as tabelas mudam mais frequentemente o seu tamanho, distribuição de valores, ou ambos. Como tal, o processo de carga é um local lógico para implementar alguns processos de gestão.

São fornecidos os seguintes princípios orientadores para a atualização das suas estatísticas durante o processo de carga:

- Certifique-se de que cada tabela carregada tem pelo menos um objeto estatístico atualizado. Este processo atualiza as informações sobre o tamanho da tabela (contagem de filas e contagem de páginas) como parte da atualização das estatísticas.
- Foco nas colunas que participam em se junta, GRUPO BY, ORDER BY, e Cláusulas DISTINTAS.
- Considere atualizar colunas "chave ascendente" como datas de transação com mais frequência porque estes valores não serão incluídos no histograma estatístico.
- Considere atualizar as colunas de distribuição estática com menos frequência.
- Lembre-se, cada objeto estatístico é atualizado em sequência. Simplesmente implementar `UPDATE STATISTICS <TABLE_NAME>` nem sempre é ideal, especialmente para mesas largas com muitos objetos estatísticos.

Para mais informações, consulte [a Estimativa cardinalício.](/sql/relational-databases/performance/cardinality-estimation-sql-server)

### <a name="examples-create-statistics"></a>Exemplos: Criar estatísticas

Estes exemplos mostram como utilizar várias opções para criar estatísticas. As opções que utiliza para cada coluna dependem das características dos seus dados e da forma como a coluna será utilizada em consultas.

#### <a name="create-single-column-statistics-with-default-options"></a>Criar estatísticas de coluna única com opções predefinidos

Para criar estatísticas sobre uma coluna, forneça um nome para o objeto estatístico e o nome da coluna.
Esta sintaxe utiliza todas as opções padrão. Por padrão, a piscina de SQL dedicada mostra **20%** da tabela quando cria estatísticas.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando todas as linhas

A taxa de amostragem de 20% por defeito é suficiente para a maioria das situações. No entanto, pode ajustar a taxa de amostragem. Para provar a tabela completa, utilize esta sintaxe:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única especificando o tamanho da amostra

Outra opção que tem é especificar o tamanho da amostra como percentagem:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Criar estatísticas de coluna única em apenas algumas das linhas

Também pode criar estatísticas sobre uma parte das linhas da sua mesa, que é chamada de estatística filtrada.

Por exemplo, pode utilizar estatísticas filtradas quando planeia consultar uma divisória específica de uma grande mesa dividida. Ao criar estatísticas sobre apenas os valores de partição, a precisão das estatísticas melhorará. Também vai experimentar uma melhoria no desempenho da consulta.

Este exemplo cria estatísticas sobre uma gama de valores. Os valores podem ser facilmente definidos para corresponder à gama de valores numa divisória.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para que o otimizador de consulta considere a utilização de estatísticas filtradas quando escolhe o plano de consulta distribuído, a consulta deve caber dentro da definição do objeto estatístico. Utilizando o exemplo anterior, a cláusula WHERE da consulta tem de especificar os valores do col1 entre 2000101 e 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Criar estatísticas de coluna única com todas as opções

Também pode combinar as opções. O exemplo a seguir cria um objeto de estatísticas filtrado com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, consulte [ESTATÍSTICAS CREATE](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Criar estatísticas multi-colunas

Para criar um objeto de estatísticas multi-colunas, use os exemplos anteriores, mas especifique mais colunas.

> [!NOTE]
> O histograma, que é utilizado para estimar o número de linhas no resultado da consulta, só está disponível para a primeira coluna listada na definição de objeto estatístico.

Neste exemplo, o histograma está na *\_ categoria de produto.* As estatísticas transversais são calculadas na *\_ categoria do produto* e sub_category de *\_ produtos:*

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Uma vez que existe uma correlação entre *\_ a categoria do produto* e a *\_ \_ subcategoria do produto,* um objeto de estatísticas multi-colunas pode ser útil se estas colunas forem acedidas ao mesmo tempo.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Criar estatísticas sobre todas as colunas numa tabela

Uma forma de criar estatísticas é emitir comandos CREATE STATISTICS após a criação do quadro:

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Utilize um procedimento armazenado para criar estatísticas em todas as colunas numa base de dados

A piscina SQL não tem um procedimento armazenado do sistema equivalente a sp_create_stats no SQL Server. Este procedimento armazenado cria um único objeto de estatística de coluna em cada coluna da base de dados que ainda não tem estatísticas.

O exemplo a seguir irá ajudá-lo a começar com o design da sua base de dados. Sinta-se livre para adaptá-lo às suas necessidades:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

Para criar estatísticas sobre todas as colunas da tabela utilizando os predefinidos, execute o procedimento armazenado.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Para criar estatísticas sobre todas as colunas da tabela utilizando um fullscan, ligue para este procedimento:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Para criar estatísticas amostradas em todas as colunas da tabela, insira 3 e a amostra por cento. O procedimento abaixo usa uma taxa de amostra de 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Exemplos: Atualizar estatísticas

Para atualizar as estatísticas, pode:

- Atualize um objeto estatístico. Especifique o nome do objeto de estatística que pretende atualizar.
- Atualize todos os objetos estatísticos numa tabela. Especifique o nome da tabela em vez de um objeto estatístico específico.

#### <a name="update-one-specific-statistics-object"></a>Atualizar um objeto estatístico específico

Utilize a seguinte sintaxe para atualizar um objeto estatístico específico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos estatísticos específicos, pode minimizar o tempo e os recursos necessários para gerir as estatísticas. Esta ação requer algum pensamento para selecionar os melhores objetos estatísticos para atualizar.

#### <a name="update-all-statistics-on-a-table"></a>Atualizar todas as estatísticas sobre uma tabela

Um método simples para atualizar todos os objetos estatísticos sobre uma tabela é:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

A declaração de ESTATÍSTICAS DE ATUALIZAÇÃO é fácil de usar. Lembre-se apenas que atualiza *todas as* estatísticas em cima da mesa, o que leva a mais trabalho do que o necessário. 

Se o desempenho não é um problema, este método é a maneira mais fácil e completa de garantir que as estatísticas estão atualizadas.

> [!NOTE]
> Ao atualizar todas as estatísticas sobre uma tabela, a piscina sql dedicada faz uma digitalização para amostrar a tabela para cada objeto estatístico. Se o quadro for grande e tiver muitas colunas e muitas estatísticas, poderá ser mais eficiente atualizar as estatísticas individuais com base nas necessidades.

Para uma implementação de um `UPDATE STATISTICS` procedimento, consulte [tabelas temporárias.](develop-tables-temporary.md) O método de implementação é ligeiramente diferente do `CREATE STATISTICS` procedimento anterior, mas o resultado é o mesmo.
Para obter a sintaxe completa, consulte [as estatísticas de Atualização](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="statistics-metadata"></a>Metadados estatísticos

Existem várias visões e funções do sistema que pode usar para encontrar informações sobre estatísticas. Por exemplo, pode ver se um objeto de estatística pode estar desatualizado utilizando a função STATS_DATE(). STATS_DATE permite-lhe ver quando as estatísticas foram criadas ou atualizadas pela última vez.

#### <a name="catalog-views-for-statistics"></a>Vistas de catálogo para estatísticas

Estas opiniões do sistema fornecem informações sobre estatísticas:

| Vista de catálogo | Description |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada coluna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada objeto na base de dados. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada esquema na base de dados. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada objeto estatístico. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada coluna no objeto estatístico. Ligações de volta a sys.colunas. |
| [sys.mesas](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada mesa (inclui tabelas externas). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada tipo de dado. |

#### <a name="system-functions-for-statistics"></a>Funções do sistema para estatísticas

Estas funções do sistema são úteis para trabalhar com estatísticas:

| Função do sistema | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Data em que o objeto estatístico foi atualizado pela última vez. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Nível de resumo e informação detalhada sobre a distribuição de valores conforme entendido pelo objeto estatístico. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combine colunas e funções estatísticas numa só vista

Esta visão reúne colunas relacionadas com estatísticas e resultados da função STATS_DATE() em conjunto.

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() exemplos

O DBCC SHOW_STATISTICS() mostra os dados retidos num objeto estatístico. Estes dados vêm em três partes:

- Cabeçalho
- Vetor de densidade
- Histograma

O cabeçalho são os metadados sobre as estatísticas. O histograma exibe a distribuição de valores na primeira coluna-chave do objeto estatístico. 

O vetor de densidade mede a correlação entre colunas. O pool de SQL dedicado calcula as estimativas de cardinalidade com qualquer um dos dados no objeto estatístico.

#### <a name="show-header-density-and-histogram"></a>Mostrar cabeçalho, densidade e histograma

Este exemplo simples mostra as três partes de um objeto estatístico:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrar uma ou mais partes do DBCC SHOW_STATISTICS()

Se estiver apenas interessado em visualizar peças específicas, use a `WITH` cláusula e especifique quais as partes que deseja ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() diferenças

`DBCC SHOW_STATISTICS()` é mais rigorosamente implementado em pool SQL dedicado em comparação com SQL Server:

- As funcionalidades não documentadas não são suportadas.
- Não posso usá Stats_stream.
- Não se pode juntar resultados para subconjuntos específicos de dados estatísticos. Por exemplo, STAT_HEADER junte-se DENSITY_VETOR.
- NO_INFOMSGS não pode ser preparado para a supressão de mensagens.
- Os parênteses quadrados em torno das estatísticas não podem ser usados.
- Não se pode usar nomes de colunas para identificar objetos estatísticos.
- O erro personalizado 2767 não é suportado.


## <a name="statistics-in-serverless-sql-pool-preview"></a>Estatísticas na piscina SQL sem servidor (pré-visualização)

As estatísticas são criadas por coluna particular para determinado conjunto de dados (trajetória de armazenamento).

> [!NOTE]
> Não é possível criar estatísticas para colunas LOB.

### <a name="why-use-statistics"></a>Porquê usar estatísticas

Quanto mais sem servidor a piscina SQL (pré-visualização) souber dos seus dados, mais rápido poderá executar consultas contra os mesmos. Recolher estatísticas sobre os seus dados é uma das coisas mais importantes que pode fazer para otimizar as suas consultas. 

O otimizador de perguntas sql sem servidor é um otimizador baseado em custos. Compara o custo de vários planos de consulta, e depois escolhe o plano com o menor custo. Na maioria dos casos, escolhe o plano que executará o mais rápido. 

Por exemplo, se o optimizador estimar que a data em que a sua consulta está a ser filtrada irá retornar uma linha, escolherá um plano. Se estimar que a data selecionada devolverá 1 milhão de linhas, devolverá um plano diferente.

### <a name="automatic-creation-of-statistics"></a>Criação automática de estatísticas

A piscina SQL sem servidor analisa as consultas recebidas dos utilizadores por falta de estatísticas. Se faltam estatísticas, o otimizador de consultas cria estatísticas sobre colunas individuais na consulta predicado ou junta-se a condições para melhorar as estimativas de cardinalícia para o plano de consulta.

A declaração SELECT irá desencadear a criação automática de estatísticas.

> [!NOTE]
> A criação automática de estatísticas é ligada para ficheiros Parquet. Para os ficheiros CSV, é necessário criar estatísticas manualmente até que as estatísticas de ficheiros CSV suportem automaticamente.

A criação automática de estatísticas é feita de forma sincronizada para que possa incorrer num desempenho de consulta ligeiramente degradado se as suas colunas estiverem a faltar estatísticas. O tempo para criar estatísticas para uma única coluna depende do tamanho dos ficheiros visados.

### <a name="manual-creation-of-statistics"></a>Criação manual de estatísticas

A piscina SQL sem servidor permite criar estatísticas manualmente. Para ficheiros CSV, tem de criar estatísticas manualmente porque a criação automática de estatísticas não está ligada para ficheiros CSV. 

Consulte os seguintes exemplos para obter instruções sobre como criar estatísticas manualmente.

### <a name="update-statistics"></a>Atualizar estatísticas

Alterações nos dados em ficheiros, eliminação e adição de ficheiros resultam em alterações na distribuição de dados e estão desatualizadas. Nesse caso, as estatísticas têm de ser atualizadas.

O pool SQL sem servidor recria automaticamente as estatísticas se os dados forem alterados significativamente. Sempre que as estatísticas são criadas automaticamente, o estado atual do conjunto de dados também é guardado: caminhos de ficheiros, tamanhos, datas de última modificação.

Quando as estatísticas estiverem em eso, novas serão criadas. O algoritmo passa pelos dados e compara-os com o estado atual do conjunto de dados. Se o tamanho das alterações for maior do que o limiar específico, então as estatísticas antigas são eliminadas e serão recriadas ao longo do novo conjunto de dados.

As estatísticas manuais nunca são declaradas velhas.

> [!NOTE]
> A recriação automática de estatísticas é ligada para ficheiros Parquet. Para os ficheiros CSV, é necessário deixar cair e criar estatísticas manualmente até que as estatísticas de ficheiros CSV suportem automaticamente. Confira os exemplos abaixo sobre como deixar cair e criar estatísticas.

Uma das primeiras perguntas a fazer quando se está a resolver problemas é: **"As estatísticas estão atualizadas?"**

Quando o número de linhas mudou substancialmente, ou há uma mudança material na distribuição de valores para uma coluna, *então* é hora de atualizar as estatísticas.

> [!NOTE]
> Se houver uma alteração material na distribuição de valores para uma coluna, deve atualizar as estatísticas independentemente da última vez que foram atualizadas.

### <a name="implement-statistics-management"></a>Implementar gestão de estatísticas

Pode querer alargar o seu pipeline de dados para garantir que as estatísticas são atualizadas quando os dados são significativamente alterados através da adição, eliminação ou alteração de ficheiros.

Estão previstos os seguintes princípios orientadores para a atualização das suas estatísticas:

- Certifique-se de que o conjunto de dados tem pelo menos um objeto estatístico atualizado. Isto atualiza informações de tamanho (contagem de linha e contagem de páginas) como parte da atualização de estatísticas.
- Foco nas colunas que participam em ONDE, JOIN, GROUP BY, ORDER BY e CLÁUSULAS DISTINTAS.
- Atualize colunas "chave ascendente" como datas de transação com mais frequência porque estes valores não serão incluídos no histograma estatístico.
- Atualize as colunas de distribuição estática com menos frequência.

Para mais informações, consulte [a Estimativa cardinalício.](/sql/relational-databases/performance/cardinality-estimation-sql-server)

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Exemplos: Criar estatísticas para colunas no caminho OPENROWSET

Os exemplos que se seguem mostram-lhe como utilizar várias opções para criar estatísticas. As opções que utiliza para cada coluna dependem das características dos seus dados e da forma como a coluna será utilizada em consultas.

> [!NOTE]
> Só pode criar estatísticas de coluna única neste momento.
>
> São necessárias permissões a seguir para a execução de sp_create_openrowset_statistics e sp_drop_openrowset_statistics: ADMINISTRAR OPERAÇÕES A GRANEL ou ADMINISTRAR OPERAÇÕES A GRANEL.

É utilizado o seguinte procedimento armazenado para criar estatísticas:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argumentos: [ @stmt = ] N'statement_text' - Especifica uma declaração Transact-SQL que devolverá os valores da coluna a serem utilizados para as estatísticas. Pode utilizar o TABLESAMPLE para especificar amostras de dados a utilizar. Se o TABLESAMPLE não for especificado, o FULLSCAN será utilizado.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> A amostragem de CSV não funciona neste momento, apenas o FULLSCAN é suportado para o CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando todas as linhas

Para criar estatísticas sobre uma coluna, forneça uma consulta que devolva a coluna para a qual necessita estatísticas.

Por padrão, se não especificar o contrário, o pool SQL sem servidor utiliza 100% dos dados fornecidos no conjunto de dados quando cria estatísticas.

Por exemplo, criar estatísticas com opções predefinidas (FULLSCAN) para uma coluna de um ano do conjunto de dados com base no ficheiro population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única especificando o tamanho da amostra

Pode especificar o tamanho da amostra como percentagem:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Exemplos: Atualizar estatísticas

Para atualizar as estatísticas, é necessário deixar cair e criar estatísticas. É utilizado o seguinte procedimento armazenado para deixar cair as estatísticas:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> São necessárias permissões a seguir para a execução de sp_create_openrowset_statistics e sp_drop_openrowset_statistics: ADMINISTRAR OPERAÇÕES A GRANEL ou ADMINISTRAR OPERAÇÕES A GRANEL.

Argumentos: [ @stmt = ] N'statement_text' - Especifica a mesma declaração Transact-SQL utilizada quando as estatísticas foram criadas.

Para atualizar as estatísticas da coluna do ano no conjunto de dados, que se baseia no ficheiro population.csv, é necessário deixar cair e criar estatísticas:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Exemplos: Criar estatísticas para colunas de tabelas externas

Os exemplos que se seguem mostram-lhe como utilizar várias opções para criar estatísticas. As opções que utiliza para cada coluna dependem das características dos seus dados e da forma como a coluna será utilizada em consultas.

> [!NOTE]
> Só pode criar estatísticas de coluna única neste momento.

Para criar estatísticas sobre uma coluna, forneça um nome para o objeto estatístico e o nome da coluna.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumentos: external_table Especifica quadro externo que as estatísticas devem ser criadas.

Estatísticas fullscan compute digitalizando todas as linhas. FULLSCAN e SAMPLE 100% têm os mesmos resultados. FULLSCAN não pode ser usado com a opção SAMPLE.

O número de amostra POR CENTO Especifica a percentagem aproximada ou o número de linhas na tabela ou vista indexada para o otimizador de consulta a utilizar quando cria estatísticas. O número pode ser de 0 a 100.

A AMOSTRA não pode ser utilizada com a opção FULLSCAN.

> [!NOTE]
> A amostragem de CSV não funciona neste momento, apenas o FULLSCAN é suportado para o CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando todas as linhas

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única especificando o tamanho da amostra

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Exemplos: Atualizar estatísticas

Para atualizar as estatísticas, é necessário deixar cair e criar estatísticas. Primeiro, deixe cair as estatísticas:

```sql
DROP STATISTICS census_external_table.sState
```

E criar estatísticas:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>Metadados estatísticos

Existem várias visões e funções do sistema que pode usar para encontrar informações sobre estatísticas. Por exemplo, pode ver se um objeto de estatística pode estar desatualizado utilizando a função STATS_DATE(). STATS_DATE permite-lhe ver quando as estatísticas foram criadas ou atualizadas pela última vez.

> [!NOTE]
> Os metadados estatísticos estão disponíveis apenas para colunas de tabelas externas. Os metadados estatísticos não estão disponíveis para colunas OPENROWSET.

#### <a name="catalog-views-for-statistics"></a>Vistas de catálogo para estatísticas

Estas opiniões do sistema fornecem informações sobre estatísticas:

| Vista de catálogo                                                 | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Uma linha para cada coluna.                                     |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Uma linha para cada objeto na base de dados.                     |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Uma linha para cada esquema na base de dados.                     |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Uma linha para cada objeto estatístico.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Uma linha para cada coluna no objeto estatístico. Ligações de volta a sys.colunas. |
| [sys.mesas](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Uma linha para cada mesa (inclui tabelas externas).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Uma linha para cada tipo de dado.                                  |

#### <a name="system-functions-for-statistics"></a>Funções do sistema para estatísticas

Estas funções do sistema são úteis para trabalhar com estatísticas:

| Função do sistema                                              | Description                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Data em que o objeto estatístico foi atualizado pela última vez. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combine colunas e funções estatísticas numa só vista

Esta visão reúne colunas relacionadas com estatísticas e resultados da função STATS_DATE() em conjunto.

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>Passos seguintes

Para melhorar ainda mais o desempenho de consulta para piscinas SQL dedicadas, consulte [monitorar a sua carga de trabalho](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e as [melhores práticas para piscina SQL dedicada.](best-practices-sql-pool.md#maintain-statistics)

Para melhorar ainda mais o desempenho da consulta para o pool SQL sem servidor, consulte [as melhores práticas para piscina SQL sem servidor](best-practices-sql-on-demand.md)
