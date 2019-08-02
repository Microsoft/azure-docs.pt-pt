---
title: Usando a identidade para criar chaves substitutas-Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações e exemplos para usar a propriedade IDENTITY para criar chaves substitutas em tabelas no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4c65bf7cc8edfa246508bb22001aed40c34414f3
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515594"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Usando a identidade para criar chaves substitutas no Azure SQL Data Warehouse

Recomendações e exemplos para usar a propriedade IDENTITY para criar chaves substitutas em tabelas no Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>O que é uma chave substituta

Uma chave substituta em uma tabela é uma coluna com um identificador exclusivo para cada linha. A chave não é gerada a partir dos dados da tabela. Os modeladores de dados gostam de criar chaves substitutas em suas tabelas quando projetam modelos de data warehouse. Você pode usar a propriedade IDENTITY para atingir essa meta de forma simples e eficaz, sem afetar o desempenho da carga.  

## <a name="creating-a-table-with-an-identity-column"></a>Criando uma tabela com uma coluna de identidade

A propriedade IDENTITY é projetada para escalar horizontalmente entre todas as distribuições no data warehouse sem afetar o desempenho da carga. Portanto, a implementação da identidade é orientada para atingir essas metas.

Você pode definir uma tabela como tendo a propriedade IDENTITY quando criar a tabela pela primeira vez usando a sintaxe semelhante à seguinte instrução:

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

Em seguida, você `INSERT..SELECT` pode usar para popular a tabela.

Este restante desta seção destaca as nuances da implementação para ajudá-lo a entender isso de forma mais completa.  

### <a name="allocation-of-values"></a>Alocação de valores

A propriedade IDENTITY não garante a ordem na qual os valores substitutos são alocados, o que reflete o comportamento do SQL Server e do banco de dados SQL do Azure. No entanto, no Azure SQL Data Warehouse, a ausência de uma garantia é mais pronunciada.

O exemplo a seguir é uma ilustração:

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

No exemplo anterior, duas linhas descarregou na distribuição 1. A primeira linha tem o valor substituto de 1 na coluna `C1`e a segunda linha tem o valor substituto de 61. Esses dois valores foram gerados pela propriedade IDENTITY. No entanto, a alocação dos valores não é contígua. Este comportamento é propositado.

### <a name="skewed-data"></a>Dados distorcidos

O intervalo de valores para o tipo de dados é distribuído uniformemente entre as distribuições. Se uma tabela distribuída sofre de dados distorcidos, o intervalo de valores disponíveis para o tipo de dados pode ser esgotado prematuramente. Por exemplo, se todos os dados terminam em uma única distribuição, a tabela efetivamente tem acesso a apenas um sexagésimo dos valores do tipo de dados. Por esse motivo, a propriedade Identity é limitada apenas `INT` a `BIGINT` tipos de dados e.

### <a name="selectinto"></a>SELECIONE.. PORTA

Quando uma coluna de identidade existente é selecionada em uma nova tabela, a nova coluna herda a propriedade de identidade, a menos que uma das condições a seguir seja verdadeira:

- A instrução SELECT contém uma junção.
- Várias instruções SELECT são unidas por meio de UNION.
- A coluna de identidade é listada mais de uma vez na lista de seleção.
- A coluna de identidade faz parte de uma expressão.

Se qualquer uma dessas condições for verdadeira, a coluna será criada como NOT NULL em vez de herdar a propriedade IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE COMO SELECT

CREATE TABLE AS SELECT (CTAS) segue o mesmo comportamento de SQL Server que está documentado para SELECT.. Porta. No entanto, você não pode especificar uma propriedade de identidade na definição `CREATE TABLE` de coluna da parte da instrução. Você também não pode usar a função IDENTITY na `SELECT` parte do CTAS. Para preencher uma tabela, você precisa usar `CREATE TABLE` para definir a tabela seguida por `INSERT..SELECT` para preenchê-la.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Inserindo valores explicitamente em uma coluna de identidade

SQL data warehouse dá `SET IDENTITY_INSERT <your table> ON|OFF` suporte à sintaxe. Você pode usar essa sintaxe para inserir valores explicitamente na coluna de identidade.

Muitos modeladores de dados gostam de usar valores negativos predefinidos para determinadas linhas em suas dimensões. Um exemplo é a linha-1 ou "membro desconhecido".

O próximo script mostra como adicionar essa linha explicitamente usando SET IDENTITY_INSERT:

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

## <a name="loading-data"></a>A carregar dados

A presença da propriedade IDENTITY tem algumas implicações em seu código de carregamento de dados. Esta seção destaca alguns padrões básicos para carregar dados em tabelas usando a identidade.

Para carregar dados em uma tabela e gerar uma chave substituta usando IDENTITY, crie a tabela e, em seguida, use INSERT.. SELECIONAR ou inserir.. VALORES para executar a carga.

O exemplo a seguir realça o padrão básico:

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
> Não é possível usar `CREATE TABLE AS SELECT` atualmente ao carregar dados em uma tabela com uma coluna de identidade.
>

Para obter mais informações sobre como carregar dados, consulte [projetando, extração, carregamento e transformação (ELT) para](design-elt-data-loading.md) [as práticas recomendadas de carregamento](guidance-for-loading-data.md)e SQL data warehouse do Azure.

## <a name="system-views"></a>Vistas de sistema

Você pode usar a exibição de catálogo [Sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) para identificar uma coluna que tem a propriedade Identity.

Para ajudá-lo a entender melhor o esquema de banco de dados, este exemplo mostra como integrar sys. identity_column ' a outras exibições de catálogo do sistema:

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
- Quando a coluna também é a chave de distribuição
- Quando a tabela é uma tabela externa

As seguintes funções relacionadas não têm suporte no SQL Data Warehouse:

- [IDENTIDADE ()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Tarefas comuns

Esta seção fornece um exemplo de código que você pode usar para executar tarefas comuns ao trabalhar com colunas de identidade.

A coluna C1 é a identidade em todas as tarefas a seguir.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Localizar o valor mais alto alocado para uma tabela

Use a `MAX()` função para determinar o valor mais alto alocado para uma tabela distribuída:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Localizar a semente e o incremento para a propriedade IDENTITY

Você pode usar as exibições de catálogo para descobrir os valores de configuração de incremento de identidade e semente para uma tabela usando a seguinte consulta:

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
- [IDENTIDADE de CREATE TABLE (Transact-SQL) (Propriedade)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
