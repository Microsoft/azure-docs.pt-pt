---
title: Utilizar procedimentos armazenados
description: Dicas para implementar procedimentos armazenados no pool Synapse SQL (data warehouse) para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2b106a28514179d9be43b3c56ea8030eaf7ba13f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495000"
---
# <a name="use-stored-procedures-in-sql-pool"></a>Utilize procedimentos armazenados na piscina SQL

Dicas para implementar procedimentos armazenados no pool Synapse SQL (data warehouse) para o desenvolvimento de soluções.

## <a name="what-to-expect"></a>O que esperar

A piscina SQL suporta muitas das funcionalidades T-SQL que são usadas no SQL Server. Mais importante ainda, existem funcionalidades específicas de escala que podes usar para maximizar o desempenho da tua solução.

No entanto, para manter a escala e desempenho da piscina SQL existem também algumas funcionalidades e funcionalidades que têm diferenças comportamentais e outras que não são suportadas.

## <a name="stored-procedures-in-sql-pool"></a>Procedimentos armazenados na piscina SQL

Os procedimentos armazenados são uma ótima forma de encapsular o seu código SQL; armazenando-o perto dos seus dados no armazém de dados. Os procedimentos armazenados ajudam os desenvolvedores a modular as suas soluções, encapsulando o código em unidades manejáveis; facilitando uma maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-los ainda mais flexíveis.

O pool SQL proporciona uma implementação simplificada e simplificada do procedimento armazenado. A maior diferença em relação ao SQL Server é que o procedimento armazenado não é código pré-compilado. Nos armazéns de dados, o tempo de compilação é pequeno em comparação com o tempo que leva para executar consultas contra grandes volumes de dados. É mais importante garantir que o código de procedimento armazenado está corretamente otimizado para grandes consultas. O objetivo é poupar horas, minutos e segundos, não milissegundos. Por conseguinte, é mais útil pensar nos procedimentos armazenados como recipientes para a lógica SQL.

Quando a piscina SQL executa o seu procedimento armazenado, as declarações SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante este processo, cada declaração é convertida em consultas distribuídas. O código SQL que é executado contra os dados é diferente da consulta submetida.

## <a name="nesting-stored-procedures"></a>Procedimentos armazenados de nidificação

Quando os procedimentos armazenados ligam para outros procedimentos armazenados, ou executam SQL dinâmico, então o procedimento interno armazenado ou invocação de código é dito estar aninhado.

A piscina SQL suporta um máximo de oito níveis de nidificação. Isto é ligeiramente diferente do SQL Server. O nível do ninho no SQL Server é de 32.

A chamada de procedimento armazenado de nível superior equivale ao nível do ninho 1.

```sql
EXEC prc_nesting
```

Se o procedimento armazenado também fizer outra chamada DOEC, o nível do ninho aumenta para dois.

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
> A piscina SQL não suporta atualmente [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Tens de seguir o nível do ninho. É pouco provável que ultrapasse o limite de oito ninhos, mas se o fizer, tem de reformular o seu código para encaixar nos níveis de nidificação dentro deste limite.

## <a name="insertexecute"></a>INSERT..EXEBONITO

A piscina SQL não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma declaração INSERT. No entanto, há uma abordagem alternativa que pode usar. Por exemplo, consulte o artigo sobre [tabelas temporárias.](develop-tables-temporary.md)

## <a name="limitations"></a>Limitações

Existem alguns aspetos dos procedimentos armazenados transact-SQL que não são implementados na piscina SQL.

São:

* procedimentos temporários armazenados
* procedimentos armazenados numerados
* procedimentos armazenados alargados
* Procedimentos armazenados do CLR
* opção de encriptação
* opção de replicação
* parâmetros valorizados pela tabela
* parâmetros apenas de leitura
* parâmetros padrão
* contextos de execução
* declaração de retorno

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)
