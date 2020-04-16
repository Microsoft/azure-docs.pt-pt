---
title: Utilização de procedimentos armazenados
description: Dicas para implementar procedimentos armazenados no pool SQL Synapse (armazém de dados) para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a431df1ff4ef0984d1197933e7ca78979fa23089
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430984"
---
# <a name="using-stored-procedures-in-sql-pool"></a>Utilização de procedimentos armazenados na piscina SQL

Dicas para implementar procedimentos armazenados no pool SQL Synapse (armazém de dados) para o desenvolvimento de soluções.

## <a name="what-to-expect"></a>O que esperar

A piscina SQL suporta muitas das funcionalidades T-SQL que são usadas no Servidor SQL. Mais importante ainda, existem funcionalidades específicas de escala que pode usar para maximizar o desempenho da sua solução.

No entanto, para manter a escala e desempenho do pool SQL existem também algumas funcionalidades e funcionalidades que têm diferenças comportamentais e outras que não são suportadas.

## <a name="introducing-stored-procedures"></a>Introdução de procedimentos armazenados

Os procedimentos armazenados são uma ótima maneira de encapsular o seu código SQL; armazenando-os perto dos seus dados no armazém de dados. Os procedimentos armazenados ajudam os desenvolvedores a modular as suas soluções encapsulando o código em unidades manejáveis; facilitando uma maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-los ainda mais flexíveis.

O pool SQL proporciona uma implementação simplificada e simplificada do procedimento armazenado. A maior diferença em comparação com o SQL Server é que o procedimento armazenado não é um código pré-compilado. Nos armazéns de dados, o tempo de compilação é pequeno em comparação com o tempo que demora a executar consultas contra grandes volumes de dados. É mais importante garantir que o código de procedimento armazenado está corretamente otimizado para grandes consultas. O objetivo é poupar horas, minutos e segundos, não milissegundos. Por conseguinte, é mais útil pensar nos procedimentos armazenados como recipientes para a lógica SQL.

Quando a piscina SQL executa o seu procedimento armazenado, as declarações SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante este processo, cada declaração é convertida em consultas distribuídas. O código SQL que é executado contra os dados é diferente da consulta submetida.

## <a name="nesting-stored-procedures"></a>Procedimentos armazenados de nidificação

Quando os procedimentos armazenados, ligue para outros procedimentos armazenados, ou execute o SQL dinâmico, então o procedimento interno armazenado ou a invocação do código é dito estar aninhado.

A piscina SQL suporta um máximo de oito níveis de nidificação. Isto é ligeiramente diferente do Servidor SQL. O nível de ninho no SQL Server é 32.

A chamada de procedimento armazenada de nível superior equivale ao nível 1 do ninho.

```sql
EXEC prc_nesting
```

Se o procedimento armazenado também fizer outra chamada exec, o nível do ninho aumenta para dois.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Se o segundo procedimento executar algum SQL dinâmico, o nível do ninho aumenta para três.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> A piscina SQL não suporta atualmente [.@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Tens de seguir o nível do ninho. É pouco provável que exceda o limite de oito ninhos, mas se o fizer, precisa de reformular o seu código para se ajustar aos níveis de nidificação dentro deste limite.

## <a name="insertexecute"></a>INSERIR. EXECUTAR

A piscina SQL não permite consumir o conjunto de resultados de um procedimento armazenado com uma declaração INSERT. No entanto, existe uma abordagem alternativa que pode utilizar. Por exemplo, consulte o artigo sobre [mesas temporárias.](develop-tables-temporary.md)

## <a name="limitations"></a>Limitações

Existem alguns aspetos dos procedimentos armazenados transact-SQL que não são implementados em piscina SQL.

São:

* procedimentos temporários armazenados
* procedimentos armazenados numerados
* procedimentos armazenados alargados
* Procedimentos armazenados pela CLR
* opção de encriptação
* opção de replicação
* parâmetros de tabela
* parâmetros de leitura apenas
* parâmetros padrão
* contextos de execução
* declaração de retorno

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](develop-overview.md)
