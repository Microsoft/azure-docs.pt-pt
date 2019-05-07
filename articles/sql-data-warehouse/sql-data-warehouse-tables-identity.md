---
title: Utilizar a identidade para criar chaves alternativas - Azure SQL Data Warehouse | Documentos da Microsoft
description: Recomendações e exemplos para utilizar a propriedade de identidade para criar chaves alternativas em tabelas no armazém de dados SQL do Azure.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: be7d1f7d04b759a5d60ba26d391d19d1bc57f325
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154303"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Utilizar a identidade para criar chaves alternativas no Azure SQL Data Warehouse

Recomendações e exemplos para utilizar a propriedade de identidade para criar chaves alternativas em tabelas no armazém de dados SQL do Azure.

## <a name="what-is-a-surrogate-key"></a>O que é uma chave de substituição

Uma chave de substituição numa tabela é uma coluna com um identificador exclusivo para cada linha. A chave não é gerada dos dados da tabela. Modeladores de dados, como criar chaves alternativas em suas tabelas ao criar modelos de armazém de dados. Pode usar a propriedade de identidade para atingir esse objetivo, simples e eficaz, sem afetar o desempenho de carga.  

## <a name="creating-a-table-with-an-identity-column"></a>Criando uma tabela com uma coluna de identidade

A propriedade de identidade foi concebida para aumentar horizontalmente em todas as distribuições no armazém de dados sem afetar o desempenho de carga. Por conseguinte, a implementação de identidade é orientada em relação a atingir essas metas.

Pode definir uma tabela como tendo a propriedade de identidade quando cria pela primeira vez a tabela utilizando a sintaxe semelhante para a instrução seguinte:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Em seguida, pode utilizar `INSERT..SELECT` para preencher a tabela.

Este resto desta secção destaca as nuances da implementação para o ajudar a compreendê-los mais completa.  

### <a name="allocation-of-values"></a>Alocação de valores

A propriedade de identidade não garante a ordem na qual os valores de substituição são alocados, que reflete o comportamento do SQL Server e base de dados do Azure SQL. No entanto, no Azure SQL Data Warehouse, a ausência de uma garantia é mais evidentes.

O exemplo seguinte é uma ilustração:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
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

No exemplo anterior, duas linhas acabou de chegar na distribuição de 1. A primeira linha com o valor de substituição de 1 coluna `C1`, e a segunda linha com o valor de substituição de 61. Esses valores foram gerados pela propriedade de identidade. No entanto, a alocação dos valores não é contígua. Este comportamento é propositado.

### <a name="skewed-data"></a>Dados distorcidos

O intervalo de valores para o tipo de dados são distribuídos uniformemente pelas distribuições. Se uma tabela distribuída sofre dados inclinadas nas quais, em seguida, o intervalo de valores disponíveis para o tipo de dados pode esgotar-se prematuramente. Por exemplo, se todos os dados acaba entrando numa única distribuição, em seguida, com eficiência a tabela tem acesso a apenas um sixtieth dos valores do tipo de dados. Por esse motivo, a propriedade de identidade é limitada a `INT` e `BIGINT` apenas tipos de dados.

### <a name="selectinto"></a>SELECT..INTO

Quando uma coluna de identidade existente está selecionada para uma nova tabela, a nova coluna herda a propriedade de identidade, a menos que uma das seguintes condições for verdadeira:

- A instrução SELECT contém uma associação.
- Múltiplas instruções SELECIONADAS estão associadas ao utilizar a União.
- A coluna de identidade é listada mais de uma vez na lista de SELEÇÃO.
- A coluna de identidade é parte de uma expressão.

Se qualquer uma dessas condições for verdadeira, a coluna é criada NOT NULL em vez de herdá-la a propriedade de identidade.

### <a name="create-table-as-select"></a>CRIAR TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) segue o mesmo comportamento de SQL Server que está documentado para SELECT.... EM. No entanto, não é possível especificar uma propriedade de identidade na definição de coluna a `CREATE TABLE` parte da instrução. Também não é possível utilizar a função de identidade no `SELECT` parte do CTAS. Para preencher uma tabela, precisa usar `CREATE TABLE` para definir a tabela seguida `INSERT..SELECT` para preenchê-lo.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicitamente a inserir valores numa coluna de identidade

SQL Data Warehouse suporta `SET IDENTITY_INSERT <your table> ON|OFF` sintaxe. Pode usar essa sintaxe explicitamente inserir valores na coluna de identidade.

Muitos modeladores de dados gostam de usar valores negativos predefinidos para determinadas linhas em suas dimensões. Um exemplo é a -1 ou de linha de "membro desconhecido".

O script seguinte mostra como adicionar explicitamente esta linha utilizando IDENTITY_INSERT definido:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Carregar dados

A presença da propriedade de identidade possui algumas implicações para o seu código de carregamento de dados. Esta secção destaca alguns padrões básicos para carregar dados para tabelas com identidade.

Para carregar dados para uma tabela e gerar uma chave de substituição com a identidade, criar a tabela e, em seguida, utilize INSERT.... SELECIONE ou INSIRA.... VALORES para executar a carga.

O exemplo a seguir destaca o padrão básico:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Não é possível usar `CREATE TABLE AS SELECT` atualmente quando o carregamento de dados para uma tabela com uma coluna de identidade.
>

Para obter mais informações sobre como carregar dados, consulte [conceber extrair, carregamento e transformação (ELT) do Azure SQL Data Warehouse](design-elt-data-loading.md) e [melhores práticas de carregamento](guidance-for-loading-data.md).

## <a name="system-views"></a>Vistas de sistema

Pode utilizar o [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) vista para identificar uma coluna que tem a propriedade de identidade do catálogo.

Para ajudar a compreender melhor o esquema de banco de dados, este exemplo mostra como integrar sys.identity_column' com outros modos de exibição de catálogo de sistema:

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

Não é possível utilizar a propriedade de identidade:

- Quando o tipo de dados de coluna não é INT ou BIGINT
- Quando a coluna também é a chave de distribuição
- Quando a tabela é uma tabela externa

As seguintes funções relacionadas não são suportadas no SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Tarefas comuns

Esta seção fornece alguns exemplos de código, que pode usar para executar tarefas comuns ao trabalhar com colunas de identidade.

C1 da coluna é a identidade em todas as tarefas seguintes.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Encontrar o valor mais alto de alocado para uma tabela

Utilize o `MAX()` função para determinar o valor mais alto atribuído a uma tabela distribuída:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Encontrar o seed e incrementação maiores para a propriedade de identidade

Pode usar as exibições de catálogo para detetar os valores de configuração de identidade incremento e semente, para uma tabela utilizando a seguinte consulta:

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

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da tabela](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [CRIAR a identidade de tabela (Transact-SQL) (propriedade)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
