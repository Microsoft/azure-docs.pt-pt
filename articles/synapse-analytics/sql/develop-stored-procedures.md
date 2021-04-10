---
title: Utilizar procedimentos armazenados
description: Dicas para implementar procedimentos armazenados usando o Sinaapse SQL em Azure Synapse Analytics para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674156"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Procedimentos armazenados utilizando o SQL synapse em Azure Synapse Analytics

Os pools Sinaapse SQL provisões e sem servidor permitem-lhe colocar uma lógica complexa de processamento de dados em procedimentos armazenados pela SQL. Os procedimentos armazenados são uma ótima maneira de encapsular o seu código SQL e armazená-lo perto dos seus dados no armazém de dados. Os procedimentos armazenados ajudam os desenvolvedores a modular as suas soluções, encapsulando o código em unidades manejáveis e facilitando uma maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-los ainda mais flexíveis.
Neste artigo encontrará algumas dicas para implementar procedimentos armazenados na piscina Synapse SQL para o desenvolvimento de soluções.

## <a name="what-to-expect"></a>O que esperar

O Sinaapse SQL suporta muitas das funcionalidades T-SQL que são utilizadas no SQL Server. Mais importante ainda, existem funcionalidades específicas de escala que podes usar para maximizar o desempenho da tua solução. Neste artigo, você vai aprender sobre as funcionalidades que você pode colocar em procedimentos armazenados.

> [!NOTE]
> No corpo de procedimento pode utilizar apenas as funcionalidades suportadas na área de superfície Synapse SQL. [Reveja este artigo](overview-features.md) para identificar objetos, declaração que pode ser usada em procedimentos armazenados. Os exemplos nestes artigos utilizam funcionalidades genéricas que estão disponíveis tanto na área de superfície sem servidor como dedicada. Consulte limitações adicionais [nas piscinas Sinaapse SQL a provisionadas e sem servidor](#limitations) no final deste artigo.

Para manter a escala e desempenho da piscina SQL, existem também algumas funcionalidades e funcionalidades que têm diferenças comportamentais e outras que não são suportadas.

## <a name="stored-procedures-in-synapse-sql"></a>Procedimentos armazenados em Sinapse SQL

No exemplo seguinte, pode ver os procedimentos que deixam cair objetos externos se existirem na base de dados:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Estes procedimentos podem ser executados utilizando `EXEC` uma declaração onde pode especificar o nome e os parâmetros do procedimento:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

O Sinaapse SQL proporciona uma implementação simplificada e simplificada do procedimento armazenado. A maior diferença em relação ao SQL Server é que o procedimento armazenado não é código pré-compilado. Nos armazéns de dados, o tempo de compilação é pequeno em comparação com o tempo que leva para executar consultas contra grandes volumes de dados. É mais importante garantir que o código de procedimento armazenado está corretamente otimizado para grandes consultas. O objetivo é poupar horas, minutos e segundos, não milissegundos. Por conseguinte, é mais útil pensar nos procedimentos armazenados como recipientes para a lógica SQL.

Quando o Synapse SQL executa o seu procedimento armazenado, as declarações SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante este processo, cada declaração é convertida em consultas distribuídas. O código SQL que é executado contra os dados é diferente da consulta submetida.

## <a name="encapsulate-validation-rules"></a>Regras de validação encapsulada

Os procedimentos armazenados permitem localizar a lógica de validação num único módulo armazenado na base de dados SQL. No exemplo seguinte, pode ver como validar os valores dos parâmetros e alterar os seus valores predefinidos.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

A lógica do procedimento sql validará os parâmetros de entrada quando o procedimento é chamado.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Procedimentos armazenados de nidificação

Quando os procedimentos armazenados ligam para outros procedimentos armazenados, ou executam SQL dinâmico, então o procedimento interno armazenado ou invocação de código é dito estar aninhado.
Um exemplo de procedimento aninhado é mostrado no seguinte código:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Este procedimento aceita um parâmetro que representa algum nome e, em seguida, chama outros procedimentos para largar os objetos com este nome.
A piscina Sinaapse SQL suporta um máximo de oito níveis de nidificação. Esta capacidade é ligeiramente diferente do SQL Server. O nível do ninho no SQL Server é de 32.

A chamada de procedimento armazenado de nível superior equivale ao nível do ninho 1.

```sql
EXEC clean_up 'mytest'
```

Se o procedimento armazenado também fizer outra chamada DOEC, o nível do ninho aumenta para dois.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Se o segundo procedimento executar algum SQL dinâmico, o nível do ninho aumenta para três.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> O Sinaapse SQL não suporta atualmente [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true). Tens de seguir o nível do ninho. É pouco provável que ultrapasse o limite de oito ninhos, mas se o fizer, tem de reformular o seu código para encaixar nos níveis de nidificação dentro deste limite.

## <a name="insertexecute"></a>INSERT..EXEBONITO

A piscina Sinaapse SQL prevista não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma declaração INSERT. Há uma abordagem alternativa que podes usar. Por exemplo, consulte o artigo sobre [tabelas temporárias](develop-tables-temporary.md) para a piscina Sinaapse SQL.

## <a name="limitations"></a>Limitações

Existem alguns aspetos dos procedimentos armazenados transact-SQL que não são implementados em SQL synapse, tais como:

| Recurso/opção | Aprovisionado | Sem servidor |
| --- | --- |
| Procedimentos temporários armazenados | No | Yes |
| Procedimentos armazenados numerados | No | No |
| Procedimentos armazenados expandidos | No | No |
| Procedimentos armazenados do CLR | No | No |
| Opção de encriptação | No | Yes |
| Opção de replicação | No | No |
| Parâmetros de valor de tabela | No | No |
| Parâmetros apenas de leitura | No | No |
| Parâmetros padrão | No | Yes |
| Contextos de execução | No | No |
| Declaração de devolução | No | Yes |
| INSIRA EM .. EXEC | No | Sim |

## <a name="next-steps"></a>Próximos passos

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)
