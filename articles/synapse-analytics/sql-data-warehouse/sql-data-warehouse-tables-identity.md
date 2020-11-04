---
title: Usar o IDENTITY para criar chaves de substituição
description: Recomendações e exemplos para a utilização da propriedade IDENTITY para criar chaves de substituição em mesas em piscinas SQL dedicadas.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/20/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e81b3d7781f1c6f7bf5743a083e9640dd6c831
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323581"
---
# <a name="using-identity-to-create-surrogate-keys-using-dedicated-sql-pool-in-azuresynapse-analytics"></a>Utilização da IDENTIDADE para criar chaves de substituição utilizando piscina SQL dedicada em AzureSynapse Analytics

Neste artigo, você encontrará recomendações e exemplos para usar a propriedade IDENTITY para criar chaves de substituição em mesas em piscina de SQL dedicada.

## <a name="what-is-a-surrogate-key"></a>O que é uma chave de substituição

Uma chave de substituição em uma mesa é uma coluna com um identificador único para cada linha. A chave não é gerada a partir dos dados da tabela. Os modeladores de dados gostam de criar chaves de substituição nas suas tabelas quando desenham modelos de armazém de dados. Você pode usar a propriedade IDENTITY para alcançar este objetivo de forma simples e eficaz sem afetar o desempenho da carga.
> [!NOTE]
> No Azure Synapse Analytics, o valor IDENTITÁRIO aumenta por si só em cada distribuição e não se sobrepõe aos valores identitários noutras distribuições.  O valor IDENTITÁRIO em Synapse não é garantido ser único se o utilizador inserir explicitamente um valor duplicado com "SET IDENTITY_INSERT ON" ou reseeds IDENTITY. Para mais informações, consulte [CREATE TABLE (Transact-SQL) IDENTITY (Propriedade)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest). 


## <a name="creating-a-table-with-an-identity-column"></a>Criar uma tabela com uma coluna IDENTITÁRIA

A propriedade IDENTITY é projetada para escalar em todas as distribuições na piscina de SQL dedicada sem afetar o desempenho da carga. Por conseguinte, a implementação da IDENTIDADE orienta-se para a consecução destes objetivos.

Pode definir uma tabela como tendo a propriedade IDENTITY quando criar a tabela pela primeira vez, utilizando sintaxe semelhante à seguinte declaração:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Pode então usar `INSERT..SELECT` para povoar a mesa.

Este remanescente desta secção destaca as nuances da implementação para ajudá-lo a compreendê-las mais plenamente.  

### <a name="allocation-of-values"></a>Atribuição de valores

O imóvel IDENTITY não garante a ordem pela qual os valores de substituição são atribuídos devido à arquitetura distribuída do armazém de dados. A propriedade IDENTITY é projetada para escalar em todas as distribuições na piscina de SQL dedicada sem afetar o desempenho da carga. 

O exemplo a seguir é uma ilustração:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

No exemplo anterior, duas filas aterraram na distribuição 1. A primeira linha tem o valor de substituição de 1 em `C1` coluna, e a segunda linha tem o valor de substituição de 61. Ambos estes valores foram gerados pela propriedade IDENTITY. No entanto, a atribuição dos valores não é contígua. Este comportamento é propositado.

### <a name="skewed-data"></a>Dados distorcidos

A gama de valores para o tipo de dados é distribuída uniformemente por todas as distribuições. Se uma tabela distribuída sofre de dados distorcidos, então o intervalo de valores disponíveis para o tipo de dados pode ser esgotado prematuramente. Por exemplo, se todos os dados acabam numa única distribuição, então efetivamente a tabela tem acesso a apenas um sexto dos valores do tipo de dados. Por esta razão, a propriedade IDENTITY está limitada `INT` e `BIGINT` apenas os tipos de dados.

### <a name="selectinto"></a>SELECIONE.. EM

Quando uma coluna IDENTITÁRIA existente é selecionada para uma nova tabela, a nova coluna herda a propriedade IDENTITY, a menos que uma das seguintes condições seja verdadeira:

- A declaração SELECT contém uma junção.
- Várias declarações SELECT são acompanhadas através da UTILIZAÇÃO DA UNIÃO.
- A coluna IDENTITY está listada mais de uma vez na lista SELECT.
- A coluna IDENTITY faz parte de uma expressão.

Se alguma destas condições for verdadeira, a coluna é criada NÃO NULA em vez de herdar o imóvel IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) segue o mesmo comportamento do SQL Server que está documentado para SELECT.. PARA DENTRO. No entanto, não é possível especificar uma propriedade IDENTITÁRIA na definição da coluna `CREATE TABLE` da parte da declaração. Também não pode utilizar a função IDENTITY na `SELECT` parte do CTAS. Para povoar uma mesa, é necessário usar `CREATE TABLE` para definir a tabela seguida para a `INSERT..SELECT` povoar.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Inserir explicitamente valores numa coluna IDENTITÁRIA

Piscina SQL dedicada suporta `SET IDENTITY_INSERT <your table> ON|OFF` sintaxe. Pode utilizar esta sintaxe para inserir explicitamente valores na coluna IDENTITY.

Muitos modeladores de dados gostam de usar valores negativos predefinidos para certas linhas nas suas dimensões. Um exemplo é a linha -1 ou "membro desconhecido".

O próximo script mostra como adicionar explicitamente esta linha usando set IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>A carregar os dados

A presença da propriedade IDENTITY tem algumas implicações no seu código de carregamento de dados. Esta secção destaca alguns padrões básicos para carregar dados em tabelas utilizando o IDENTITY.

Para carregar dados numa tabela e gerar uma chave de substituição utilizando o IDENTITY, crie a tabela e, em seguida, use INSERT.. SELECIONE OU INSIRA.. VALORES para executar a carga.

O exemplo a seguir destaca o padrão básico:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Não é possível utilizar `CREATE TABLE AS SELECT` atualmente ao carregar dados numa tabela com uma coluna IDENTITY.
>

Para obter mais informações sobre os dados de carregamento, consulte [Designing Extract, Load e Transform (ELT) para piscina SQL dedicada](design-elt-data-loading.md) e [boas práticas de carregamento.](guidance-for-loading-data.md)

## <a name="system-views"></a>Vistas de sistema

Pode utilizar a [vista de](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) catálogo sys.identity_columns para identificar uma coluna que tenha a propriedade IDENTITY.

Para ajudá-lo a entender melhor o esquema da base de dados, este exemplo mostra como integrar sys.identity_column' com outras vistas do catálogo do sistema:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitações

A propriedade IDENTITY não pode ser usada:

- Quando o tipo de dados da coluna não é INT ou BIGINT
- Quando a coluna é também a chave de distribuição
- Quando a mesa é uma mesa externa

As seguintes funções relacionadas não são suportadas em piscina SQL dedicada:

- [IDENTIDADE()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>Tarefas comuns

Esta secção fornece algum código de amostra que pode utilizar para executar tarefas comuns quando trabalha com colunas IDENTITY.

A Coluna C1 é a IDENTIDADE em todas as seguintes tarefas.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Encontre o valor mais elevado atribuído para uma tabela

Utilize a `MAX()` função para determinar o valor mais elevado atribuído a uma tabela distribuída:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Encontre a semente e incremento para a propriedade IDENTITY

Pode utilizar as vistas do catálogo para descobrir os valores de configuração de incremento de identidade e sementes para uma tabela utilizando a seguinte consulta:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da tabela](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE (Transact-SQL) IDENTIDADE (Propriedade)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
