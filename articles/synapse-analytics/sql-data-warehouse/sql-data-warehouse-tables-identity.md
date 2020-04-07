---
title: Usar a IDENTIDADE para criar chaves de substituição
description: Recomendações e exemplos para a utilização da propriedade IDENTITY para criar chaves de substituição em mesas em piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e681e8ad655c31d5078b56b8f1a49cfd7c664533
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742634"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Utilização da IDENTIDADE para criar chaves de substituição na piscina SYnapse SQL

Recomendações e exemplos para a utilização da propriedade IDENTITY para criar chaves de substituição em mesas em piscina Synapse SQL.

## <a name="what-is-a-surrogate-key"></a>O que é uma chave de substituição

Uma chave de substituição em uma mesa é uma coluna com um identificador único para cada linha. A chave não é gerada a partir dos dados da tabela. Os modeladores de dados gostam de criar chaves de substituição nas suas tabelas quando projetam modelos de armazém de dados. Você pode usar a propriedade IDENTITY para alcançar este objetivo de forma simples e eficaz sem afetar o desempenho da carga.  

## <a name="creating-a-table-with-an-identity-column"></a>Criar uma tabela com uma coluna IDENTITY

A propriedade IDENTITY foi projetada para escalar todas as distribuições na piscina SYnapse SQL sem afetar o desempenho da carga. Por isso, a implementação do IDENTIDADE está orientada para a consecução destes objetivos.

Pode definir uma tabela como tendo a propriedade IDENTITY quando criar a tabela pela primeira vez usando sintaxe semelhante à seguinte declaração:

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

Pode então `INSERT..SELECT` usar para povoar a mesa.

Esta restante secção destaca as nuances da implementação para ajudá-lo a compreendê-las mais plenamente.  

### <a name="allocation-of-values"></a>Atribuição de valores

A propriedade IDENTITY não garante a ordem em que os valores de substituição são atribuídos, o que reflete o comportamento do SQL Server e da Base de Dados Azure SQL. No entanto, na piscina SYnapse SQL, a ausência de uma garantia é mais acentuada.

O exemplo seguinte é uma ilustração:

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

No exemplo anterior, duas linhas aterraram na distribuição 1. A primeira linha tem o valor `C1`de substituição de 1 na coluna, e a segunda linha tem o valor de substituição de 61. Ambos os valores foram gerados pela propriedade IDENTIDADE. No entanto, a atribuição dos valores não é contígua. Este comportamento é propositado.

### <a name="skewed-data"></a>Dados distorcidos

A gama de valores para o tipo de dados está distribuída uniformemente pelas distribuições. Se uma tabela distribuída sofre de dados distorcidos, então o leque de valores disponíveis para o tipo de dados pode ser esgotado prematuramente. Por exemplo, se todos os dados acabarem numa única distribuição, então efetivamente a tabela tem acesso a apenas um sexto dos valores do tipo de dados. Por esta razão, a `INT` propriedade `BIGINT` IDENTITY está limitada e apenas aos tipos de dados.

### <a name="selectinto"></a>SELECIONE.. EM

Quando uma coluna IDENTITÁria existente é selecionada para uma nova tabela, a nova coluna herda a propriedade IDENTITY, a menos que uma das seguintes condições seja verdadeira:

- A declaração SELECT contém uma adesão.
- Várias declarações SELECT são unidas através da utilização da UNIÃO.
- A coluna IDENTITY está listada mais de uma vez na lista SELECT.
- A coluna IDENTIDADE faz parte de uma expressão.

Se alguma destas condições for verdadeira, a coluna é criada NOT NULL em vez de herdar a propriedade IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CRIAR TABELA COMO SELECT (CTAS) segue o mesmo comportamento do Servidor SQL que está documentado para SELECT.. PARA DENTRO. No entanto, não é possível especificar uma `CREATE TABLE` propriedade IDENTITY na definição de coluna da parte da declaração. Também não pode utilizar a função IDENTITY na `SELECT` parte do CTAS. Para povoar uma mesa, `CREATE TABLE` você precisa usar `INSERT..SELECT` para definir a mesa seguida para povoá-la.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Inserir explicitamente valores numa coluna IDENTITY

Piscina Synapse SQL `SET IDENTITY_INSERT <your table> ON|OFF` suporta sintaxe. Pode utilizar esta sintaxe para inserir explicitamente valores na coluna IDENTIDADE.

Muitos modeladores de dados gostam de usar valores negativos predefinidos para determinadas linhas nas suas dimensões. Um exemplo é a linha -1 ou "membro desconhecido".

O próximo guião mostra como adicionar explicitamente esta linha utilizando o SET IDENTITY_INSERT:

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

## <a name="loading-data"></a>Carregar dados

A presença da propriedade IDENTITY tem algumas implicações no seu código de carregamento de dados. Esta secção destaca alguns padrões básicos para carregar dados em tabelas utilizando a IDENTIDADE.

Para carregar os dados numa tabela e gerar uma chave de substituição utilizando a IDENTIDADE, crie a tabela e, em seguida, utilize o INSERT.. SELECIONE ou INSIRA.. VALORES para executar a carga.

O exemplo que se segue realça o padrão básico:

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
> Não é possível `CREATE TABLE AS SELECT` utilizar atualmente quando se carrega dados numa tabela com uma coluna IDENTITY.
>

Para obter mais informações sobre os dados de carregamento, consulte [O Extrato, Carga e Transformação (ELT) para piscina Synapse SQL](design-elt-data-loading.md) e [carregar as melhores práticas.](guidance-for-loading-data.md)

## <a name="system-views"></a>Vistas de sistema

Pode utilizar a vista de catálogo [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para identificar uma coluna que tenha a propriedade IDENTITY.

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

As seguintes funções relacionadas não são suportadas na piscina SYnapse SQL:

- [IDENTIDADE()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>Tarefas comuns

Esta secção fornece um código de amostra que pode utilizar para executar tarefas comuns quando trabalha com colunas IDENTITY.

A Coluna C1 é a IDENTIDADE em todas as seguintes tarefas.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Encontre o valor mais elevado atribuído para uma tabela

Utilize `MAX()` a função para determinar o valor mais elevado atribuído a um quadro distribuído:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Encontre a semente e incremento para a propriedade IDENTITY

Pode utilizar as vistas do catálogo para descobrir os valores de incremento de identidade e de configuração de sementes para uma tabela, utilizando a seguinte consulta:

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
